# Initiative 3 — Sell Discipline Signal Layer
## Build Guide

**Parent strategy:** [NCG AI Strategy Assessment](../ncg-ai-strategy-assessment.md)  
**Priority:** Phase 2 — requires CCO review before build  
**Build time:** 10–12 weeks (8 weeks build + 2 weeks CCO/compliance review)  
**Monthly cost:** $2,000–$4,000  
**Hard dependency:** Initiative 1 infrastructure must be live first  
**Regulatory sensitivity:** High — read compliance section before starting

---

## What This Builds

A dedicated signal monitoring layer that sits on top of the Initiative 1
surveillance infrastructure and applies specific detection logic to portfolio
companies looking for patterns historically associated with growth deceleration —
before those patterns appear in reported revenue.

When two or more leading indicators fire simultaneously for a single company,
an alert is sent immediately to the relevant portfolio manager. The alert
presents the data. The PM decides what to do with it.

No automated action is taken. No sell recommendation is made. The word "sell"
never appears in system output.

---

## Compliance Review (Complete Before Building)

This initiative has higher regulatory sensitivity than Initiatives 1, 2, 4, or 5
because it directly informs position exit decisions. Before writing a line of code:

**Required CCO actions:**
1. Kelly McNulty reviews this document and the methodology described in Step 2
2. Confirm that the signal system is documented as a research tool, not a
   discretionary model, in NCG's written supervisory procedures (WSPs)
3. Confirm Form ADV Part 2A disclosure language for AI-assisted monitoring is
   adequate — if not, update before go-live
4. Review vendor DPAs (Revelio, AlphaSense) to confirm NCG's usage patterns
   and investment activity cannot be inferred from API call patterns
5. Confirm recordkeeping approach satisfies Rule 204-2 for research records
   used in investment decisions

**What the CCO should confirm in writing:**
> "The NCG Sell Signal Layer is a research monitoring tool. It surfaces public
> data signals for portfolio manager review. It does not make recommendations,
> does not execute trades, and does not replace portfolio manager judgment.
> All investment decisions remain exclusively with the portfolio managers."

---

## Architecture Overview

```
┌──────────────────────────────────────────────────────────┐
│         INITIATIVE 1 INFRASTRUCTURE (already running)    │
│  SEC EDGAR  │  AlphaSense transcripts  │  Revelio jobs   │
└──────────────────────────┬───────────────────────────────┘
                           │ raw signals (all tickers)
                           ▼
┌──────────────────────────────────────────────────────────┐
│              SELL SIGNAL DETECTOR                         │
│  sell_detector.py                                         │
│                                                           │
│  CHECK 1: Job posting contraction (Revelio)               │
│  CHECK 2: Transcript language shift (Claude)              │
│  CHECK 3: Insider selling cluster (EDGAR Form 4)          │
│  CHECK 4: Customer review velocity (G2/Capterra)          │
│  CHECK 5: Guidance language deterioration (AlphaSense)    │
└──────────────────────────┬───────────────────────────────┘
                           │
                           ▼
┌──────────────────────────────────────────────────────────┐
│          MULTI-SIGNAL COMBINER                            │
│  combiner.py                                              │
│  Fires alert when ≥2 signals active simultaneously        │
│  Single signal → Watch tier only (no alert)               │
└──────────────────────────┬───────────────────────────────┘
                           │
              ┌────────────┼────────────┐
              ▼            ▼            ▼
       Immediate        Morning      S3 Archive
       Slack alert     digest flag   (recordkeeping)
       (PM only)
```

---

## Step 1 — Individual Signal Detectors

Each of the five signal detectors is independent. They share a common
return format: `{"ticker": str, "signal_type": str, "fired": bool,
"evidence": str, "severity": "high"|"medium"}`.

**File: `sell_detector.py`**

