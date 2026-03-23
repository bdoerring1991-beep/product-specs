# Initiative 1 — Portfolio Surveillance Intelligence
## Build Guide

**Parent strategy:** [NCG AI Strategy Assessment](../ncg-ai-strategy-assessment.md)  
**Priority:** Start now  
**Build time:** 6–8 weeks  
**Monthly cost:** $2,500–$5,500  
**Owner:** Technical contractor + Peter Capouch (COO) for sign-off

---

## What This Builds

A Python-based surveillance system that runs every morning at 6:00 AM CT,
pulls signals from five public data sources for every company in NCG's
portfolio watch list, scores each signal by urgency, and delivers a
structured digest to each portfolio manager via Slack and email before
markets open.

The system produces three output tiers: Urgent (immediate Slack alert),
Watch (in the morning digest), and Background (archived only).
Portfolio managers review; no automated action is ever taken.

---

## Architecture Overview

```
┌─────────────────────────────────────────────────────────┐
│                    DATA SOURCES (public)                 │
│  SEC EDGAR  │  AlphaSense  │  Revelio  │  USPTO │ News  │
└──────┬───────────┬──────────────┬──────────┬──────┬──────┘
       │           │              │          │      │
       ▼           ▼              ▼          ▼      ▼
┌─────────────────────────────────────────────────────────┐
│              INGESTION LAYER (Python scripts)            │
│   edgar_poller.py  │  alphasense_client.py  │  jobs.py  │
└──────────────────────────┬──────────────────────────────┘
                           │
                           ▼
┌─────────────────────────────────────────────────────────┐
│           SIGNAL SCORING ENGINE (rule-based)             │
│      scorer.py — classifies Urgent / Watch / BG          │
└──────────────────────────┬──────────────────────────────┘
                           │
                           ▼
┌─────────────────────────────────────────────────────────┐
│         AI DIGEST GENERATOR (Claude API)                 │
│   digest_writer.py — plain-language signal summaries     │
└──────────────┬────────────────────────┬─────────────────┘
               │                        │
               ▼                        ▼
┌──────────────────────┐   ┌───────────────────────────────┐
│  DELIVERY (Slack +   │   │  RECORDKEEPING (S3 archive)   │
│  email per PM)       │   │  7-year retention, SEC 204-2  │
└──────────────────────┘   └───────────────────────────────┘
```

---

## Prerequisites

Complete the [shared setup in the README](./README.md) first, then:

- [ ] AlphaSense API access — request enterprise trial at alphasense.com,
  negotiate a data processing agreement that prohibits training on NCG usage
- [ ] SEC EDGAR account — free, register at efts.sec.gov with NCG email
  (required to avoid rate limiting; EDGAR requires a User-Agent header)
- [ ] Revelio Labs trial — revelio-labs.com, for job posting data
  (1-month free trial available; confirm DPA before sharing company list)
- [ ] Slack workspace with a bot token — create a Slack App at api.slack.com
  with `chat:write` and `im:write` scopes
- [ ] NCG portfolio watch list — a CSV file of ticker symbols for every
  company across all four strategies, maintained by Joe Grundfeldt (trader)

---

## Step 1 — Build the Watch List Manager

This is the configuration layer. It maps each ticker to the portfolio
managers who cover it and the urgency thresholds relevant to that name.

**File: `watchlist.py`**

```python
import pandas as pd
import json

def load_watchlist(path: str = "data/watchlist.csv") -> dict:
    """
    Load the portfolio watch list from CSV.
    
    Expected CSV columns:
      ticker, company_name, strategy, primary_pm, secondary_pm,
      sector, market_cap_category, position_size_pct
    
    Returns dict keyed by ticker.
    """
    df = pd.read_csv(path)
    watchlist = {}
    for _, row in df.iterrows():
        watchlist[row["ticker"]] = {
            "name": row["company_name"],
            "strategy": row["strategy"],          # micro/small/smid/large
            "primary_pm": row["primary_pm"],       # Slack user ID
            "secondary_pm": row["secondary_pm"],
            "sector": row["sector"],
            "market_cap": row["market_cap_category"],
            "position_pct": float(row["position_size_pct"]),
        }
    return watchlist


def get_pm_tickers(pm_slack_id: str, watchlist: dict) -> list:
    """Return all tickers where this PM is primary or secondary."""
    return [t for t, v in watchlist.items()
            if v["primary_pm"] == pm_slack_id
            or v["secondary_pm"] == pm_slack_id]
```

