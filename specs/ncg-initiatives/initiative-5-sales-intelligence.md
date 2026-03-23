# Initiative 5 — Institutional Sales Intelligence Dashboard
## Build Guide

**Parent strategy:** [NCG AI Strategy Assessment](../ncg-ai-strategy-assessment.md)  
**Priority:** Phase 2  
**Build time:** 8–10 weeks  
**Monthly cost:** $2,500–$5,000  
**Primary users:** John Solecki (institutional sales), Bob Scott (CEO)

---

## What This Builds

A sales intelligence system purpose-built for institutional asset management —
the one domain where standard CRMs like Salesforce and HubSpot fall short
because they don't natively handle 13F filing data, mandate cycle timing,
performance-sensitivity modeling, or consultant database monitoring.

The system gives John Solecki three things he currently has to reconstruct
manually before each sales call: who's ready to hear from NCG, which existing
clients need proactive attention, and which new institutions are entering NCG's
target allocation category based on their most recent 13F filing.

---

## Architecture Overview

```
┌───────────────────────────────────────────────────────────┐
│                   DATA SOURCES                             │
│  Backstop CRM  │  SEC EDGAR 13F  │  eVestment  │  Email  │
└────────────────────────┬──────────────────────────────────┘
                         │
                         ▼
┌───────────────────────────────────────────────────────────┐
│              INGESTION PIPELINES                           │
│  backstop_sync.py  │  thirteenf_monitor.py  │  email.py  │
└────────────────────────┬──────────────────────────────────┘
                         │
              ┌──────────┼────────────┐
              ▼          ▼            ▼
     ┌──────────────┐ ┌────────────┐ ┌──────────────────┐
     │  ENGAGEMENT  │ │  RETENTION │ │  PROSPECT FINDER │
     │  SCORER      │ │  SCORER    │ │  (13F Monitor)   │
     └──────┬───────┘ └─────┬──────┘ └────────┬─────────┘
            │               │                 │
            └───────────────┼─────────────────┘
                            ▼
              ┌─────────────────────────┐
              │  AI SUMMARY LAYER       │
              │  (Claude — daily brief) │
              └──────────┬──────────────┘
                         │
              ┌──────────┼──────────┐
              ▼          ▼          ▼
        Slack daily  Dashboard   S3 Archive
        brief        (web UI)
```

---

## Prerequisites

- [ ] Backstop Solutions account — request trial at backstopsolutions.com
  (purpose-built CRM for asset managers; native 13F and performance data integration)
- [ ] eVestment database access (optional but valuable for consultant tracking)
- [ ] Google Workspace or Microsoft 365 API access for email engagement tracking
- [ ] SEC EDGAR access (free — already set up from Initiative 1)
- [ ] Anthropic API key

---

## Step 1 — CRM Data Model

Backstop provides the CRM foundation. This step defines the custom fields
NCG needs beyond Backstop's defaults to power the AI layer.

**Custom fields to add in Backstop:**

```
Contact / Institution record custom fields:
  ncg_engagement_score        (Number 0–100, AI-calculated)
  ncg_retention_risk_score    (Number 0–100, AI-calculated)
  last_performance_review_date (Date)
  performance_sensitivity      (Picklist: high / medium / low)
  mandate_review_cycle_month   (Number 1–12, estimated)
  thirteenf_last_parsed        (Date)
  small_cap_growth_allocation_pct (Number — from 13F)
  current_ncg_competitors      (Multi-select — managers they currently use)
  prospect_source              (Picklist: 13F monitor / referral / event / inbound)
```

---

## Step 2 — 13F Monitor (New Prospect Finder)

This is the highest-leverage component for new business development. It
automatically identifies institutions increasing allocations to strategies
that compete with NCG — meaning they are active buyers in NCG's market.

**File: `thirteenf_monitor.py`**

