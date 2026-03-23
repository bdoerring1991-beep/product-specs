# PRD: Einstein Deal Loss Intelligence

**Status:** Case Study — Portfolio  
**Author:** Blake Doerring
**Last Updated:** March 2026  
**Document Type:** Product Requirements Document  
**Intended Audience:** Hiring managers, product teams, Salesforce ecosystem partners

---

> **Case study framing:** This PRD identifies and addresses a genuine gap in
> Salesforce's current AI feature set. All referenced Einstein and Agentforce
> capabilities are current as of Salesforce Spring '25. Baseline metrics are
> sourced from Gartner, Forrester, and CSO Insights research. This document
> demonstrates product thinking applied to a real, well-documented problem in
> enterprise sales operations.

---

## The Gap in Salesforce's Current AI Coverage

Salesforce's Einstein and Agentforce suite is comprehensive for the *active* deal
lifecycle: Einstein Lead Scoring ranks inbound leads, Einstein Opportunity Insights
flags at-risk open deals, Agentforce Sales Coach simulates buyer objections before
calls, and Einstein Sales Emails drafts personalized outreach.

Every one of these features points forward — toward closing the deal.

**None of them activates when a deal is lost.**

When a rep changes an Opportunity's `StageName` to `Closed Lost`, Salesforce
records whatever the rep selects from a `Close_Reason__c` picklist — and that
is the end of the process. The competitive intelligence embedded in that lost
deal, the real reason it was lost, what the winning competitor said, which
objections the rep couldn't handle — all of it evaporates. It never reaches
product management, competitive enablement, or sales leadership in structured form.

This PRD proposes **Einstein Deal Loss Intelligence**: an AI system that activates
automatically on Closed Lost, conducts a structured asynchronous debrief with the
rep, extracts competitive and process intelligence, and routes it to the right
teams — without requiring a single calendar invite or manual report.

---

## Problem Statement

### The data quality problem

B2B companies rely on the `Close_Reason__c` picklist to understand why they lose
deals. This data is systematically unreliable.

- **Reps select loss reasons under low motivation and time pressure.** A rep who
  just lost a six-month deal does not carefully audit their own performance to
  choose the most accurate picklist value. They pick the most defensible option
  ("Price") and move on.
- **Forrester research (2023) found that manual loss-reason picklists are accurate
  only 38% of the time** when validated against call recordings and email threads.
  The remaining 62% of losses are misclassified — typically over-attributing to
  "Price" and under-attributing to "No Champion," "Competitor Feature Gap," and
  "Internal Priority Shift."
- **Salesforce's own research (State of Sales, 2024) found that 57% of sales reps
  say their CRM data does not accurately reflect why deals are lost.** Reps know
  the data is wrong. They just have no incentive to fix it.

### The competitive intelligence problem

Every lost deal contains competitive intelligence that product, marketing, and
sales enablement teams need:

- Which competitor won, and what they said their differentiators were
- Which objections the rep faced that existing battlecards didn't address
- Which product features the prospect explicitly said the competitor had and
  Salesforce's company didn't
- Which persona type (Economic Buyer, Champion, Technical Evaluator) went cold
  and why

This intelligence exists today only in the rep's memory and in unstructured email
threads and call recordings scattered across Salesforce Inbox, Gong, and personal
email. There is no system that extracts and routes it.

### The cost

- Average B2B enterprise deal cycle: 7.4 months (Gartner, 2024)
- Average deal value at Salesforce customers: $85,000 ARR (CSO Insights)
- The average enterprise sales team analyzes fewer than 20% of its lost deals in
  any structured format (CEB/Gartner Win/Loss Analysis Study, 2023)
- Each unanalyzed loss represents both a missed revenue recovery opportunity and
  a missed signal for product roadmap, competitive positioning, and rep coaching

---

## Proposed Solution

**Einstein Deal Loss Intelligence** is triggered automatically when an Opportunity
`StageName` is updated to `Closed Lost`. It does three things:

**1. Structured Async Debrief (rep-facing)**
Within 2 hours of the stage change, the rep receives a Slack message or Salesforce
in-app notification with a 5-question AI-powered debrief. The debrief is
conversational — not a form — and takes 4 minutes to complete. The AI reads
the rep's answers in context of the Opportunity's full history (emails via
Einstein Activity Capture, call notes, contact engagement) and asks intelligent
follow-up questions.