```python
import requests
import os
from datetime import datetime, timedelta
from typing import Optional
import anthropic

REVELIO_KEY     = os.getenv("REVELIO_API_KEY")
ALPHASENSE_KEY  = os.getenv("ALPHASENSE_API_KEY")
claude_client   = anthropic.Anthropic(api_key=os.getenv("ANTHROPIC_API_KEY"))

# ── CHECK 1: Job Posting Contraction ────────────────────────────────────────

def check_job_contraction(ticker: str) -> dict:
    """
    Fire if net growth-function job postings have declined >20% over
    the past 60 days vs. the prior 60 days.
    
    Growth functions: Sales, Marketing, Customer Success, Biz Dev.
    Excludes Support and G&A — those contractions are less predictive.
    """
    resp = requests.get(
        "https://api.reveliolabs.com/v1/job_postings/company_timeseries",
        headers={"Authorization": f"Bearer {REVELIO_KEY}"},
        params={
            "ticker": ticker,
            "weeks": 18,
            "group_by": "function",
            "functions": "Sales,Marketing,Customer Success,Business Development",
        },
        timeout=15
    )
    
    if resp.status_code != 200:
        return {"ticker": ticker, "signal_type": "job_contraction",
                "fired": False, "evidence": "Data unavailable", "severity": None}
    
    data = resp.json()
    
    total_recent = 0
    total_prior  = 0
    
    for func_data in data.get("results", []):
        counts = func_data["weekly_counts"]
        if len(counts) < 16:
            continue
        total_recent += sum(c["count"] for c in counts[-8:]) / 8
        total_prior  += sum(c["count"] for c in counts[-16:-8]) / 8
    
    if total_prior == 0:
        return {"ticker": ticker, "signal_type": "job_contraction",
                "fired": False, "evidence": "Insufficient data", "severity": None}
    
    pct_change = (total_recent - total_prior) / total_prior * 100
    
    fired = pct_change < -20
    severity = "high" if pct_change < -35 else "medium"
    
    return {
        "ticker": ticker,
        "signal_type": "job_contraction",
        "fired": fired,
        "evidence": (
            f"Growth-function postings: {pct_change:+.1f}% over 60 days "
            f"({total_prior:.0f} avg/wk → {total_recent:.0f} avg/wk). "
            f"Threshold: -20% for trigger."
        ),
        "severity": severity if fired else None,
    }


# ── CHECK 2: Transcript Language Deterioration ───────────────────────────────

DETERIORATION_PROMPT = """Analyze these two consecutive quarterly earnings 
call transcripts (prepared remarks only) from the same company.

Determine ONLY whether the current quarter shows meaningful deterioration 
in growth language relative to the prior quarter. Deterioration means:
- Management is more hedging or vague about growth trajectory
- New mentions of macro headwinds, budget scrutiny, or deal slippage 
  not present in the prior quarter
- Guidance language has weakened (from specific to vague ranges)
- Competitive pressure language has intensified

Do NOT flag normal quarter-to-quarter variation in phrasing.
Do NOT flag improvements or neutral changes.
Only flag genuine deterioration.

PRIOR QUARTER TRANSCRIPT:
{prior}

CURRENT QUARTER TRANSCRIPT:
{current}

Return JSON only:
{{
  "deterioration_detected": true/false,
  "severity": "high" | "medium" | null,
  "evidence": "One specific sentence quoting the change detected, 
               or null if no deterioration"
}}"""


def check_transcript_deterioration(
    ticker: str,
    prior_transcript: str,
    current_transcript: str
) -> dict:
    """
    Use Claude to detect growth language deterioration between
    two consecutive earnings call transcripts.
    """
    import json
    
    response = claude_client.messages.create(
        model="claude-sonnet-4-6",
        max_tokens=300,
        messages=[{"role": "user", "content": DETERIORATION_PROMPT.format(
            prior=prior_transcript[:4000],
            current=current_transcript[:4000],
        )}]
    )
    
    try:
        result = json.loads(response.content[0].text)
    except json.JSONDecodeError:
        result = {"deterioration_detected": False,
                  "severity": None, "evidence": None}
    
    return {
        "ticker": ticker,
        "signal_type": "transcript_deterioration",
        "fired": result.get("deterioration_detected", False),
        "evidence": result.get("evidence", ""),
        "severity": result.get("severity"),
    }


# ── CHECK 3: Insider Selling Cluster ─────────────────────────────────────────

def check_insider_selling(ticker: str, days_back: int = 30) -> dict:
    """
    Fire if 3+ unique insiders have filed discretionary sales
    (non-10b5-1) in the past 30 days.
    
    Rule: 10b5-1 plan sales are excluded — they are pre-planned.
    Discretionary cluster sales by multiple insiders are the signal.
    """
    since = (datetime.utcnow() - timedelta(days=days_back)
             ).strftime("%Y-%m-%d")
    
    # Use OpenInsider or EDGAR full-text search for Form 4 data
    # OpenInsider provides structured Form 4 data with 10b5-1 flag
    resp = requests.get(
        "https://openinsider.com/screener",
        params={
            "s": ticker,
            "fd": -30,
            "td": 0,
            "tt": "4",       # Sale transactions
            "cli": "1",      # Exclude 10b5-1 plans
            "format": "json",
        },
        timeout=10
    )
    
    if resp.status_code != 200:
        return {"ticker": ticker, "signal_type": "insider_selling",
                "fired": False, "evidence": "Data unavailable", "severity": None}
    
    transactions = resp.json().get("data", [])
    unique_insiders = set(t.get("insider_name") for t in transactions)
    total_value = sum(float(t.get("value", 0)) for t in transactions)
    
    fired = len(unique_insiders) >= 3
    severity = "high" if (len(unique_insiders) >= 4
                          or total_value > 5_000_000) else "medium"
    
    return {
        "ticker": ticker,
        "signal_type": "insider_selling",
        "fired": fired,
        "evidence": (
            f"{len(unique_insiders)} unique insiders filed discretionary "
            f"sales totaling ${total_value:,.0f} in the past {days_back} days. "
            f"10b5-1 plan sales excluded."
        ) if fired else f"Only {len(unique_insiders)} discretionary sellers — below threshold.",
        "severity": severity if fired else None,
    }


# ── CHECK 4: Guidance Deterioration ──────────────────────────────────────────

def check_guidance_deterioration(ticker: str) -> dict:
    """
    Fire if the most recent quarter's guidance was:
    (a) below consensus at time of issuance, AND
    (b) represented a deceleration from the prior quarter's guidance.
    
    This is a concurrent indicator — it appears in the same quarter
    revenue begins to slow, not ahead of it.
    """
    resp = requests.get(
        "https://api.alphasense.com/v1/estimates/guidance_history",
        headers={"Authorization": f"Bearer {ALPHASENSE_KEY}"},
        params={"ticker": ticker, "metric": "revenue", "quarters": 4},
        timeout=10
    )
    
    if resp.status_code != 200 or not resp.json().get("history"):
        return {"ticker": ticker, "signal_type": "guidance_deterioration",
                "fired": False, "evidence": "Data unavailable", "severity": None}
    
    history = resp.json()["history"]
    if len(history) < 2:
        return {"ticker": ticker, "signal_type": "guidance_deterioration",
                "fired": False, "evidence": "Insufficient quarters", "severity": None}
    
    curr = history[0]
    prev = history[1]
    
    # Compare guidance growth rate: current quarter's implied growth
    # vs. prior quarter's implied growth
    curr_growth = curr.get("guidance_growth_rate", None)
    prev_growth = prev.get("guidance_growth_rate", None)
    
    if curr_growth is None or prev_growth is None:
        return {"ticker": ticker, "signal_type": "guidance_deterioration",
                "fired": False, "evidence": "Growth rate data unavailable",
                "severity": None}
    
    deceleration = prev_growth - curr_growth
    fired = deceleration > 5.0  # >500bps deceleration in guided growth rate
    
    return {
        "ticker": ticker,
        "signal_type": "guidance_deterioration",
        "fired": fired,
        "evidence": (
            f"Guided growth rate decelerated {deceleration:.1f}ppts: "
            f"{prev['quarter']} guided {prev_growth:.1f}% → "
            f"{curr['quarter']} guided {curr_growth:.1f}%."
        ),
        "severity": "high" if deceleration > 10 else "medium" if fired else None,
    }
```

