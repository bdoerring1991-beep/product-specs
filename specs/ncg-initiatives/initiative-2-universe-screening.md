# Initiative 2 — Universe Screening Acceleration
## Build Guide

**Parent strategy:** [NCG AI Strategy Assessment](../ncg-ai-strategy-assessment.md)  
**Priority:** Phase 2  
**Build time:** 8–10 weeks  
**Monthly cost:** $1,500–$3,500  
**Dependency:** None — but builds naturally on AlphaSense access from Initiative 1

---

## What This Builds

A Python pipeline that takes any ticker passing NCG's quantitative revenue
growth screen and generates a structured 2-page pre-diligence briefing in
under 3 minutes. The briefing compresses what would otherwise be 6–8 hours
of initial reading into a 45-minute PM review, enabling NCG to evaluate
2–3x more new ideas per quarter without adding headcount.

The briefing produces no investment opinions. It organizes public facts.
The PM's judgment — the mosaic — starts after reading it, not before.

---

## Architecture Overview

```
┌──────────────────────────────────────────────────────────┐
│              INPUT: ticker + screen_date                  │
└─────────────────────────┬────────────────────────────────┘
                          │
         ┌────────────────┼────────────────┐
         ▼                ▼                ▼
   SEC EDGAR          AlphaSense      Revelio Labs
   10-K / 10-Q        Transcripts     Job Postings
   fetcher            (last 4 qtrs)   (12-week trend)
         │                │                │
         └────────────────┼────────────────┘
                          ▼
              ┌─────────────────────┐
              │  DATA NORMALIZER    │
              │  normalizer.py      │
              └──────────┬──────────┘
                         │
                         ▼
              ┌─────────────────────┐
              │  AI BRIEFING ENGINE │
              │  briefing_writer.py │
              │  (Claude API)       │
              └──────────┬──────────┘
                         │
              ┌──────────┼──────────┐
              ▼          ▼          ▼
        Markdown PDF    Slack    S3 Archive
        briefing file   notify   (recordkeeping)
```

---

## Prerequisites

- [ ] AlphaSense API access (from Initiative 1, or standalone)
- [ ] SEC EDGAR access (from Initiative 1, or standalone — free)
- [ ] Revelio Labs API access (from Initiative 1, or standalone)
- [ ] Anthropic API key
- [ ] `weasyprint` or `reportlab` Python library for PDF generation (optional)

---

## Step 1 — Quantitative Screen Interface

NCG already runs a quantitative screen. This step captures the output
of that screen as a structured input to the briefing pipeline.

**File: `screen_intake.py`**

```python
import pandas as pd
from dataclasses import dataclass
from datetime import datetime


@dataclass
class ScreenedCompany:
    """
    Represents a company that passed NCG's quantitative revenue growth screen.
    This object is the input to the pre-diligence briefing pipeline.
    """
    ticker: str
    company_name: str
    market_cap_category: str     # micro / small / smid / large
    revenue_growth_1yr: float    # e.g. 0.42 for 42%
    revenue_growth_3yr: float    # 3-year CAGR
    screened_date: str           # YYYY-MM-DD
    sector: str
    screened_by: str             # PM name


def load_screen_results(filepath: str) -> list[ScreenedCompany]:
    """
    Load companies from the PM's export of their quantitative screen.
    
    Accepts a CSV with columns:
      ticker, company_name, market_cap_category, revenue_growth_1yr,
      revenue_growth_3yr, sector, screened_by
    """
    df = pd.read_csv(filepath)
    df["screened_date"] = datetime.utcnow().strftime("%Y-%m-%d")
    
    return [ScreenedCompany(**row) for _, row in df.iterrows()]
```

---

## Step 2 — Data Fetcher

Pulls all public data needed for the briefing in parallel to keep
total fetch time under 90 seconds per company.

**File: `data_fetcher.py`**