**2. Competitive Intelligence Extraction (automated)**
The AI analyzes the completed debrief plus available Opportunity data — contact
roles, email threads, call transcripts (if Gong/Einstein Conversation Intelligence
is connected) — and produces a structured Competitive Intelligence record
(`Competitive_Intel__c`) on the Opportunity. This record is automatically routed
to the relevant competitive enablement owner based on the competitor named.

**3. Pattern Intelligence for Leadership (aggregate)**
Across all completed debriefs, Einstein identifies loss patterns: "Deals lost to
Competitor X in the Mid-Market segment are 3x more likely to cite 'Reporting
Limitations' than Enterprise losses." This surfaces in a dedicated Tableau CRM
dashboard visible to Sales VPs, RevOps, and Product Management.

---

## Target Users

| Persona | Salesforce Role | What They Get |
|---|---|---|
| Account Executive | Standard User | 4-minute async debrief that feels like reflection, not admin |
| Sales Manager | Sales Manager Profile | Rep-level debrief summaries + coaching flags for 1:1s |
| VP of Sales | Sales VP Profile | Aggregate loss pattern dashboard by segment, competitor, and rep |
| RevOps / Sales Operations | System Administrator | Data quality improvement — accurate loss reasons replace picklist guesses |
| Product Manager | Read-only + Competitive Intel permission | Structured competitive feedback routed directly from closed deals |
| Competitive Enablement | Competitive Intel Owner permission set | Auto-routed intel records when named competitors appear in debriefs |
| Sales Enablement | Content Manager Profile | Objection pattern data to update battlecards and training materials |

---

## Goals & Success Metrics

### Primary Goal
Transform Closed Lost data from unreliable picklist values into structured,
actionable intelligence — without adding friction to the rep's workflow.

| Metric | Baseline | Target | Measurement |
|---|---|---|---|
| Loss reason data accuracy rate | 38% (validated vs. call recordings) | ≥ 75% | Quarterly audit: sample 50 losses, validate debrief output vs. Gong transcripts |
| Debrief completion rate | 0% (no debrief exists) | ≥ 70% | Debrief_Completed__c field on Opportunity |
| Time to complete debrief | N/A | ≤ 5 minutes median | Debrief start timestamp → submit timestamp |
| Competitive intel records created per quarter | ~15 (manually, inconsistently) | ≥ 80% of losses involving a named competitor | Competitive_Intel__c record count vs. Opportunities with competitor named |
| Rep satisfaction with debrief (1–5) | N/A | ≥ 3.8 | In-debrief CSAT after submission |

### Secondary Goal
Surface actionable competitive and product intelligence to non-sales teams.

| Metric | Baseline | Target | Measurement |
|---|---|---|---|
| Time from deal loss to competitive intel reaching product team | 14–30 days (manual) | ≤ 24 hours | Competitive_Intel__c CreatedDate vs. Opportunity CloseDate |
| Battlecard update frequency driven by loss data | Quarterly (manual review) | Monthly | Competitive enablement team OKR |
| Product roadmap items influenced by loss intelligence | Not tracked | 2+ per quarter attributed to loss data | Product team tagging in Jira/Productboard |

---

## Functional Requirements

### FR-01 — Closed Lost Trigger & Debrief Initiation (Critical)

**Trigger:** A Flow Builder automation fires when `Opportunity.StageName` changes
to `Closed Lost` on any Opportunity with `Amount >= $10,000` and
`CloseDate >= TODAY() - 7` (to exclude backdated closes and low-value pipeline).

**Trigger exclusions (do not initiate debrief if):**
- Opportunity was open for < 14 days (likely a data entry error or early-stage
  disqualification — these are noise, not signal)
- `Opportunity.OwnerId` maps to a user with `IsActive = false` (rep has left)
- The Opportunity already has a `Debrief_Completed__c = true`
  (de-duplication guard)
- `RecordType.Name = 'Renewal'` — renewal losses have a separate process

**Notification channel (configurable by admin):**
- Primary: Slack DM via Agentforce Slack integration (if Slack is connected)
- Fallback: Salesforce in-app Bell notification + Task created on the Opportunity
  with `Subject = 'Complete deal debrief — [Opportunity Name]'` and
  `ActivityDate = TODAY() + 2`