```python
import requests
import json
import os
from datetime import datetime, timedelta
from typing import List, Dict

EDGAR_UA = os.getenv("SEC_EDGAR_USER_AGENT")

# Known small/mid cap growth equity managers NCG competes with
# Used to identify institutions that already allocate to NCG's category
COMPETITOR_MANAGERS = [
    "Brown Advisory",
    "Champlain Investment Partners",
    "Conestoga Capital Advisors",
    "Geneva Capital Management",
    "Hood River Capital Management",
    "Silvercrest Asset Management",
    "TimesSquare Capital Management",
    "Wasatch Advisors",
    "William Blair",
]

# CIK numbers for competitor managers (from EDGAR company search)
# In production, build this from EDGAR's investment adviser search
COMPETITOR_CIKS = {
    "Brown Advisory": "0001614678",
    # ... add others from EDGAR search
}


def get_13f_filers_with_competitor_holdings() -> List[Dict]:
    """
    Find institutions that filed 13Fs showing positions in
    known small/mid cap growth managers (NCG competitors).
    
    These institutions are warm prospects — they actively allocate
    to NCG's strategy category.
    
    NOTE: This is a simplified approach. Production version uses
    EDGAR's full-text 13F search or a third-party 13F data service
    (e.g., Whale Wisdom, WhaleRock, or S&P Global's 13F database)
    for faster and more complete coverage.
    """
    prospects = []
    
    for manager_name, manager_cik in COMPETITOR_CIKS.items():
        # Find all 13F filers who hold this manager's mutual funds
        # (13F shows institutional holdings of mutual fund shares)
        resp = requests.get(
            f"https://efts.sec.gov/LATEST/search-index"
            f"?q=%22{manager_name.replace(' ', '+')}%22"
            f"&dateRange=custom"
            f"&startdt={(datetime.utcnow() - timedelta(days=120)).strftime('%Y-%m-%d')}"
            f"&forms=13F-HR",
            headers={"User-Agent": EDGAR_UA},
            timeout=15
        )
        
        if resp.status_code != 200:
            continue
        
        hits = resp.json().get("hits", {}).get("hits", [])
        
        for hit in hits[:20]:  # Top 20 filers per manager
            filer = hit.get("_source", {})
            
            # Exclude existing NCG clients (check against Backstop)
            institution_name = filer.get("entity_name", "")
            if not institution_name or is_existing_client(institution_name):
                continue
            
            prospects.append({
                "institution_name": institution_name,
                "cik":              filer.get("file_num", ""),
                "competitor_held":  manager_name,
                "filing_date":      filer.get("file_date", ""),
                "source":           "13F monitor",
                "detected_date":    datetime.utcnow().isoformat(),
            })
    
    return deduplicate_prospects(prospects)


def is_existing_client(institution_name: str) -> bool:
    """Check Backstop API to see if institution is an existing client."""
    # In production: query Backstop's REST API
    # Simplified: check a local cache file updated nightly from Backstop
    try:
        with open("data/existing_clients.json") as f:
            clients = json.load(f)
        return institution_name.lower() in [c.lower() for c in clients]
    except FileNotFoundError:
        return False


def deduplicate_prospects(prospects: List[Dict]) -> List[Dict]:
    """Remove duplicate institutions, keeping the most recent detection."""
    seen = {}
    for p in prospects:
        name = p["institution_name"]
        if name not in seen:
            seen[name] = p
    return list(seen.values())


def run_quarterly_13f_scan() -> List[Dict]:
    """
    Full quarterly scan. Run after 13F filing deadline each quarter
    (mid-February, mid-May, mid-August, mid-November).
    Returns new prospects not yet in Backstop.
    """
    print(f"Running 13F prospect scan: {datetime.utcnow().isoformat()}")
    new_prospects = get_13f_filers_with_competitor_holdings()
    
    # Save to Backstop via API (or export CSV for manual import)
    if new_prospects:
        output_path = (f"data/new_prospects_"
                       f"{datetime.utcnow().strftime('%Y%m%d')}.json")
        with open(output_path, "w") as f:
            json.dump(new_prospects, f, indent=2)
        print(f"Found {len(new_prospects)} new prospects → {output_path}")
    
    return new_prospects
```

---

## Step 3 — Engagement Scorer

Tracks how engaged each prospect and client is based on observable
signals: email opens, document downloads, meeting recency, and
responsiveness to outreach.

**File: `engagement_scorer.py`**