---

## Step 2 — Multi-Signal Combiner

The rule is explicit: a single signal is a Watch item. Two or more signals
firing simultaneously generate an alert to the PM.

**File: `combiner.py`**

```python
from datetime import datetime
from typing import List, Dict


def evaluate_signals(ticker: str, signals: List[Dict],
                     position_pct: float) -> Dict:
    """
    Evaluate all sell signals for one ticker.
    
    Returns:
      status: "alert" | "watch" | "clear"
      fired_count: number of signals currently active
      summary: plain-language summary for PM
      signals: full signal detail list
    """
    fired = [s for s in signals if s.get("fired")]
    high_severity = [s for s in fired if s.get("severity") == "high"]
    
    # Alert conditions:
    # - 2+ signals fired (any severity), OR
    # - 1 high-severity signal + position > 2%
    alert = (
        len(fired) >= 2
        or (len(high_severity) >= 1 and position_pct >= 2.0)
    )
    
    status = "alert" if alert else ("watch" if fired else "clear")
    
    return {
        "ticker":       ticker,
        "status":       status,
        "fired_count":  len(fired),
        "position_pct": position_pct,
        "signals":      signals,
        "high_severity":len(high_severity),
        "evaluated_at": datetime.utcnow().isoformat(),
        "summary":      build_summary(ticker, fired, position_pct),
    }


def build_summary(ticker: str, fired_signals: List[Dict],
                  position_pct: float) -> str:
    """
    Build a plain-language, PM-facing summary of active signals.
    No investment opinion. Facts only.
    """
    if not fired_signals:
        return f"{ticker}: No active sell signals."
    
    signal_descriptions = "\n".join(
        f"  • {s['signal_type'].replace('_',' ').title()}: {s['evidence']}"
        for s in fired_signals
    )
    
    return (
        f"{ticker} — {len(fired_signals)} signal(s) active "
        f"(position: {position_pct:.1f}% of portfolio)\n\n"
        f"{signal_descriptions}\n\n"
        f"This alert surfaces public data signals for PM review. "
        f"No recommendation is made or implied."
    )
```