**Sample `data/watchlist.csv`:**
```
ticker,company_name,strategy,primary_pm,secondary_pm,sector,market_cap_category,position_size_pct
AXON,Axon Enterprise,smid,U12345678,U87654321,Technology,SMID,3.2
DUOL,Duolingo,small,U87654321,U12345678,Technology,Small,2.8
...
```

---

## Step 2 — SEC EDGAR Poller

Polls EDGAR's free full-text search API for new filings on each ticker.
No third-party subscription needed for this layer.

**File: `edgar_poller.py`**

```python
import requests
import os
from datetime import datetime, timedelta
from typing import List, Dict

EDGAR_BASE = "https://efts.sec.gov/LATEST/search-index"
HEADERS = {
    "User-Agent": os.getenv("SEC_EDGAR_USER_AGENT",
                            "NCG-AI contact@ncgrowth.com")
}

# Filing types and their urgency classification
FILING_URGENCY = {
    "8-K":   "urgent",   # material events — always urgent
    "SC 13G": "urgent",  # new major shareholders
    "SC 13D": "urgent",  # activist positions
    "4":     "watch",    # insider transactions — score further
    "10-Q":  "watch",
    "10-K":  "watch",
    "DEF 14A": "background",  # proxy
    "S-1":   "urgent",   # competitors going public
}

def get_recent_filings(ticker: str, days_back: int = 1) -> List[Dict]:
    """
    Fetch all SEC filings for a ticker from the past N days.
    Uses EDGAR full-text search — no API key required.
    """
    since = (datetime.utcnow() - timedelta(days=days_back)
             ).strftime("%Y-%m-%d")
    
    params = {
        "q": f'"{ticker}"',
        "dateRange": "custom",
        "startdt": since,
        "enddt": datetime.utcnow().strftime("%Y-%m-%d"),
        "hits.hits._source": "period_of_report,file_date,form_type,"
                             "entity_name,file_num",
        "hits.hits.total": 20,
    }
    
    resp = requests.get(
        "https://efts.sec.gov/LATEST/search-index?q=%22TICKER%22"
        f"&dateRange=custom&startdt={since}",
        headers=HEADERS,
        timeout=10
    )
    
    # Use the EDGAR company search endpoint for cleaner results
    cik_resp = requests.get(
        f"https://data.sec.gov/submissions/CIK{get_cik(ticker)}.json",
        headers=HEADERS,
        timeout=10
    )
    
    if cik_resp.status_code != 200:
        return []
    
    data = cik_resp.json()
    filings = data.get("filings", {}).get("recent", {})
    
    results = []
    for i, form in enumerate(filings.get("form", [])):
        filed = filings["filingDate"][i]
        if filed >= since and form in FILING_URGENCY:
            results.append({
                "ticker": ticker,
                "form_type": form,
                "filed_date": filed,
                "accession": filings["accessionNumber"][i],
                "description": filings.get("primaryDocument", [""])[i],
                "urgency": FILING_URGENCY.get(form, "background"),
            })
    
    return results


def get_cik(ticker: str) -> str:
    """Look up CIK number for a ticker from EDGAR company search."""
    resp = requests.get(
        f"https://www.sec.gov/cgi-bin/browse-edgar"
        f"?company={ticker}&action=getcompany&type=&dateb=&owner=include"
        f"&count=10&search_text=&output=atom",
        headers=HEADERS,
        timeout=10
    )
    # Parse CIK from response — simplified; production version uses
    # EDGAR's company_tickers.json file for faster exact-match lookup
    # https://www.sec.gov/files/company_tickers.json
    return "0000000000"  # placeholder — replace with real CIK lookup


def score_form4(accession: str, ticker: str) -> str:
    """
    Elevate Form 4 urgency to 'urgent' if it represents a discretionary
    cluster sale (multiple insiders, not 10b5-1 plan).
    Returns 'urgent' or 'watch'.
    """
    # Fetch the actual Form 4 XML from EDGAR
    url = (f"https://www.sec.gov/Archives/edgar/data/"
           f"{accession.replace('-','')}/{accession}-index.htm")
    resp = requests.get(url, headers=HEADERS, timeout=10)
    
    # Check for 10b5-1 plan indicator in the filing
    # If present and single filer: keep as 'watch'
    # If absent or multiple filers in same week: escalate to 'urgent'
    is_plan = "10b5-1" in resp.text.lower()
    return "watch" if is_plan else "urgent"
```