```python
from datetime import datetime, timedelta
from typing import Dict, Optional


def score_engagement(contact: Dict) -> int:
    """
    Calculate engagement score (0–100) for a prospect or client.
    
    contact dict (from Backstop API):
    {
      "last_meeting_date": "2025-12-15",
      "last_email_open_date": "2026-01-20",
      "last_document_download_date": "2026-02-01",
      "total_meetings_12mo": 3,
      "response_rate_pct": 75,  # % of outreach emails receiving a reply
      "last_outreach_date": "2026-02-15",
      "last_response_date": "2026-02-16",
    }
    """
    score = 0
    today = datetime.utcnow()
    
    def days_since(date_str: Optional[str]) -> int:
        if not date_str:
            return 999
        try:
            d = datetime.strptime(date_str, "%Y-%m-%d")
            return (today - d).days
        except ValueError:
            return 999
    
    # Meeting recency (max 30 points)
    mtg_days = days_since(contact.get("last_meeting_date"))
    if mtg_days < 30:    score += 30
    elif mtg_days < 90:  score += 20
    elif mtg_days < 180: score += 10
    elif mtg_days < 365: score += 5
    
    # Email engagement (max 20 points)
    email_days = days_since(contact.get("last_email_open_date"))
    if email_days < 14:  score += 20
    elif email_days < 30: score += 12
    elif email_days < 60: score += 6
    
    # Document downloads (max 20 points)
    doc_days = days_since(contact.get("last_document_download_date"))
    if doc_days < 30:    score += 20
    elif doc_days < 90:  score += 12
    elif doc_days < 180: score += 6
    
    # Meeting frequency — 12-month total (max 20 points)
    mtg_count = contact.get("total_meetings_12mo", 0)
    if mtg_count >= 4:  score += 20
    elif mtg_count >= 2: score += 12
    elif mtg_count >= 1: score += 6
    
    # Response rate (max 10 points)
    response_rate = contact.get("response_rate_pct", 0)
    if response_rate >= 70:  score += 10
    elif response_rate >= 40: score += 6
    elif response_rate >= 20: score += 3
    
    return min(score, 100)


def flag_gone_cold(contact: Dict, score: int) -> Optional[str]:
    """
    Return an alert message if a previously warm prospect has gone cold.
    'Gone cold' = score dropped below 40 AND had meeting in last 12 months.
    """
    had_recent_meeting = contact.get("total_meetings_12mo", 0) >= 1
    
    if score < 40 and had_recent_meeting:
        days = (datetime.utcnow()
                - datetime.strptime(
                    contact.get("last_meeting_date", "2000-01-01"),
                    "%Y-%m-%d")).days
        return (
            f"Engagement dropped — last meeting {days} days ago, "
            f"no recent email/document activity. "
            f"Recommend re-engagement outreach."
        )
    return None
```

---

## Step 4 — Retention Risk Scorer

Identifies existing clients who may be considering redemptions based on
performance sensitivity and recent engagement patterns.

**File: `retention_scorer.py`**

```python
from datetime import datetime
from typing import Dict, Optional


def score_retention_risk(client: Dict,
                          current_performance: Dict) -> Dict:
    """
    Score retention risk for an existing client (0–100, higher = more risk).
    
    client dict:
    {
      "name": str,
      "aum_with_ncg": float,           # AUM in $ millions
      "performance_sensitivity": str,  # high / medium / low
      "last_contact_date": str,        # YYYY-MM-DD
      "last_performance_call_date": str,
      "redemption_history": list,      # list of past partial redemption dates
    }
    
    current_performance dict:
    {
      "strategy": str,
      "trailing_1yr_vs_benchmark_bps": int,  # negative = underperformance
      "trailing_3yr_vs_benchmark_bps": int,
      "ytd_vs_benchmark_bps": int,
    }
    """
    risk = 0
    
    # Performance-based risk
    t1yr = current_performance.get("trailing_1yr_vs_benchmark_bps", 0)
    t3yr = current_performance.get("trailing_3yr_vs_benchmark_bps", 0)
    
    if t1yr < -300:    risk += 35  # Significant 1yr underperformance
    elif t1yr < -150:  risk += 20
    elif t1yr < 0:     risk += 8
    
    if t3yr < -150:    risk += 20  # 3yr underperformance is more serious
    elif t3yr < 0:     risk += 10
    
    # Performance sensitivity multiplier
    sensitivity = client.get("performance_sensitivity", "medium")
    if sensitivity == "high":
        risk = int(risk * 1.4)
    elif sensitivity == "low":
        risk = int(risk * 0.6)
    
    # Contact recency
    from engagement_scorer import days_since  # reuse utility
    last_contact = days_since(client.get("last_contact_date"))
    if last_contact > 90:   risk += 20
    elif last_contact > 45: risk += 10
    
    # History of partial redemptions
    redemptions = client.get("redemption_history", [])
    if len(redemptions) >= 2: risk += 15
    elif len(redemptions) == 1: risk += 8
    
    risk = min(risk, 100)
    
    # Generate action flag
    action = None
    if risk >= 70:
        action = (f"⚠️ High retention risk — "
                  f"Schedule call with {client['name']} within 2 weeks. "
                  f"Lead with long-term track record and market context.")
    elif risk >= 45:
        action = (f"🟡 Moderate retention risk — "
                  f"Include {client['name']} in next monthly client update. "
                  f"Confirm they received Q commentary.")
    
    return {
        "client_name": client["name"],
        "aum_millions": client.get("aum_with_ncg", 0),
        "risk_score":   risk,
        "risk_tier":    "high" if risk >= 70 else "medium" if risk >= 45 else "low",
        "action":       action,
        "scored_at":    datetime.utcnow().isoformat(),
    }
```

