# Initiative 4 — Client Communication Accelerator
## Build Guide

**Parent strategy:** [NCG AI Strategy Assessment](../ncg-ai-strategy-assessment.md)  
**Priority:** Start now  
**Build time:** 4–6 weeks  
**Monthly cost:** $600–$1,800  
**Primary users:** John Solecki (institutional sales), all portfolio managers

---

## What This Builds

Three connected tools that multiply the output of a one-person institutional
sales operation and reduce the time portfolio managers spend on communication
scaffolding rather than research:

- **4a — RFP Answer Library:** An AI assistant that drafts RFP responses in
  4–6 hours instead of 20–40 hours by assembling NCG's own words from a
  structured answer library
- **4b — Commentary Production Support:** A template-driven assistant that
  generates the structural scaffolding of the quarterly letter so PMs write
  only the qualitative analysis
- **4c — Prospect Research Briefs:** A pipeline that auto-generates pre-meeting
  institution profiles from public 13F filings and org data in under 10 minutes

All three produce drafts. Humans approve before anything goes out.

---

## 4a — RFP Answer Library

### Architecture

```
┌─────────────────────────────────────────┐
│  ANSWER LIBRARY (structured JSON)        │
│  ~200 Q&A pairs, tagged by category     │
│  Source: John's prior RFP responses     │
└──────────────────┬──────────────────────┘
                   │
                   ▼
┌─────────────────────────────────────────┐
│  NEW RFP INPUT (PDF or DOCX)            │
└──────────────────┬──────────────────────┘
                   │
                   ▼
┌─────────────────────────────────────────┐
│  QUESTION EXTRACTOR (Claude)            │
│  Parses each RFP question               │
└──────────────────┬──────────────────────┘
                   │
                   ▼
┌─────────────────────────────────────────┐
│  ANSWER MATCHER + DRAFTER (Claude)      │
│  Maps question → best library answer   │
│  + drafts bridging language             │
└──────────────────┬──────────────────────┘
                   │
                   ▼
┌─────────────────────────────────────────┐
│  DRAFT RFP RESPONSE (Word/Markdown)     │
│  → John reviews and customizes          │
│  → PM approves any investment claims    │
└─────────────────────────────────────────┘
```

### Step 1 — Build the Answer Library

This is the most important step and requires no code. John Solecki pulls
the last 10 RFP responses and categorizes every answer.

**File: `data/rfp_library.json`** (sample structure)

```json
{
  "version": "1.0",
  "last_updated": "2026-03-01",
  "maintained_by": "John Solecki",
  "answers": [
    {
      "id": "PHIL-001",
      "category": "investment_philosophy",
      "question_patterns": [
        "Describe your investment philosophy",
        "What is your investment approach",
        "How do you select investments"
      ],
      "answer": "NCG focuses exclusively on high-growth companies across market capitalizations. We believe investing in companies that can sustain high organic growth rates will drive superior long-term investment returns. The better we are at identifying these rare, high-growth companies, the better our investment performance will be.",
      "approved_by": "Bob Scott",
      "approved_date": "2026-02-01",
      "strategies": ["all"]
    },
    {
      "id": "PROC-001",
      "category": "investment_process",
      "question_patterns": [
        "Describe your research process",
        "How do you identify investment candidates",
        "Walk us through your investment process"
      ],
      "answer": "Our process begins with a quantitative screen of the universe of publicly traded companies to identify those growing revenue at high rates within each market cap segment. We then build a mosaic combining information from company managements, analyst contacts, trade publications, and industry contacts developed over 25+ years. Each potential investment is actively debated by all portfolio managers before purchase and for as long as it is held.",
      "approved_by": "Peter Capouch",
      "approved_date": "2026-02-01",
      "strategies": ["all"]
    },
    {
      "id": "RISK-001",
      "category": "risk_management",
      "question_patterns": [
        "How do you manage risk",
        "Describe your risk management process",
        "What are your portfolio construction guidelines"
      ],
      "answer": "We believe the biggest risk to our portfolios is an issue at one of our companies causing a sudden, unforeseen slowdown in growth. We spend over half our time continuing to research portfolio companies to ensure fundamentals remain strong. We maintain sector and stock-level constraints. A key component of our risk process is admitting mistakes quickly — when we believe a thesis is broken, we sell.",
      "approved_by": "Tom Press",
      "approved_date": "2026-02-01",
      "strategies": ["all"]
    }
  ]
}
```