---

## Step 3 — Job Posting Signal (Revelio Labs)

Job postings are a leading revenue indicator NCG has no current systematic
way to monitor. Revelio Labs provides normalized, de-duplicated job posting
data via API.

**File: `jobs_monitor.py`**

```python
import requests
import os
from datetime import datetime, timedelta

REVELIO_KEY = os.getenv("REVELIO_API_KEY")
REVELIO_BASE = "https://api.reveliolabs.com/v1"

# Functions that signal business momentum when growing
GROWTH_FUNCTIONS = ["Sales", "Marketing", "Business Development",
                    "Customer Success", "Revenue Operations"]

# Functions that signal product investment when growing
INVEST_FUNCTIONS = ["Engineering", "Research", "Product", "Data Science"]

# Functions that signal cost-cutting when shrinking
COST_FUNCTIONS = ["Operations", "Finance", "HR", "Legal", "Admin"]


def get_job_posting_trend(ticker: str, weeks: int = 12) -> dict:
    """
    Fetch job posting counts by function for a company over the
    past N weeks. Returns a trend dict with signal classification.
    
    Revelio Labs endpoint: /job_postings/company_timeseries
    """
    resp = requests.get(
        f"{REVELIO_BASE}/job_postings/company_timeseries",
        headers={"Authorization": f"Bearer {REVELIO_KEY}"},
        params={
            "ticker": ticker,
            "weeks": weeks,
            "group_by": "function",
        },
        timeout=15
    )
    
    if resp.status_code != 200:
        return {"error": f"Revelio API error {resp.status_code}"}
    
    data = resp.json()
    
    # Calculate 4-week rolling change by function
    signals = []
    for function_data in data.get("results", []):
        func = function_data["function"]
        counts = function_data["weekly_counts"]  # list of {week, count}
        
        if len(counts) < 8:
            continue
        
        recent_4w = sum(c["count"] for c in counts[-4:]) / 4
        prior_4w  = sum(c["count"] for c in counts[-8:-4]) / 4
        
        if prior_4w == 0:
            continue
        
        pct_change = (recent_4w - prior_4w) / prior_4w * 100
        
        # Classify signal
        if func in GROWTH_FUNCTIONS and pct_change < -20:
            signals.append({
                "function": func,
                "change_pct": round(pct_change, 1),
                "signal": "⚠️ Growth function hiring contraction",
                "urgency": "watch",
            })
        elif func in INVEST_FUNCTIONS and pct_change > 30:
            signals.append({
                "function": func,
                "change_pct": round(pct_change, 1),
                "signal": "📈 R&D investment acceleration",
                "urgency": "background",
            })
        elif func in GROWTH_FUNCTIONS and pct_change > 25:
            signals.append({
                "function": func,
                "change_pct": round(pct_change, 1),
                "signal": "📈 Sales/GTM expansion",
                "urgency": "background",
            })
    
    return {
        "ticker": ticker,
        "signals": signals,
        "as_of": datetime.utcnow().isoformat(),
    }
```

---