```python
import requests
import os
import concurrent.futures
from typing import Optional

ALPHASENSE_KEY = os.getenv("ALPHASENSE_API_KEY")
EDGAR_UA = os.getenv("SEC_EDGAR_USER_AGENT")


def fetch_10k_text(ticker: str) -> Optional[str]:
    """
    Fetch the most recent 10-K filing text from SEC EDGAR.
    Returns the Business section (Item 1) and Risk Factors (Item 1A).
    Truncates to 15,000 characters for token budget management.
    """
    # 1. Get CIK from EDGAR company_tickers.json (cached locally)
    import json
    with open("data/company_tickers.json") as f:
        tickers_map = json.load(f)
    
    cik = None
    for _, v in tickers_map.items():
        if v["ticker"] == ticker.upper():
            cik = str(v["cik_str"]).zfill(10)
            break
    
    if not cik:
        return None
    
    # 2. Get most recent 10-K accession number
    resp = requests.get(
        f"https://data.sec.gov/submissions/CIK{cik}.json",
        headers={"User-Agent": EDGAR_UA},
        timeout=10
    )
    data = resp.json()
    filings = data["filings"]["recent"]
    
    for i, form in enumerate(filings["form"]):
        if form == "10-K":
            accession = filings["accessionNumber"][i].replace("-", "")
            # Fetch the actual filing document
            doc_resp = requests.get(
                f"https://www.sec.gov/Archives/edgar/data/"
                f"{int(cik)}/{accession}/",
                headers={"User-Agent": EDGAR_UA},
                timeout=15
            )
            # In production, parse the filing index to find the
            # primary document and extract relevant sections.
            # Simplified here — production uses BeautifulSoup
            # to extract Item 1 and Item 1A text.
            return doc_resp.text[:15000]
    
    return None


def fetch_transcripts(ticker: str, quarters: int = 4) -> list[dict]:
    """
    Fetch last N earnings call transcripts from AlphaSense.
    Returns list of {quarter, year, text} dicts, management remarks only.
    """
    resp = requests.get(
        "https://api.alphasense.com/v1/transcripts",
        headers={"Authorization": f"Bearer {ALPHASENSE_KEY}"},
        params={
            "ticker": ticker,
            "type": "earnings_call",
            "limit": quarters,
            "section": "prepared_remarks",
        },
        timeout=15
    )
    
    if resp.status_code != 200:
        return []
    
    return resp.json().get("transcripts", [])


def fetch_guidance_history(ticker: str) -> list[dict]:
    """
    Fetch management guidance vs. actuals for the last 8 quarters.
    Source: AlphaSense estimates API.
    Returns list of {quarter, guidance_revenue, actual_revenue,
    beat_miss_pct} dicts.
    """
    resp = requests.get(
        "https://api.alphasense.com/v1/estimates/guidance_history",
        headers={"Authorization": f"Bearer {ALPHASENSE_KEY}"},
        params={"ticker": ticker, "metric": "revenue", "quarters": 8},
        timeout=10
    )
    return resp.json().get("history", []) if resp.status_code == 200 else []


def fetch_all_data(ticker: str) -> dict:
    """
    Fetch all data sources in parallel. Returns combined dict.
    Total time: ~60–90 seconds for a complete fetch.
    """
    with concurrent.futures.ThreadPoolExecutor(max_workers=4) as ex:
        f_10k         = ex.submit(fetch_10k_text,      ticker)
        f_transcripts = ex.submit(fetch_transcripts,   ticker, 4)
        f_guidance    = ex.submit(fetch_guidance_history, ticker)
    
    return {
        "ticker":      ticker,
        "sec_10k":     f_10k.result(),
        "transcripts": f_transcripts.result(),
        "guidance":    f_guidance.result(),
    }
```

---

## Step 3 — AI Briefing Writer

The core of the initiative. Takes all fetched data and generates
the structured 2-page pre-diligence briefing using Claude.

**File: `briefing_writer.py`**