---

## Step 5 — Daily Sales Brief Generator

Pulls all scores and generates John's daily brief using Claude.

**File: `sales_brief.py`**

```python
import anthropic
import os
import json
from datetime import datetime
from typing import List, Dict

client = anthropic.Anthropic(api_key=os.getenv("ANTHROPIC_API_KEY"))

SALES_BRIEF_PROMPT = """Generate a concise daily sales brief for John Solecki,
institutional sales at NCG (Next Century Growth Investors).
John manages a national institutional book as the firm's sole sales professional.

Today: {today}

PIPELINE DATA:
{pipeline_data}

FORMAT:
## Today's Priority Actions (max 3)
The 3 highest-priority actions for today. Be specific: name, action, reason.

## Retention Watch (clients needing attention)
Any existing clients with retention risk score ≥ 45. List name, risk tier,
and recommended action. If none, say "No clients currently flagged."

## New Prospects This Week
Any new prospects identified from 13F monitoring in the past 7 days.
List institution name, why they're a warm prospect, and suggested first step.

## Engagement Gaps (gone cold)
Prospects who scored well in the past and have recently gone cold.
Suggest a re-engagement approach for each. Max 3 items.

Keep the brief under 400 words total. John reads it in 5 minutes.
Facts only — no general sales advice."""


def generate_daily_brief(pipeline_data: Dict) -> str:
    """Generate John's daily sales intelligence brief."""
    
    response = client.messages.create(
        model="claude-sonnet-4-6",
        max_tokens=600,
        messages=[{"role": "user", "content": SALES_BRIEF_PROMPT.format(
            today=datetime.utcnow().strftime("%A, %B %d, %Y"),
            pipeline_data=json.dumps(pipeline_data, indent=2),
        )}]
    )
    
    return response.content[0].text
```

---

## Step 6 — Orchestrator

**File: `sales_intelligence_main.py`**