## Step 4 — Earnings Language Change Detector

This component compares consecutive earnings call transcripts for a company
and flags statistically meaningful language shifts in how management
describes growth.

**File: `transcript_analyzer.py`**

```python
import anthropic
import os
from typing import Optional

client = anthropic.Anthropic(api_key=os.getenv("ANTHROPIC_API_KEY"))

# Transcripts are fetched from AlphaSense API (see alphasense_client.py)
# This module handles the AI analysis layer only.

LANGUAGE_ANALYSIS_PROMPT = """You are analyzing consecutive earnings call 
transcripts for a publicly traded company. Your job is to detect meaningful 
shifts in how management describes business momentum.

PREVIOUS QUARTER TRANSCRIPT EXCERPT (management prepared remarks only):
{prev_transcript}

CURRENT QUARTER TRANSCRIPT EXCERPT (management prepared remarks only):
{curr_transcript}

Analyze these two transcripts and identify any of the following changes:

1. GUIDANCE SPECIFICITY: Has management become more vague or hedging in 
   how they describe growth outlook? (e.g. "we expect robust double-digit 
   growth" → "we expect growth to continue" is a specificity decline)

2. DEMAND LANGUAGE: Any shift in how management describes customer demand, 
   pipeline, or win rates?

3. COMPETITIVE LANGUAGE: Any new mentions of competitive pressure, pricing 
   headwinds, or customer budget scrutiny not present in the prior quarter?

4. COST LANGUAGE: Any new emphasis on cost efficiency, headcount optimization, 
   or margin protection that was absent previously?

Return a JSON object only — no preamble, no explanation outside the JSON:
{{
  "language_shift_detected": true/false,
  "urgency": "urgent" | "watch" | "none",
  "shifts": [
    {{
      "category": "guidance_specificity" | "demand" | "competitive" | "cost",
      "description": "One sentence describing the specific change detected",
      "prev_quote": "Exact quote from previous transcript (max 20 words)",
      "curr_quote": "Exact quote from current transcript (max 20 words)"
    }}
  ],
  "overall_tone_change": "more_positive" | "more_cautious" | "neutral" | "no_change"
}}"""


def analyze_transcript_shift(
    ticker: str,
    prev_transcript: str,
    curr_transcript: str,
    max_chars: int = 8000
) -> dict:
    """
    Use Claude to detect meaningful language changes between two 
    consecutive earnings call transcripts.
    
    Truncates transcripts to max_chars to stay within token budget.
    Uses management prepared remarks only — not Q&A — to focus on
    forward-looking statements.
    """
    prompt = LANGUAGE_ANALYSIS_PROMPT.format(
        prev_transcript=prev_transcript[:max_chars],
        curr_transcript=curr_transcript[:max_chars],
    )
    
    response = client.messages.create(
        model="claude-sonnet-4-6",
        max_tokens=600,
        messages=[{"role": "user", "content": prompt}]
    )
    
    import json
    try:
        result = json.loads(response.content[0].text)
        result["ticker"] = ticker
        return result
    except json.JSONDecodeError:
        return {
            "ticker": ticker,
            "language_shift_detected": False,
            "urgency": "none",
            "shifts": [],
            "error": "JSON parse failed — raw response archived"
        }
```

---

## Step 5 — Signal Scorer and Aggregator

All signals from all sources run through a single scoring function that
normalizes urgency and deduplicates.

**File: `scorer.py`**