**Categories to build out (target 200+ total answers):**
- `investment_philosophy` — 10–15 answers
- `investment_process` — 15–20 answers
- `risk_management` — 15–20 answers
- `team_and_organization` — 20–25 answers
- `performance_and_track_record` — 10–15 answers (link to performance docs)
- `operations_and_compliance` — 20–30 answers (Kelly McNulty input required)
- `esg_and_responsible_investing` — 10–15 answers
- `fees_and_pricing` — 5–10 answers
- `separately_managed_accounts` — 15–20 answers
- `mutual_funds` — 10–15 answers
- `ucits_fund` — 10–15 answers

### Step 2 — RFP Response Engine

**File: `rfp_engine.py`**

```python
import anthropic
import json
import os
from typing import List, Dict

client = anthropic.Anthropic(api_key=os.getenv("ANTHROPIC_API_KEY"))

QUESTION_EXTRACTOR_PROMPT = """Extract all questions from this RFP document.
Return a JSON array of objects, one per question:
[{{"question_number": "1a", "question_text": "...", "max_words": null}}]

If a word limit is specified, capture it in max_words.
If no word limit, set max_words to null.

RFP DOCUMENT:
{rfp_text}

Return JSON only. No preamble."""


ANSWER_DRAFTER_PROMPT = """You are drafting an RFP response on behalf of NCG 
(Next Century Growth Investors), a growth equity investment manager.

QUESTION:
{question}

BEST MATCHING ANSWER FROM NCG'S LIBRARY:
{library_answer}

WORD LIMIT: {word_limit}

INSTRUCTIONS:
1. Use the library answer as the foundation — these are NCG's approved words
2. Adapt the answer to directly address the specific question asked
3. Keep NCG's voice: professional, direct, confident but not arrogant
4. Do not add claims, statistics, or facts not in the library answer
5. If the library answer doesn't fully address the question, note 
   "[REQUIRES ADDITIONAL INPUT FROM TEAM]" at the end
6. Stay within word limit if specified

Write the answer only. No preamble, no explanation."""


def extract_rfp_questions(rfp_text: str) -> List[Dict]:
    """Parse all questions from an RFP document."""
    response = client.messages.create(
        model="claude-sonnet-4-6",
        max_tokens=3000,
        messages=[{"role": "user", "content":
                   QUESTION_EXTRACTOR_PROMPT.format(rfp_text=rfp_text[:20000])}]
    )
    
    try:
        return json.loads(response.content[0].text)
    except json.JSONDecodeError:
        return []


def find_best_answer(question: str, library: List[Dict]) -> Dict:
    """
    Find the best matching answer from the library using embedding similarity.
    Simplified version uses keyword matching; production should use embeddings.
    """
    question_lower = question.lower()
    best_match = None
    best_score = 0
    
    for entry in library:
        score = 0
        # Score by pattern match
        for pattern in entry.get("question_patterns", []):
            if any(word in question_lower
                   for word in pattern.lower().split()):
                score += 1
        # Score by category keyword match
        if entry["category"].replace("_", " ") in question_lower:
            score += 2
        
        if score > best_score:
            best_score = score
            best_match = entry
    
    return best_match


def draft_rfp_response(rfp_text: str, library_path: str,
                       strategy: str = "all") -> str:
    """
    Generate a complete draft RFP response.
    Returns markdown-formatted draft with PM review flags.
    """
    with open(library_path) as f:
        library_data = json.load(f)
    
    # Filter answers by strategy relevance
    library = [a for a in library_data["answers"]
               if a["strategy"] in (strategy, "all")]
    
    questions = extract_rfp_questions(rfp_text)
    
    draft_sections = [
        "# RFP DRAFT RESPONSE — NCG\n\n"
        "**Status: DRAFT — Requires John Solecki review + PM approval "
        "for any investment claims**\n\n"
        "---\n\n"
    ]
    
    for q in questions:
        best = find_best_answer(q["question_text"], library)
        
        if not best:
            # No library match — flag for manual response
            draft_sections.append(
                f"## Question {q.get('question_number', '?')}\n\n"
                f"**Question:** {q['question_text']}\n\n"
                f"**🚩 NO LIBRARY MATCH — Requires manual response**\n\n"
                f"---\n\n"
            )
            continue
        
        # Draft the answer using Claude
        response = client.messages.create(
            model="claude-sonnet-4-6",
            max_tokens=500,
            messages=[{"role": "user", "content": ANSWER_DRAFTER_PROMPT.format(
                question=q["question_text"],
                library_answer=best["answer"],
                word_limit=q.get("max_words") or "No limit specified",
            )}]
        )
        
        answer = response.content[0].text
        
        draft_sections.append(
            f"## Question {q.get('question_number', '?')}\n\n"
            f"**Question:** {q['question_text']}\n\n"
            f"**Answer:**\n\n{answer}\n\n"
            f"_[Library source: {best['id']} | "
            f"Approved by: {best['approved_by']}]_\n\n"
            f"---\n\n"
        )
    
    return "".join(draft_sections)
```