**Notification message (Slack example):**

> Hey [First Name] — you just closed out [Opportunity Name]. Before you move on,
> I have 5 quick questions that take about 4 minutes. Your answers go directly to
> the product and competitive teams, and your manager can see them too.
> No right answers here — this is about capturing what actually happened.
>
> [Start Debrief →]

Acceptance criteria:
- Notification delivered within 2 hours of `StageName` change
- Debrief link is single-use and expires after 7 days
- If not completed within 7 days: one reminder sent at day 4, then marked
  `Debrief_Status__c = 'Expired'`
- Admin can configure the `Amount` threshold and record type exclusions

---

### FR-02 — AI-Powered Conversational Debrief (Critical)

The debrief is delivered as a conversational interface — not a form. Each question
is presented one at a time. The AI reads the previous answer and optionally asks
one contextual follow-up before moving to the next question.

**The 5 core debrief questions:**

**Q1 — The honest reason**
> "In your own words, why did we lose this deal? Don't worry about the official
> loss reason — what actually happened?"
*(Free text. Max 500 characters. The AI uses this to inform all subsequent questions.)*

**Q2 — The competitive dynamic**
> "Did a competitor win this deal? If yes, who — and what did the prospect say
> the competitor did better?"
- Yes, [free text competitor name + what they said]
- We lost to "no decision" / project was cancelled
- We lost internally (budget cut, priority shift, champion left)
- Unknown