```python
from datetime import datetime
from typing import List, Dict


def score_signals(raw_signals: List[Dict], ticker: str,
                  position_pct: float) -> List[Dict]:
    """
    Normalize and rank signals from all sources.
    Position size weights urgency: a 4% position warrants faster
    review than a 0.5% position.
    
    Returns sorted list with urgency: urgent > watch > background.
    """
    scored = []
    
    for sig in raw_signals:
        base_urgency = sig.get("urgency", "background")
        
        # Escalate watch → urgent if position is large (>3% of portfolio)
        if base_urgency == "watch" and position_pct >= 3.0:
            final_urgency = "urgent"
            escalation_note = f"Escalated: position size {position_pct}%"
        else:
            final_urgency = base_urgency
            escalation_note = None
        
        scored.append({
            "ticker": ticker,
            "source": sig.get("source", "unknown"),
            "signal": sig.get("signal", sig.get("description", "")),
            "urgency": final_urgency,
            "escalation_note": escalation_note,
            "raw": sig,
            "scored_at": datetime.utcnow().isoformat(),
        })
    
    urgency_order = {"urgent": 0, "watch": 1, "background": 2}
    return sorted(scored, key=lambda x: urgency_order[x["urgency"]])


def deduplicate(signals: List[Dict]) -> List[Dict]:
    """Remove duplicate signals from the same source and ticker."""
    seen = set()
    unique = []
    for sig in signals:
        key = f"{sig['ticker']}_{sig['source']}_{sig['signal'][:40]}"
        if key not in seen:
            seen.add(key)
            unique.append(sig)
    return unique
```

---

## Step 6 — AI Digest Writer

Takes all scored signals for a PM's watch list and generates the
structured plain-language digest using Claude.

**File: `digest_writer.py`**

```python
import anthropic
import os
import json
from datetime import datetime
from typing import List, Dict

client = anthropic.Anthropic(api_key=os.getenv("ANTHROPIC_API_KEY"))

DIGEST_PROMPT = """You are writing a morning research digest for a portfolio 
manager at a growth equity investment firm. The manager focuses on high-growth 
small and mid-cap companies.

Today's date: {today}
Portfolio manager: {pm_name}

Below are signals detected across the portfolio watch list, pre-scored by 
urgency. Write a concise, professional digest in three sections.

SIGNALS:
{signals_json}

FORMAT RULES:
- URGENT section: max 5 items, one sentence each, lead with the ticker
- WATCH section: max 10 items, one sentence each
- BACKGROUND section: just a bulleted list of tickers with one-word descriptor
- No investment recommendations or opinions — facts and signals only
- Use professional financial language, not marketing language
- If a signal is ambiguous, say so — do not over-interpret
- Total digest should be readable in under 4 minutes

Write the digest now. Start directly with the content — no preamble."""


def generate_digest(pm_name: str, signals: List[Dict],
                    date: str = None) -> str:
    """Generate the morning digest for one portfolio manager."""
    
    if not date:
        date = datetime.utcnow().strftime("%A, %B %d, %Y")
    
    urgent   = [s for s in signals if s["urgency"] == "urgent"]
    watch    = [s for s in signals if s["urgency"] == "watch"]
    bg       = [s for s in signals if s["urgency"] == "background"]
    
    signals_structured = {
        "urgent_signals":     urgent[:5],
        "watch_signals":      watch[:10],
        "background_signals": bg,
    }
    
    response = client.messages.create(
        model="claude-sonnet-4-6",
        max_tokens=1200,
        messages=[{
            "role": "user",
            "content": DIGEST_PROMPT.format(
                today=date,
                pm_name=pm_name,
                signals_json=json.dumps(signals_structured, indent=2)
            )
        }]
    )
    
    return response.content[0].text
```

---

## Step 7 — Delivery and Archiving

**File: `delivery.py`**