---

## 4b — Quarterly Commentary Production Support

### Architecture

The commentary has two types of content:
- **Structural/data content** — performance tables, attribution, sector weights.
  AI generates this from data inputs.
- **Qualitative analysis** — market commentary, portfolio thesis updates,
  new investment rationale. PMs write this. AI never touches it.

**File: `commentary_builder.py`**

```python
import anthropic
import os
from datetime import datetime

client = anthropic.Anthropic(api_key=os.getenv("ANTHROPIC_API_KEY"))

# Load NCG's voice from prior approved commentaries
# In production, store 4–6 approved past commentaries as context
NCG_VOICE_EXAMPLES = """
[Excerpt from Q3 2025 approved commentary]
"Despite economic fears and uncertainty causing a market pullback early
in the year, 2025 was the third straight year of double-digit gains for
US equities..."

[Excerpt from Q2 2025 approved commentary]
"In the second quarter, equity markets produced..."
"""

SECTION_PROMPT = """You are helping draft the structural section of NCG's 
quarterly portfolio commentary. NCG's voice is professional, concise, 
direct, and avoids both hype and excessive hedging.

SECTION TYPE: {section_type}
QUARTER: {quarter}
YEAR: {year}

INPUT DATA:
{data}

EXAMPLES OF NCG'S APPROVED WRITING STYLE:
{voice_examples}

Draft this section following NCG's style exactly. 
Do not add market opinions or investment recommendations.
Stick strictly to organizing and presenting the data provided.
Flag any gaps in the data with [DATA NEEDED: description]."""


def build_performance_table(performance_data: dict, quarter: str, year: int) -> str:
    """
    Generate the performance attribution table section from data inputs.
    
    performance_data format:
    {
      "strategy_name": str,
      "quarter_return": float,
      "benchmark_return": float,
      "ytd_return": float,
      "benchmark_ytd": float,
      "top_contributors": [{"name": str, "contribution_bps": int}],
      "top_detractors":   [{"name": str, "contribution_bps": int}],
    }
    """
    response = client.messages.create(
        model="claude-sonnet-4-6",
        max_tokens=800,
        messages=[{"role": "user", "content": SECTION_PROMPT.format(
            section_type="performance_attribution_table",
            quarter=quarter,
            year=year,
            data=str(performance_data),
            voice_examples=NCG_VOICE_EXAMPLES,
        )}]
    )
    return response.content[0].text


def build_sector_positioning_section(sector_data: dict,
                                      quarter: str, year: int) -> str:
    """
    Generate the sector positioning narrative from sector weight data.
    
    sector_data format:
    {
      "overweights": [{"sector": str, "vs_benchmark_pct": float}],
      "underweights": [{"sector": str, "vs_benchmark_pct": float}],
      "notable_changes": [{"sector": str, "change": str}]
    }
    """
    response = client.messages.create(
        model="claude-sonnet-4-6",
        max_tokens=600,
        messages=[{"role": "user", "content": SECTION_PROMPT.format(
            section_type="sector_positioning_summary",
            quarter=quarter,
            year=year,
            data=str(sector_data),
            voice_examples=NCG_VOICE_EXAMPLES,
        )}]
    )
    return response.content[0].text


def assemble_commentary_template(quarter: str, year: int,
                                  strategy: str) -> str:
    """
    Generate the full commentary template with AI-completed structural
    sections and clear PM-input placeholders.
    """
    return f"""# {strategy} — Q{quarter} {year} Commentary

---

## [PM WRITES: Market Overview — Q{quarter} {year}]

> *This section contains the portfolio managers' view of market conditions
> during the quarter. Write 2–3 paragraphs.*

[PM INPUT REQUIRED]

---

## Portfolio Performance

[AI-GENERATED — Review before use]
{{performance_table_output}}

---

## Sector Positioning

[AI-GENERATED — Review before use]
{{sector_section_output}}

---

## [PM WRITES: New Investments — Q{quarter} {year}]

> *For each new investment initiated during the quarter:
> company name, what they do, and the thesis in 3–4 sentences.*

[PM INPUT REQUIRED]

---

## [PM WRITES: Portfolio Updates]

> *Notable developments at existing holdings. Focus on
> fundamental changes, not price movement.*

[PM INPUT REQUIRED]

---

## [PM WRITES: Outlook]

> *2–3 sentences on positioning heading into the next quarter.*

[PM INPUT REQUIRED]

---

*Past performance is not indicative of future results.
This commentary is for informational purposes only and does not 
constitute investment advice.*
"""
```