```python
import schedule
import time
import json
import os
import boto3
from datetime import datetime
from slack_sdk import WebClient

from thirteenf_monitor  import run_quarterly_13f_scan
from engagement_scorer  import score_engagement, flag_gone_cold
from retention_scorer   import score_retention_risk
from sales_brief        import generate_daily_brief

slack  = WebClient(token=os.getenv("SLACK_BOT_TOKEN"))
s3     = boto3.client("s3")
BUCKET = os.getenv("S3_RECORDKEEPING_BUCKET")

JOHN_SLACK_ID = os.getenv("JOHN_SLACK_ID")


def run_daily_sales_brief():
    """
    Run every morning at 7:00 AM CT.
    Pull Backstop data, score all contacts, generate and deliver brief.
    """
    # In production, fetch live data from Backstop API
    # Simplified: load from nightly Backstop export
    with open("data/backstop_export.json") as f:
        backstop_data = json.load(f)
    
    with open("data/current_performance.json") as f:
        performance = json.load(f)
    
    # Score all prospects
    prospect_scores = []
    for prospect in backstop_data.get("prospects", []):
        score = score_engagement(prospect)
        cold_flag = flag_gone_cold(prospect, score)
        prospect_scores.append({
            "name": prospect["name"],
            "score": score,
            "cold_flag": cold_flag,
        })
    
    # Score existing clients for retention risk
    retention_scores = []
    for client in backstop_data.get("clients", []):
        strategy = client.get("strategy", "small_cap_growth")
        perf = performance.get(strategy, {})
        risk = score_retention_risk(client, perf)
        retention_scores.append(risk)
    
    # Load any new 13F prospects identified this week
    try:
        import glob
        recent_files = sorted(glob.glob("data/new_prospects_*.json"))[-1:]
        new_prospects = []
        for f in recent_files:
            with open(f) as fp:
                new_prospects.extend(json.load(fp))
    except (IndexError, FileNotFoundError):
        new_prospects = []
    
    # Assemble pipeline data for brief
    pipeline_data = {
        "high_risk_clients":    [r for r in retention_scores if r["risk_tier"] == "high"],
        "medium_risk_clients":  [r for r in retention_scores if r["risk_tier"] == "medium"],
        "gone_cold_prospects":  [p for p in prospect_scores if p["cold_flag"]],
        "new_13f_prospects":    new_prospects[:5],  # Top 5 new prospects
        "high_engagement":      sorted(prospect_scores,
                                      key=lambda x: -x["score"])[:5],
    }
    
    # Generate brief
    brief = generate_daily_brief(pipeline_data)
    
    # Deliver to John
    slack.chat_postMessage(
        channel=JOHN_SLACK_ID,
        text=f"*NCG Sales Intelligence — "
             f"{datetime.utcnow().strftime('%A, %b %d')}*\n\n{brief}",
    )
    
    # Archive
    key = (f"sales_briefs/{datetime.utcnow().strftime('%Y-%m-%d')}_brief.json")
    s3.put_object(
        Bucket=BUCKET,
        Key=key,
        Body=json.dumps({
            "brief": brief,
            "pipeline_data": pipeline_data,
            "generated": datetime.utcnow().isoformat(),
        }, indent=2),
        ContentType="application/json",
    )
    
    print(f"Sales brief delivered and archived → s3://{key}")


# Run Monday–Friday at 7:00 AM CT (13:00 UTC)
schedule.every().monday.at("13:00").do(run_daily_sales_brief)
schedule.every().tuesday.at("13:00").do(run_daily_sales_brief)
schedule.every().wednesday.at("13:00").do(run_daily_sales_brief)
schedule.every().thursday.at("13:00").do(run_daily_sales_brief)
schedule.every().friday.at("13:00").do(run_daily_sales_brief)

# Run 13F scan quarterly — triggered manually or via cron
# schedule.every().day.at("18:00").do(check_if_13f_quarter_end)

if __name__ == "__main__":
    run_daily_sales_brief()  # Test run immediately
    while True:
        schedule.run_pending()
        time.sleep(60)
```

---

## Testing Checklist

- [ ] Test 13F monitor on 5 known competitor managers — confirm their
  institutional holders appear in results
- [ ] Test engagement scorer on 3 known active clients and 3 known
  inactive contacts — scores should reflect the actual relationship quality
- [ ] Test retention scorer during a period of mild underperformance —
  confirm high-sensitivity clients score higher risk
- [ ] Review 5 generated sales briefs — confirm priority actions are
  actionable and specific, not generic
- [ ] Confirm S3 archiving works for all brief types
- [ ] John Solecki reviews one week of daily briefs and provides calibration
  feedback before going live

---

## Go-Live Checklist

- [ ] Backstop CRM configured with custom fields from Step 1
- [ ] Existing client and prospect data imported into Backstop
- [ ] John Solecki trained on reading the brief (30-minute walkthrough)
- [ ] Bob Scott (CEO) briefed on the retention risk scoring approach —
  confirm the thresholds reflect NCG's actual client relationship norms
- [ ] Performance data feed established (manual weekly update or Backstop
  performance module integration)
- [ ] 13F competitor list reviewed and confirmed with John

---

## Cost Breakdown

| Item | Type | Monthly Cost |
|---|---|---|
| Backstop Solutions CRM | Subscription | $2,000–$4,000 |
| Anthropic Claude API (daily briefs) | Usage | $50–$150 |
| eVestment database (optional consultant tracking) | Subscription | $500–$1,000 |
| AWS (S3 + compute — shared) | Usage | Minimal |
| **Total** | | **$2,550–$5,150/mo** |

---

## Ongoing Maintenance

| Task | Frequency | Owner |
|---|---|---|
| Update Backstop with meeting notes and contact activity | After each meeting/call | John Solecki |
| Calibrate engagement score thresholds | Quarterly | John + Bob Scott |
| Run 13F scan and review new prospects | Quarterly (post filing deadline) | John Solecki |
| Export existing clients list to keep 13F deduplication current | Monthly | Kelly McNulty |
| Review brief quality — is it surfacing the right actions? | Monthly | John Solecki |