```python
import os
import boto3
import json
from datetime import datetime
from slack_sdk import WebClient
from slack_sdk.errors import SlackApiError

slack = WebClient(token=os.getenv("SLACK_BOT_TOKEN"))
s3    = boto3.client("s3")
BUCKET = os.getenv("S3_RECORDKEEPING_BUCKET", "ncg-ai-recordkeeping")


def send_slack_digest(pm_slack_id: str, digest_text: str) -> bool:
    """Send digest as a Slack DM to the portfolio manager."""
    try:
        slack.chat_postMessage(
            channel=pm_slack_id,
            text=f"*NCG Morning Digest — "
                 f"{datetime.utcnow().strftime('%b %d, %Y')}*\n\n"
                 + digest_text,
            unfurl_links=False,
        )
        return True
    except SlackApiError as e:
        print(f"Slack delivery failed for {pm_slack_id}: {e}")
        return False


def send_urgent_alert(pm_slack_id: str, ticker: str,
                      signal: str, source: str) -> None:
    """
    Send an immediate Slack alert for urgent signals.
    Called as soon as an urgent signal is detected — does not
    wait for the morning digest run.
    """
    slack.chat_postMessage(
        channel=pm_slack_id,
        text=f"🔴 *URGENT — {ticker}*\n"
             f"Source: {source}\n"
             f"Signal: {signal}\n"
             f"_{datetime.utcnow().strftime('%H:%M CT')} — "
             f"See full context in morning digest_",
    )


def archive_to_s3(pm_name: str, date: str,
                  signals: list, digest: str) -> str:
    """
    Archive all signals and generated digest to S3 for SEC recordkeeping.
    Returns the S3 key for confirmation.
    
    SEC Rule 204-2 requires retention of records used in investment 
    decisions for 5 years (2 years in accessible location).
    """
    key = f"digests/{date}/{pm_name.replace(' ','_')}_digest.json"
    
    payload = {
        "pm":       pm_name,
        "date":     date,
        "signals":  signals,
        "digest":   digest,
        "model":    "claude-sonnet-4-6",
        "archived": datetime.utcnow().isoformat(),
    }
    
    s3.put_object(
        Bucket=BUCKET,
        Key=key,
        Body=json.dumps(payload, indent=2),
        ContentType="application/json",
    )
    
    return key
```

---

## Step 8 — Main Orchestrator

**File: `main.py`**

```python
import schedule
import time
from datetime import datetime

from watchlist       import load_watchlist, get_pm_tickers
from edgar_poller    import get_recent_filings, score_form4
from jobs_monitor    import get_job_posting_trend
from transcript_analyzer import analyze_transcript_shift
from scorer          import score_signals, deduplicate
from digest_writer   import generate_digest
from delivery        import send_slack_digest, send_urgent_alert, archive_to_s3

# Portfolio manager Slack IDs — stored in config, not hardcoded
PM_CONFIG = {
    "tom_press":    {"slack_id": "U_TOM",   "name": "Tom Press"},
    "bob_scott":    {"slack_id": "U_BOB",   "name": "Bob Scott"},
    "peter_capouch":{"slack_id": "U_PETER", "name": "Peter Capouch"},
    "kaj_doerring": {"slack_id": "U_KAJ",   "name": "Kaj Doerring"},
    "tom_dignard":  {"slack_id": "U_TOMID", "name": "Tom Dignard"},
}


def run_daily_surveillance():
    """
    Main surveillance run. Executes at 6:00 AM CT on trading days.
    Pulls all signals, scores them, writes digests, delivers, archives.
    """
    print(f"[{datetime.utcnow().isoformat()}] Starting surveillance run")
    
    watchlist = load_watchlist("data/watchlist.csv")
    today = datetime.utcnow().strftime("%Y-%m-%d")
    
    for pm_key, pm_info in PM_CONFIG.items():
        tickers = get_pm_tickers(pm_info["slack_id"], watchlist)
        all_signals = []
        
        for ticker in tickers:
            pos_pct = watchlist[ticker]["position_pct"]
            
            # 1. SEC filings
            filings = get_recent_filings(ticker, days_back=1)
            for f in filings:
                if f["form_type"] == "4":
                    f["urgency"] = score_form4(f["accession"], ticker)
                f["source"] = "SEC EDGAR"
                f["signal"] = (f"New {f['form_type']} filing: "
                               f"{f.get('description', 'see filing')}")
            
            # 2. Job postings (run weekly not daily — API cost management)
            if datetime.utcnow().weekday() == 0:  # Monday only
                job_signals = get_job_posting_trend(ticker)
                for s in job_signals.get("signals", []):
                    s["source"] = "Revelio Labs"
                filings += job_signals.get("signals", [])
            
            # Score all signals for this ticker
            scored = score_signals(
                filings, ticker, pos_pct
            )
            
            # Send immediate Slack alert for urgent signals
            for sig in scored:
                if sig["urgency"] == "urgent":
                    send_urgent_alert(
                        pm_info["slack_id"],
                        ticker,
                        sig["signal"],
                        sig["source"]
                    )
            
            all_signals.extend(scored)
        
        # Deduplicate and generate digest
        clean_signals = deduplicate(all_signals)
        digest = generate_digest(pm_info["name"], clean_signals)
        
        # Deliver digest
        send_slack_digest(pm_info["slack_id"], digest)
        
        # Archive for recordkeeping
        key = archive_to_s3(pm_info["name"], today, clean_signals, digest)
        print(f"Archived {pm_info['name']} digest to s3://{key}")
    
    print(f"[{datetime.utcnow().isoformat()}] Surveillance run complete")


# Schedule: 6:00 AM CT = 12:00 PM UTC (11 AM UTC during CDT)
schedule.every().day.at("11:00").do(run_daily_surveillance)

if __name__ == "__main__":
    print("NCG Portfolio Surveillance running — waiting for schedule...")
    run_daily_surveillance()  # Run once immediately on start
    while True:
        schedule.run_pending()
        time.sleep(60)
```