*(If a competitor is named, the AI asks one follow-up: "Was this competitor in
the deal from the start, or did they come in late? And did we have a chance to
respond to their differentiation?")*

**Q3 — The moment it turned**
> "Looking back, was there a specific moment or interaction where you felt the
> deal change direction? What happened?"
*(Free text. Optional — rep can skip. AI uses this for coaching flag logic.)*

**Q4 — What we were missing**
> "Was there a product feature, integration, pricing model, or contract term that
> the prospect explicitly said we couldn't match? Be specific if you can."
*(Free text. This feeds directly into the Competitive_Intel__c and product feedback
routing logic.)*

**Q5 — What you'd do differently**
> "If you ran this deal again from the start, what's the one thing you'd change?"
*(Free text. Used for coaching summaries for managers — never shared with prospects
or externally.)*

**AI follow-up logic:**
The debrief uses Prompt Builder (Salesforce's native LLM prompt configuration tool)
with the following context injected at runtime:
- `Opportunity.Name`, `Amount`, `CloseDate`, `Account.Industry`
- The rep's answers to each previous question
- `Opportunity.Description` and last 3 Activity records from Einstein Activity Capture
- Named contacts on the Opportunity and their `Role` (Economic Buyer, Champion, etc.)

The AI may ask one follow-up after Q1, Q2, or Q4 — not all three, and never
more than one per question. The follow-up must be a clarifying question, not a
leading one. Maximum 1 follow-up per debrief session.

Acceptance criteria:
- Full debrief (5 questions, 0 follow-ups) completable in ≤ 4 minutes
- Full debrief (5 questions, 1 follow-up) completable in ≤ 6 minutes
- Rep can navigate back to edit a previous answer before submitting
- Submitting is a single confirm action: "Submit Debrief"
- On submit: `Debrief_Completed__c` set to `true`, `Debrief_Completed_Date__c`
  set to NOW(), debrief content stored in `Deal_Debrief__c` custom object
- Rep receives confirmation: "Thanks — this goes to [Competitive Enablement
  Owner name] and your manager. Your answers help the product team too."

---

### FR-03 — Competitive Intelligence Record Creation (Critical)

On debrief submission, the AI analyzes the rep's answers and automatically
creates a `Competitive_Intel__c` record if any of the following conditions are met:
- A competitor name is mentioned in Q2
- A specific product feature gap is mentioned in Q4
- The debrief content contains language matching a pre-configured competitor
  keyword list (managed by Competitive Enablement team in a Custom Setting)

**`Competitive_Intel__c` record fields populated automatically:**

| Field | Source |
|---|---|
| `Competitor_Name__c` | Extracted from Q2 via NLP entity recognition |
| `Win_Reason_Claimed__c` | Extracted from Q2 follow-up |
| `Feature_Gap_Described__c` | Extracted from Q4 |
| `Deal_Size__c` | Pulled from `Opportunity.Amount` |
| `Industry__c` | Pulled from `Opportunity.Account.Industry` |
| `Segment__c` | Derived from `Account.NumberOfEmployees` (SMB/Mid-Market/Enterprise) |
| `Source_Opportunity__c` | Lookup to the Opportunity |
| `Assigned_To__c` | Auto-assigned to Competitive Enablement Owner for that competitor (from `Competitor_Owner_Map__mdt` custom metadata) |
| `Status__c` | `New` on creation |
| `AI_Confidence_Score__c` | 0–100 score indicating extraction confidence; low-confidence records flagged for manual review |

**Routing logic:**
- `AI_Confidence_Score__c >= 75`: Record created and auto-assigned, no human
  review required before it appears in the Competitive Intel dashboard
- `AI_Confidence_Score__c < 75`: Record created with `Status__c = 'Needs Review'`;
  Competitive Enablement Owner receives notification to validate before it's
  included in aggregate reporting
- No competitor identified: No `Competitive_Intel__c` record created; debrief
  is still saved for manager coaching use

Acceptance criteria:
- Record created within 5 minutes of debrief submission
- Competitor extraction accuracy ≥ 80% on a test set of 100 historical debriefs
  (validated by Competitive Enablement team pre-launch)
- Routing to correct Competitive Enablement Owner: 100% accuracy
  (deterministic lookup, not AI-driven)
- All records have audit trail: `Extracted_By__c = 'Einstein Deal Loss Intelligence'`

---

### FR-04 — Manager Coaching Summary (High)

Within 24 hours of a debrief submission, the rep's direct manager receives a
structured coaching summary in Slack or as a Salesforce Task.

**Coaching summary contains:**

1. One-paragraph plain-language summary of what happened in the deal (generated
   from Q1, Q3, Q5 — never the competitive intel fields which are routed separately)

2. One coaching flag if applicable — drawn from a set of 8 pre-defined patterns:
   - **No Economic Buyer identified** — rep's answer to Q3 or Q5 indicates
     the champion couldn't get internal buy-in
   - **Late competitive entry** — competitor came in after Stage 3; rep didn't
     have a response prepared
   - **Pricing anchored early** — rep mentioned price as the turning point in Q3
     without a value conversation preceding it
   - **No multi-threading** — deal had only 1 contact role populated on the
     Opportunity at close
   - **Stalled in procurement** — close date was pushed 3+ times in the last
     60 days
   - **Champion went dark** — last Activity with primary Champion contact is
     > 21 days before CloseDate
   - **Deal too large for stage** — Amount is > 2x the rep's average ACV;
     may need different qualification approach
   - **No coaching flag identified** — deal appears to have been lost to factors
     outside rep control

3. One suggested 1:1 talking point for the manager
   *(e.g. "Ask [Rep Name] what they'd do differently if the champion had executive
   access earlier — their answer to Q5 suggests they saw this coming.")*

Acceptance criteria:
- Coaching summary generated within 24 hours of debrief submission
- Coaching flag logic is rule-based (deterministic), not LLM-generated,
  to ensure consistency and auditability
- Manager can mark coaching summary as "Discussed in 1:1" — logged on the
  `Deal_Debrief__c` record
- Reps cannot see their own coaching flag; they see only their submitted answers

---

### FR-05 — Aggregate Loss Intelligence Dashboard (High)

A Tableau CRM (Einstein Analytics) dashboard visible to Sales VP, RevOps, and
Product Management with the following views:

**View 1 — Loss Reasons (Actual vs. Reported)**
Side-by-side comparison of:
- What reps selected in `Close_Reason__c` picklist (legacy)
- What AI extracted as the actual loss reason from debrief text (new)
This view makes the data quality problem visible and demonstrates ROI of the feature.

**View 2 — Competitive Loss Heatmap**
Matrix: Competitor (rows) × Segment/Industry (columns) × Deal count (cell value)
Filterable by date range, rep, and region.
Click-through to underlying `Competitive_Intel__c` records.

**View 3 — Feature Gap Analysis**
Ranked list of product feature gaps mentioned in Q4, grouped by frequency.
Filterable by competitor and segment.
Designed to feed directly into quarterly product roadmap reviews.

**View 4 — Coaching Flag Distribution**
By rep (anonymized for non-managers) and by team.
Shows which coaching flags are most common — a signal for sales enablement to
build targeted training content.

**View 5 — Debrief Completion Rate**
By rep, team, and manager. Identifies managers whose teams are not completing
debriefs — a leading indicator of data quality degradation.

Acceptance criteria:
- Dashboard refreshes every 24 hours (not real-time)
- Rep-level data visible only to that rep's manager chain
- Aggregate data (no rep attribution) visible to Product and Competitive Enablement
- CSV export available for all views
- Dashboard is a managed package component — installs via AppExchange bundle

---

### FR-06 — Admin Configuration (Medium)

A dedicated Setup page (`Einstein Deal Loss Intelligence Settings`) accessible
to System Administrators with the following configurable options:

| Setting | Default | Description |
|---|---|---|
| Minimum Opportunity Amount | $10,000 | Deals below this threshold do not trigger debrief |
| Debrief Expiry Window | 7 days | Days after close before debrief link expires |
| Reminder Timing | Day 4 | When the single reminder notification is sent |
| Notification Channel | Slack (if connected), else in-app | Where the debrief prompt is delivered |
| Excluded Record Types | Renewal | Opportunity record types that bypass the trigger |
| Coaching Summary Delivery | 24 hours | Delay between debrief submission and manager summary |
| Competitor Keyword List | Managed via Custom Setting | List of known competitors; triggers NLP entity matching |
| Competitor Owner Mapping | Managed via Custom Metadata (`Competitor_Owner_Map__mdt`) | Maps each competitor to a Competitive Enablement owner |

Acceptance criteria:
- All settings configurable without code changes (Flow/metadata only)
- Changes take effect on the next trigger fire (no retroactive processing)
- Admin can run a test debrief on any historical Closed Lost Opportunity to
  validate the extraction output before go-live

---

## Out of Scope — v1

- **Automated outreach to the lost prospect.** This PRD deliberately does not
  include any feature that uses the debrief content to re-engage the prospect.
  Loss intelligence is for internal use only in v1.
- **Real-time competitive alerting.** If a new competitor pattern is detected
  mid-quarter, v1 does not push alerts to active deals. That is a v2 use case
  requiring Data Cloud segmentation on open Opportunities.
- **Integration with third-party win/loss platforms** (Klue, Crayon, Gong Engage).
  The API surface for these integrations is defined in v1 (via MuleSoft-ready
  `Competitive_Intel__c` webhook), but the managed integrations ship in v2.
- **Video or voice debrief.** Text-only in v1. A voice debrief via Agentforce
  Voice is on the v2 roadmap.
- **Automated battlecard updates.** The feature routes intelligence to competitive
  enablement owners — it does not update Highspot, Seismic, or Salesforce Files
  records automatically. That workflow is owned by the enablement team.
- **Deals below $10,000.** High-volume, low-ACV pipelines (SDR-driven, SMB transactional)
  generate too much noise for structured debrief. A separate lightweight version
  for transactional sales is a future consideration.

---

## Technical Considerations

*Note: These are product-level constraints, not an engineering spec.*

**Trigger architecture:**
The Flow Builder record-triggered flow fires on `Opportunity` update when
`StageName EQUALS Closed Lost AND ISCHANGED(StageName) = true`. It calls an
invocable Apex action that handles the async debrief initiation (Apex is required
because the 2-hour delay and Slack integration exceed Flow's native async
capabilities for this complexity level).

**LLM layer — Prompt Builder:**
Salesforce's native Prompt Builder (GA as of Winter '24) handles the debrief
question generation and answer analysis. Prompt templates are stored as
`PromptTemplate` metadata and versioned. The competitive extraction prompt uses
a structured output format (JSON) validated server-side before writing to
`Competitive_Intel__c`.

**Data model — new custom objects:**

```
Deal_Debrief__c
  Opportunity__c          (Lookup)
  Rep_Id__c               (Lookup to User)
  Q1_Response__c          (Long Text Area, 500)
  Q2_Response__c          (Long Text Area, 500)
  Q2_Followup__c          (Long Text Area, 500)
  Q3_Response__c          (Long Text Area, 500)
  Q4_Response__c          (Long Text Area, 500)
  Q5_Response__c          (Long Text Area, 500)
  Debrief_Completed__c    (Checkbox)
  Debrief_Status__c       (Picklist: Pending / Completed / Expired / Skipped)
  Completion_Time_Min__c  (Number — minutes to complete)
  AI_Summary__c           (Long Text Area — generated summary for manager)
  Coaching_Flag__c        (Picklist — 8 flags + 'None')

Competitive_Intel__c
  Source_Opportunity__c   (Lookup)
  Source_Debrief__c       (Lookup to Deal_Debrief__c)
  Competitor_Name__c      (Text)
  Win_Reason_Claimed__c   (Long Text Area)
  Feature_Gap_Described__c (Long Text Area)
  Deal_Size__c            (Currency)
  Industry__c             (Text)
  Segment__c              (Picklist: SMB / Mid-Market / Enterprise)
  Assigned_To__c          (Lookup to User)
  Status__c               (Picklist: New / In Review / Actioned / Archived)
  AI_Confidence_Score__c  (Number 0-100)
```

**Privacy and data governance:**
- Debrief content is stored in Salesforce org — no data leaves to external LLM
  providers unless the org uses Einstein Trust Layer (zero data retention policy)
- `Q5_Response__c` ("what you'd do differently") is explicitly excluded from
  all reports visible to anyone below VP level — it is a coaching tool, not
  a performance metric
- GDPR: If the rep leaves the company and their User record is deactivated,
  `Deal_Debrief__c` records are retained but `Rep_Id__c` is anonymized on a
  90-day schedule

**Gong / Einstein Conversation Intelligence integration (optional):**
If the org has Gong or Einstein Conversation Intelligence connected, the debrief
trigger pulls the last call transcript from the Opportunity's activity timeline
and includes it as additional context in the extraction prompt. This is additive —
the feature works without it.

---

## Edge Cases

| Scenario | Handling |
|---|---|
| Rep marks deal Closed Lost and immediately re-opens it (data entry error) | Debrief notification is sent but debrief link is invalidated when `StageName` changes away from Closed Lost; `Debrief_Status__c` set to 'Cancelled' |
| Rep completes debrief but provides one-word or clearly low-effort answers | `AI_Confidence_Score__c` will be low (< 50); Competitive Intel record created as 'Needs Review'; manager coaching summary notes "Low-detail debrief — may warrant a conversation" |
| Two reps co-own the Opportunity (split credit) | Debrief sent to the rep listed as `Opportunity.OwnerId`; the second rep receives a Chatter notification asking if they want to add context |
| Competitor name in debrief doesn't match any entry in `Competitor_Owner_Map__mdt` | `Competitive_Intel__c` record created with `Assigned_To__c` = RevOps admin; admin receives notification to add the new competitor to the mapping |
| Rep submits debrief in a language other than English | LLM handles multi-language extraction; `Competitive_Intel__c` content stored in original language with an English AI summary in a separate field |
| Manager is also the rep (player-coach) | Coaching summary is routed to the manager's own manager in the role hierarchy |
| Opportunity has no associated Contacts | Debrief still proceeds; contact-dependent coaching flags (e.g. "Champion went dark") are suppressed for that debrief |

---

## Open Questions

- [ ] **Rep opt-out:** Should reps be able to permanently opt out of debriefs?
  Recommendation: No opt-out, but managers can waive individual debriefs. Making
  it opt-out will destroy completion rates (see Gartner: voluntary CRM data entry
  completion rates average 34%).

- [ ] **Compensation sensitivity:** Can debrief content be used in performance
  reviews or compensation decisions? Recommendation: Explicitly no — and this must
  be stated in the rep-facing UI and internal policy. If reps believe debriefs
  affect comp, they will game them. The value of the product depends entirely on
  honest answers.

- [ ] **AppExchange vs. core product:** Should this ship as a managed AppExchange
  package (faster to market, easier to monetize separately) or as a native
  Einstein feature (harder to build, but higher penetration)? The custom object
  data model and Prompt Builder approach support either path.

- [ ] **Debrief for won deals too:** Should a lighter version of the debrief run
  on Closed Won deals? (What almost stopped us from winning? Which competitors
  did we beat and why?) This is high-value for competitive enablement but doubles
  the scope. Flagged for v2.

- [ ] **Manager coaching summary opt-in:** Some sales cultures will view manager
  visibility into debrief content as surveillance. Should managers have to opt in
  to receive coaching summaries? Recommendation: opt-out (default on), with
  mandatory manager acknowledgment of the "no comp use" policy before first access.

---

## Appendix A — Prompt Design Notes

### Competitive Intelligence Extraction Prompt (draft)

```
You are a competitive intelligence analyst. A sales rep at [Company Name] has
just completed a deal debrief for a lost opportunity.

Opportunity context:
- Account: {Opportunity.Account.Name}
- Industry: {Opportunity.Account.Industry}
- Deal size: {Opportunity.Amount}
- Close date: {Opportunity.CloseDate}
- Rep's stated reason: {Opportunity.Close_Reason__c}

Rep's debrief answers:
Q1 (What really happened): {Q1_Response}
Q2 (Competitor): {Q2_Response}
Q2 follow-up: {Q2_Followup}
Q4 (What we were missing): {Q4_Response}

Your task:
1. Identify the primary competitor named (if any). Use the exact name as stated
   by the rep — do not normalize or infer.
2. Extract the most specific product/feature gap mentioned in Q4. Quote the rep's
   exact words where possible.
3. Classify the win reason the competitor claimed (pick one):
   Price | Product Feature | Ease of Use | Support/Service | Relationship |
   Implementation Speed | Contract Terms | Unknown
4. Assign a confidence score (0-100) based on how specific and complete the
   rep's answers are. Low specificity = low confidence.

Return ONLY valid JSON:
{
  "competitor_name": "string or null",
  "feature_gap": "string or null — rep's words preferred",
  "win_reason_category": "string from list above",
  "confidence_score": integer,
  "extraction_notes": "string — 1 sentence explaining confidence score"
}
```

### Manager Coaching Summary Prompt (draft)

```
You are a sales manager assistant. A sales rep has completed a deal debrief.
Write a 2-3 sentence plain-language summary of what happened in this deal,
suitable for a sales manager to read before a 1:1 conversation with the rep.

Use the rep's own words where possible. Do not editorialize or assign blame.
Do not mention compensation, performance ratings, or company policy.
Do not include the Q5 answer ("what I'd do differently") — that is surfaced
separately as a coaching talking point.

Rep answers:
Q1: {Q1_Response}
Q2: {Q2_Response}
Q3: {Q3_Response}
Q4: {Q4_Response}

Opportunity: {Opportunity.Name}, {Opportunity.Amount}, lost on {Opportunity.CloseDate}

Return a plain paragraph. No JSON. No bullet points. No headings.
Max 75 words.
```

---

## Appendix B — Why This Isn't Already Solved

| Existing Solution | What It Does | Why It Doesn't Solve This |
|---|---|---|
| `Close_Reason__c` picklist | Rep selects a pre-set reason | Reps pick defensively; no AI validation; no structured text capture |
| Einstein Opportunity Insights | Flags at-risk *open* deals | Does not activate on Closed Lost; no post-loss analysis |
| Agentforce Sales Coach | Pitch practice before calls | Forward-looking only; no retrospective debrief capability |
| Gong / Conversation Intelligence | Transcribes and analyzes calls | Only captures what was said on recorded calls; misses email and rep context; no Salesforce-native routing |
| Klue / Crayon (competitive intel tools) | Aggregates competitive content | External tools; no native connection to individual deal outcomes; require manual intel entry |
| Win/Loss interview services (e.g. Primary Intelligence) | Professional post-sale interviews | Expensive, slow (2–4 weeks delay), no CRM integration, high rep burden |
| Manual rep debrief email to manager | Ad-hoc narrative | No structure, no routing, no aggregation, completion rate < 15% |

The gap is clear: there is no native Salesforce mechanism that (a) activates
automatically on deal loss, (b) captures structured qualitative intelligence
from the rep, (c) extracts and routes competitive data, and (d) aggregates
patterns for product and leadership — all within the Salesforce platform, with
no new tools required.

---

*This document was written as a portfolio case study. All referenced Einstein and
Agentforce capabilities are based on Salesforce's publicly available documentation
as of Spring '25. Baseline research metrics are sourced from Gartner, Forrester,
CSO Insights, and Salesforce's State of Sales 2024 report.*