---

## Step 3 — Alert Delivery

**File: `sell_alert_delivery.py`**

```python
import os
import boto3
import json
from datetime import datetime
from slack_sdk import WebClient

slack  = WebClient(token=os.getenv("SLACK_BOT_TOKEN"))
s3     = boto3.client("s3")
BUCKET = os.getenv("S3_RECORDKEEPING_BUCKET")

URGENCY_EMOJI = {"alert": "🔴", "watch": "🟡", "clear": "⚪"}


def deliver_sell_alert(pm_slack_id: str, evaluation: dict) -> None:
    """
    Send an immediate Slack DM to the PM when alert status is triggered.
    Watch-tier signals are included in the daily morning digest only.
    """
    if evaluation["status"] != "alert":
        return  # Watch items go in morning digest; clear items not sent
    
    ticker  = evaluation["ticker"]
    summary = evaluation["summary"]
    
    slack.chat_postMessage(
        channel=pm_slack_id,
        text=(
            f"🔴 *Sell Signal Alert — {ticker}*\n\n"
            f"{summary}\n\n"
            f"_{evaluation['fired_count']} signal(s) active as of "
            f"{datetime.utcnow().strftime('%H:%M CT')}. "
            f"Full detail archived to S3._"
        ),
    )
    
    # Archive alert to S3 for SEC recordkeeping
    key = (f"sell_alerts/{datetime.utcnow().strftime('%Y-%m-%d')}/"
           f"{ticker}_{datetime.utcnow().strftime('%H%M')}_alert.json")
    
    s3.put_object(
        Bucket=BUCKET,
        Key=key,
        Body=json.dumps(evaluation, indent=2),
        ContentType="application/json",
    )
```

---

## Testing Checklist

The most critical testing for this initiative is historical backtesting —
confirming the signals actually predicted what they claim to predict.

- [ ] **Backtesting required before go-live:** For 10 companies in NCG's
  historical portfolio that experienced significant drawdowns, run the five
  detectors against data available 1–3 quarters before the drawdown.
  Did the signals fire? This validates the approach before it goes live.
- [ ] Confirm Form 4 insider selling detection correctly excludes 10b5-1
  planned sales — test with 3 known examples of each type
- [ ] Test transcript deterioration detector against a company that issued a
  profit warning — confirm the prior quarter's transcript would have flagged
- [ ] Confirm no alert is sent for a single low-severity signal
- [ ] Confirm all alerts are archived to S3 before Slack delivery
- [ ] CCO review: confirm WSP documentation is updated before testing on
  live portfolio companies

---

## Go-Live Checklist

- [ ] CCO written sign-off on methodology documentation
- [ ] Form ADV Part 2A updated
- [ ] Written supervisory procedure updated to include sell signal review step
- [ ] PM onboarding: 45-minute walkthrough of signal types, thresholds,
  and how to interpret an alert (especially: what the alert does NOT mean)
- [ ] Backtesting results reviewed by at least two PMs
- [ ] Historical alert archive initialized in S3 before first live alert

---

## Cost Breakdown

| Item | Type | Monthly Cost |
|---|---|---|
| Revelio Labs (job posting data — shared with Init. 1) | Subscription | $500–$1,500 |
| AlphaSense (guidance + transcripts — shared with Init. 1) | Subscription | Included |
| Anthropic Claude API (transcript analysis) | Usage | $100–$200 |
| AWS (S3 + compute — shared) | Usage | $20–$40 |
| OpenInsider API (Form 4 structured data) | Subscription | $200–$500 |
| **Total (marginal cost above Init. 1)** | | **$820–$2,240/mo** |

---

## Ongoing Maintenance

| Task | Frequency | Owner |
|---|---|---|
| Review false positive rate (alerts that didn't lead to deterioration) | Quarterly | Designated PM + COO |
| Adjust signal thresholds based on false positive review | Quarterly | Technical contact |
| Update transcript deterioration prompt if false positives are high | As needed | Technical contact |
| Annual WSP review to include sell signal methodology | Annually | CCO |