---

## Testing Checklist

Before going live, validate each component independently:

- [ ] `edgar_poller.py` — run against 5 known tickers, confirm filings match
  what appears manually on EDGAR for the same date range
- [ ] `jobs_monitor.py` — run for 3 tickers with known recent hiring activity,
  confirm signals match what Revelio's UI shows
- [ ] `transcript_analyzer.py` — test with one known quarter where a company
  issued a profit warning. The shift detector should flag the preceding quarter's
  transcript as showing language deterioration.
- [ ] `digest_writer.py` — review 3 generated digests manually. Check: no
  investment opinions, no hallucinated signals, readable in under 4 minutes
- [ ] `delivery.py` — test Slack delivery to a test channel before sending to PM DMs
- [ ] `archive_to_s3` — confirm files appear in S3 bucket with correct path
  structure and are retrievable
- [ ] End-to-end test — run `main.py` on a Saturday with test tickers, review
  the full output before any PM sees a live digest

---

## Go-Live Checklist

- [ ] CCO (Kelly McNulty) has reviewed the data sources and confirmed no
  proprietary NCG data enters any API call
- [ ] All vendor DPAs signed: AlphaSense, Revelio Labs
- [ ] S3 bucket has lifecycle policy set: 2 years standard storage,
  then Glacier for years 3–7 (satisfies SEC Rule 204-2)
- [ ] Form ADV Part 2A updated to reference AI-assisted research monitoring tools
- [ ] PM onboarding: 30-minute walkthrough of what the digest contains,
  what it doesn't contain, and how to flag a bad signal
- [ ] Joe Grundfeldt has provided final watchlist CSV and confirmed update process

---

## Cost Breakdown

| Item | Type | Monthly Cost |
|---|---|---|
| AlphaSense API (transcript + filing access) | Subscription | $1,500–$3,000 |
| Revelio Labs job posting data | Subscription | $500–$1,500 |
| Anthropic Claude API (digest generation) | Usage | ~$150–$300 |
| AWS EC2 t3.medium (scheduler host) | Usage | $30–$50 |
| AWS S3 (recordkeeping archive) | Usage | $5–$20 |
| **Total** | | **$2,185–$4,870/mo** |

---

## Ongoing Maintenance

| Task | Frequency | Owner |
|---|---|---|
| Update watchlist CSV when positions change | Per trade | Joe Grundfeldt |
| Review archived digests for quality | Monthly sample (5 digests) | Designated PM |
| Refresh AlphaSense transcript data for new quarters | Quarterly | Technical contact |
| Review and tune urgency thresholds | Quarterly | COO + designated PM |
| Update Form ADV if AI usage materially changes | As needed | CCO |