```python
import anthropic
import os
import json
from datetime import datetime
from screen_intake import ScreenedCompany

client = anthropic.Anthropic(api_key=os.getenv("ANTHROPIC_API_KEY"))

BRIEFING_SYSTEM_PROMPT = """You are a research analyst preparing a 
pre-diligence briefing for a portfolio manager at a growth equity 
investment firm. The firm focuses exclusively on high-growth companies 
and uses a mosaic research approach — management conversations, industry 
contacts, and financial analysis combined.

Your job is to organize public information clearly and concisely.
You do not form investment opinions. You do not recommend buying or selling.
You surface facts, patterns, and open questions for the PM to pursue.

Write in professional financial language. Be specific. Cite the source of 
each claim (10-K, transcript, etc.). Flag uncertainty explicitly — 
never fill gaps with inference."""

BRIEFING_PROMPT = """Prepare a pre-diligence briefing for the following company.

COMPANY: {company_name} ({ticker})
MARKET CAP CATEGORY: {market_cap}
SECTOR: {sector}
REVENUE GROWTH (1yr): {rev_growth_1yr:.1%}
REVENUE GROWTH (3yr CAGR): {rev_growth_3yr:.1%}
SCREEN DATE: {screen_date}

SOURCE DATA:

--- 10-K BUSINESS DESCRIPTION (Item 1 excerpt) ---
{sec_10k}

--- RECENT EARNINGS CALL TRANSCRIPTS (last 4 quarters, prepared remarks) ---
{transcripts}

--- MANAGEMENT GUIDANCE VS. ACTUALS (last 8 quarters) ---
{guidance}

---

Generate a structured pre-diligence briefing with EXACTLY these six sections.
Each section must be substantive — not placeholder text. If data is 
insufficient for a section, say so explicitly.

## 1. Business Model Summary
How does this company make money? Primary revenue streams, approximate
revenue mix if disclosed, pricing model, and key unit economics mentioned
in filings or transcripts.

## 2. Growth Driver Analysis
What is actually driving the revenue growth that passed the screen?
Distinguish: volume growth vs. pricing vs. new products vs. geographic
expansion vs. market share gain. This distinction matters for sustainability.
Cite specific management statements or filing disclosures.

## 3. Competitive Position
Who are the primary named competitors? What does public evidence suggest
about competitive dynamics — pricing pressure, win rates, customer switching?
Note any management language about competitive intensity changing.

## 4. Management Guidance Track Record
Based on the last 8 quarters of guidance vs. actuals: does management
guide conservatively, accurately, or optimistically? Show the data.
A consistent beat pattern vs. a mixed pattern vs. a miss pattern
are very different inputs to the mosaic.

## 5. Key Risk Flags
Any of the following if present: customer concentration >20%,
related-party transactions, going concern notes, auditor changes,
significant insider selling clusters, pending material litigation,
unusual revenue recognition policies, or leverage concerns.
State explicitly if none are found.

## 6. Open Questions for Further Diligence
List 5–7 specific questions this briefing raises that cannot be answered
from public data alone — the questions the PM should pursue through
management conversations, industry contacts, or customer calls.
These should be specific to this company, not generic checklist items."""


def generate_briefing(company: ScreenedCompany, raw_data: dict) -> str:
    """
    Generate the pre-diligence briefing for one screened company.
    Returns markdown-formatted briefing text.
    """
    # Format transcripts for the prompt
    transcript_text = ""
    for t in raw_data.get("transcripts", []):
        transcript_text += (
            f"\n[Q{t.get('quarter')} {t.get('year')}]\n"
            f"{t.get('text', '')[:2000]}\n"
        )
    
    # Format guidance history
    guidance_text = ""
    for g in raw_data.get("guidance", []):
        beat_miss = g.get("beat_miss_pct", 0)
        symbol = "✓" if beat_miss >= 0 else "✗"
        guidance_text += (
            f"{symbol} {g['quarter']}: "
            f"Guided ${g.get('guidance_revenue','?')}M, "
            f"Actual ${g.get('actual_revenue','?')}M "
            f"({'+' if beat_miss >= 0 else ''}{beat_miss:.1f}%)\n"
        )
    
    prompt = BRIEFING_PROMPT.format(
        company_name=company.company_name,
        ticker=company.ticker,
        market_cap=company.market_cap_category,
        sector=company.sector,
        rev_growth_1yr=company.revenue_growth_1yr,
        rev_growth_3yr=company.revenue_growth_3yr,
        screen_date=company.screened_date,
        sec_10k=(raw_data.get("sec_10k") or "Not available")[:8000],
        transcripts=transcript_text[:6000] or "Not available",
        guidance=guidance_text or "Not available",
    )
    
    response = client.messages.create(
        model="claude-sonnet-4-6",
        max_tokens=2000,
        system=BRIEFING_SYSTEM_PROMPT,
        messages=[{"role": "user", "content": prompt}]
    )
    
    header = (
        f"# Pre-Diligence Briefing: {company.company_name} "
        f"({company.ticker})\n\n"
        f"**Generated:** {datetime.utcnow().strftime('%B %d, %Y at %H:%M UTC')}\n"
        f"**Screened by:** {company.screened_by}\n"
        f"**Market cap category:** {company.market_cap_category.title()}\n"
        f"**Revenue growth (1yr / 3yr CAGR):** "
        f"{company.revenue_growth_1yr:.1%} / "
        f"{company.revenue_growth_3yr:.1%}\n\n"
        f"---\n\n"
        f"> **Important:** This briefing organizes public information only. "
        f"It contains no investment recommendations or opinions. "
        f"All claims are sourced from SEC filings or earnings call transcripts "
        f"as noted. The portfolio manager's independent judgment governs "
        f"all investment decisions.\n\n"
        f"---\n\n"
    )
    
    return header + response.content[0].text
```

---

## Step 4 — Pipeline Orchestrator and Delivery

**File: `screening_pipeline.py`**