---

## 4c — Prospect Research Brief Generator

**File: `prospect_brief.py`**

```python
import requests
import anthropic
import os
import json
from datetime import datetime

client   = anthropic.Anthropic(api_key=os.getenv("ANTHROPIC_API_KEY"))
EDGAR_UA = os.getenv("SEC_EDGAR_USER_AGENT")

BRIEF_PROMPT = """Generate a pre-meeting prospect brief for John Solecki, 
an institutional sales professional at NCG (Next Century Growth Investors).

NCG manages growth equity strategies: Micro Cap, Small Cap, SMID Cap,
and Large Cap Growth (separate accounts and mutual funds).

INSTITUTION: {institution_name}
TYPE: {institution_type}  (pension / endowment / foundation / family office / consultant)

13F FILING DATA (most recent quarter):
{holdings_13f}

PUBLIC INFORMATION:
{public_info}

Generate a structured brief with these sections:

## Institution Overview
Size of AUM (if disclosed), investment committee structure (if known),
geographic location, and any public statements about investment approach.

## Current Growth Equity Exposure
Based on 13F data: which growth equity managers do they currently use?
What is their approximate allocation to small/mid cap growth?
Any notable recent additions or reductions?

## NCG Fit Assessment  
Which of NCG's strategies (Micro Cap / Small Cap / SMID / Large Cap) 
best fits their current allocation gaps? Why?
Note: This is a fit assessment for sales planning — not an investment recommendation.

## Talking Points for John
3–4 specific conversation openers tailored to this institution's 
known holdings and allocation posture.

## Open Questions to Ask
3–4 questions John should ask in the meeting to understand their
current manager review cycle and decision criteria.

Write factually from the data. Note explicitly where data is absent.
Do not fabricate specific figures."""


def fetch_13f_holdings(institution_cik: str) -> str:
    """
    Fetch the most recent 13F filing for an institution from SEC EDGAR.
    Returns the top 30 holdings as a structured string.
    """
    resp = requests.get(
        f"https://data.sec.gov/submissions/CIK{institution_cik}.json",
        headers={"User-Agent": EDGAR_UA},
        timeout=10
    )
    
    if resp.status_code != 200:
        return "13F data not available"
    
    data = resp.json()
    
    # Find most recent 13F filing
    for i, form in enumerate(data["filings"]["recent"]["form"]):
        if form == "13F-HR":
            accession = data["filings"]["recent"]["accessionNumber"][i]
            # In production, fetch and parse the actual 13F XML
            # to extract manager names, share counts, and values.
            # This is simplified — production uses SEC's XBRL 13F parser.
            return f"13F accession: {accession} — parse holdings here"
    
    return "No 13F filing found"


def generate_prospect_brief(institution_name: str,
                             institution_type: str,
                             institution_cik: str,
                             public_notes: str = "") -> str:
    """
    Generate a pre-meeting brief for a prospect institution.
    Takes ~2–3 minutes to run.
    """
    holdings = fetch_13f_holdings(institution_cik)
    
    response = client.messages.create(
        model="claude-sonnet-4-6",
        max_tokens=1500,
        messages=[{"role": "user", "content": BRIEF_PROMPT.format(
            institution_name=institution_name,
            institution_type=institution_type,
            holdings_13f=holdings,
            public_info=public_notes or "No additional public information provided",
        )}]
    )
    
    header = (
        f"# Prospect Brief: {institution_name}\n\n"
        f"**Generated:** {datetime.utcnow().strftime('%B %d, %Y')}\n"
        f"**Prepared for:** John Solecki\n\n"
        f"> *AI-generated from public data (13F filings, public sources). "
        f"Review for accuracy before meeting. "
        f"Contains no investment recommendations.*\n\n---\n\n"
    )
    
    return header + response.content[0].text
```