```python
import os
import boto3
from datetime import datetime
from slack_sdk import WebClient

from screen_intake   import load_screen_results
from data_fetcher    import fetch_all_data
from briefing_writer import generate_briefing

slack  = WebClient(token=os.getenv("SLACK_BOT_TOKEN"))
s3     = boto3.client("s3")
BUCKET = os.getenv("S3_RECORDKEEPING_BUCKET")


def run_screening_pipeline(screen_csv: str, output_dir: str = "briefings/"):
    """
    Process all companies in a screen results CSV.
    Generates briefings, saves as markdown files, notifies PM via Slack,
    and archives to S3.
    """
    companies = load_screen_results(screen_csv)
    
    print(f"Processing {len(companies)} screened companies...")
    
    for company in companies:
        print(f"  Fetching data for {company.ticker}...")
        raw_data = fetch_all_data(company.ticker)
        
        print(f"  Generating briefing for {company.ticker}...")
        briefing = generate_briefing(company, raw_data)
        
        # Save markdown file locally
        filename = (f"{output_dir}{company.ticker}_"
                    f"{company.screened_date}_briefing.md")
        os.makedirs(output_dir, exist_ok=True)
        with open(filename, "w") as f:
            f.write(briefing)
        
        # Archive to S3
        s3_key = (f"briefings/{company.screened_date}/"
                  f"{company.ticker}_briefing.md")
        s3.put_object(
            Bucket=BUCKET,
            Key=s3_key,
            Body=briefing.encode(),
            ContentType="text/markdown",
        )
        
        # Notify PM via Slack
        # Find PM slack ID from config
        pm_slack_id = get_pm_slack_id(company.screened_by)
        if pm_slack_id:
            slack.chat_postMessage(
                channel=pm_slack_id,
                text=(
                    f"📋 *Pre-diligence briefing ready: "
                    f"{company.company_name} ({company.ticker})*\n"
                    f"Revenue growth: {company.revenue_growth_1yr:.1%} (1yr)\n"
                    f"Sector: {company.sector}\n"
                    f"File: `{filename}`\n"
                    f"_AI-generated from public data — "
                    f"no investment opinion contained_"
                ),
            )
        
        print(f"  ✓ {company.ticker} briefing complete → {s3_key}")
    
    print(f"Pipeline complete. {len(companies)} briefings generated.")


def get_pm_slack_id(pm_name: str) -> str:
    """Map PM name to Slack user ID from config."""
    pm_map = {
        "Tom Press":    "U_TOM",
        "Bob Scott":    "U_BOB",
        "Peter Capouch":"U_PETER",
        "Kaj Doerring": "U_KAJ",
        "Tom Dignard":  "U_TOMID",
    }
    return pm_map.get(pm_name, "")


if __name__ == "__main__":
    import sys
    csv_path = sys.argv[1] if len(sys.argv) > 1 else "data/screen_results.csv"
    run_screening_pipeline(csv_path)
```

---

## Testing Checklist

- [ ] Run pipeline on 3 historical tickers where NCG made an investment
  and eventually sold. Do the briefings surface the key signals that
  informed those decisions? Ask one PM to validate.
- [ ] Run on 2 tickers where NCG looked but passed. Does Section 5
  (Risk Flags) surface the reasons they passed?
- [ ] Validate guidance track record section against a manually constructed
  spreadsheet for one ticker — numbers must match
- [ ] Confirm no investment language appears in briefings
  (search generated text for "buy", "sell", "recommend", "attractive")
- [ ] Confirm S3 archiving works and files are retrievable by date range
- [ ] Time the full pipeline: target under 3 minutes per company

---

## Go-Live Checklist

- [ ] PM team agrees on which sections of the briefing template to keep vs.
  modify based on the test run feedback
- [ ] Briefing template reviewed by CCO — confirm no language that could
  constitute investment advice under the Advisers Act
- [ ] Standard operating procedure documented: when does a PM request a briefing?
  (e.g. any company passing the quantitative screen gets a briefing automatically)
- [ ] File naming convention agreed with Joe Grundfeldt so briefings integrate
  with existing research file organization

---

## Cost Breakdown

| Item | Type | Monthly Cost |
|---|---|---|
| AlphaSense API (transcripts + estimates) | Subscription | $1,500–$3,000 |
| Anthropic Claude API (~50 briefings/mo × ~$0.05 each) | Usage | $50–$150 |
| AWS S3 storage | Usage | $5–$10 |
| **Total** | | **$1,555–$3,160/mo** |

---

## Ongoing Maintenance

| Task | Frequency | Owner |
|---|---|---|
| Review 3 briefings per batch for quality | Per batch | Requesting PM |
| Update briefing template sections | Quarterly | COO + one PM |
| Confirm AlphaSense transcript coverage for new tickers | As needed | Technical contact |