---

## Testing Checklist

**4a — RFP Library:**
- [ ] Build library to 50+ answers before testing (minimum viable library)
- [ ] Test on one real RFP from the last 12 months — compare draft
  to the actual submitted response for coverage and accuracy
- [ ] Confirm all flagged gaps (`[REQUIRES ADDITIONAL INPUT]`) are real
  gaps, not hallucinated absences
- [ ] One PM reviews a complete draft and confirms no unapproved
  investment claims appear

**4b — Commentary:**
- [ ] Run commentary template on Q4 2025 data and compare structural
  sections to the actual Q4 2025 commentary — should match closely
- [ ] Confirm PM-input sections are clearly marked and cannot be missed
- [ ] Confirm AI-generated sections are clearly marked for review

**4c — Prospect Briefs:**
- [ ] Test on 3 known existing clients — does the 13F analysis correctly
  show their current small/mid cap growth exposure?
- [ ] Confirm no fabricated AUM figures or manager names appear
- [ ] Time the pipeline: target under 3 minutes per brief

---

## Go-Live Checklist

- [ ] CCO review: confirm communications supervisory procedure updated
  to include AI review step before any client or prospect communication
- [ ] John Solecki completes 2-week answer library build sprint
- [ ] PMs agree on which commentary sections are AI-appropriate vs. PM-only
- [ ] Template for commentary reviewed and approved by one PM
- [ ] John tests RFP pipeline on one live RFP before it becomes the default workflow

---

## Cost Breakdown

| Item | Type | Monthly Cost |
|---|---|---|
| Anthropic Claude API (all three tools) | Usage | $200–$500 |
| AWS S3 archive | Usage | Minimal (<$10) |
| Loopio or similar RFP platform (optional — can use raw scripts) | Subscription | $300–$800 |
| **Total** | | **$500–$1,310/mo** |

---

## Ongoing Maintenance

| Task | Frequency | Owner |
|---|---|---|
| Update RFP answer library with new approved answers | Monthly | John Solecki |
| PM review of all draft communications before distribution | Per piece | Designated PM |
| Refresh NCG voice examples in commentary builder | Quarterly | John Solecki |
| Archive all AI-generated client communications | Automated | S3 pipeline |
