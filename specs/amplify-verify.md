# PRD: Amplify Verify — AI Business Case Assumption Intelligence

**Status:** Case Study — Portfolio  
**Author:** Blake Doerring 
**Last Updated:** March 2026  
**Document Type:** Product Requirements Document  
**Intended Audience:** Hiring managers, product teams, strategy execution professionals

---

> **Case study framing:** This PRD identifies and addresses a specific gap in
> Amplify Now's current platform based on publicly available product documentation,
> published research by the company's founder, and third-party transformation
> research. All Amplify product references are sourced from amplify-now.com.
> Transformation failure statistics are sourced from McKinsey, BCG, and peer-reviewed
> research cited inline.

---

## The Gap This PRD Addresses

Amplify Now's platform is built around execution visibility: dashboards for
executives, program health tracking for Transformation Offices, and initiative
management for project owners. It is, by design, a system that tracks whether
declared value is being delivered.

But the platform currently activates *after* a business case has been approved
and an initiative has been loaded. There is no mechanism within Amplify that
challenges the assumptions in that business case before capital is committed —
before the exaggeration phase that Amplify's own founder describes as the first
stage of value erosion.

Amplify's founder Matt Williams wrote in February 2026:

> *"In early planning stages, the enthusiasm for change combines with the pressure
> to secure funding. Optimism bias takes hold. Strategic ambition is translated
> into financial projections that may rest on high-level assumptions rather than
> operational clarity. Cost matching often follows. The cost of a program is
> estimated, and benefits are retrospectively shaped to justify it."*
> — Matt Williams, "Benefits Realization: Where Value Is Won or Lost," Amplify Blog,
> March 2026

And again in the same month:

> *"Business cases are designed to secure approval. They are built on assumptions.
> They are optimistic by necessity."*
> — Matt Williams, "Lies, Damn Lies, and Business Cases," Amplify Blog,
> February 2026

This is the product gap. Amplify tracks value realization. It does not yet
interrogate value declaration. **Amplify Verify** closes that gap.

---

## Problem Statement

### The optimism bias problem at business case stage

Every initiative loaded into Amplify begins with a business case: a financial
model projecting expected benefits (cost savings, revenue uplift, EBITDA
improvement) over a defined delivery timeline. These projections are the
foundation of every KPI, every dashboard, and every benefits realization
report the platform subsequently produces.

If the projections are inflated — which research and Amplify's own published
thinking suggest they routinely are — the platform faithfully tracks progress
against a false baseline.

This is not a data collection problem. Amplify already collects the data.
It is an assumption validation problem: the platform has no mechanism to
assess whether the benefit assumptions submitted are realistic before they
become the standard against which all future progress is measured.

**The research context:**

McKinsey research shows that large-scale transformation efforts fail approximately 70% of the time, with contributing factors including insufficiently high aspirations, lack of engagement, and insufficient investment in building capabilities to sustain change.

Bain's 2024 analysis found that 88% of business transformations fail to achieve their original ambitions.

McKinsey's analysis of publicly listed companies that went through full-scale transformations found that the potential for value loss begins as early as day one, with the largest share of value lost during implementation — and that on average, about half of a transformation's value is realized within the first 18 months.

The most important finding for this PRD: research from MIT Sloan Management Review found that benefits are not outcomes that automatically occur — the accumulation of benefits lags implementation, and managing for benefits does not stop when technical implementation is completed. Benefits management needs to continue until all expected benefits are realized. But that research also found that organizations rarely conduct post-implementation reviews because they already know many benefits in the business case are unlikely to be achieved.

That last finding is the core insight: **organizations know their business cases are optimistic, but submit them anyway because the alternative is not getting funded.**

This creates a specific, addressable problem. Organizations using Amplify are
accumulating a growing dataset of completed initiatives — actual benefits realized
vs. benefits projected at submission. That dataset is the most valuable benchmark
in the platform, and currently it is not used to challenge incoming business cases.

### The Transformation Office's compounding problem

For Transformation Office leaders managing 50+ initiatives simultaneously —
a core Amplify use case — the consequence of unvalidated business cases is
systematic:

- Initiatives enter the portfolio with overstated benefit projections
- The portfolio's aggregate "expected value" is inflated from day one
- As initiatives progress and actual realization falls short, the TO is
  forced to retrospectively explain why the portfolio is underperforming
- This erodes the TO's credibility with the executive team and the board

This is not a governance failure. It is a data problem at the intake stage
that no current Amplify feature addresses.

---

## Proposed Solution

**Amplify Verify** is an AI layer that activates when a new initiative business
case is submitted into Amplify. It does three things:

**1. Assumption Benchmarking**
When a business case is submitted with projected benefits (e.g. "$4.2M annual
cost savings from process automation, Year 1"), Amplify Verify compares those
projections against:
- The organization's own historical realization rates for similar initiative
  types already completed in Amplify
- Industry and initiative-type benchmarks derived from Amplify's anonymized
  cross-customer dataset (opt-in)

It surfaces a plain-language finding: *"Your organization's last 6 cost
optimization initiatives realized an average of 61% of projected Year 1
savings. This business case projects Year 1 savings at full value. Consider
whether a phased realization curve is more appropriate."*

**2. Assumption Risk Scoring**
Each benefit line item in the submitted business case receives an
`Assumption Risk` rating: Low / Medium / High / Unverified.

Risk is assessed across four dimensions:
- **Historical accuracy** — how similar projections performed in the org's
  own completed initiatives
- **Timeline realism** — whether the projected benefit realization timeline
  matches the initiative's stage gate schedule
- **Dependency completeness** — whether the assumptions reference dependencies
  (headcount changes, system implementations, process redesigns) that are
  either unlogged in Amplify or currently at-risk
- **Benefit type precedent** — whether this type of benefit (e.g. "productivity
  uplift from new tooling") has been claimed before in the organization and
  whether those prior claims were ever validated

**3. Executive Confidence Score**
The business case receives an overall `Value Confidence Score` (0–100) visible
to the Transformation Office before the initiative is submitted to the investment
committee. This score is not a pass/fail gate — it is a conversation starter.
The TO can submit a low-confidence business case with documented rationale,
but they do so with visibility rather than in the dark.

---

## Target Users

| Persona | Amplify Role | What They Get from Amplify Verify |
|---|---|---|
| Initiative Owner | Initiative Owner | Pre-submission feedback on which benefit assumptions are most at risk, with suggestions to strengthen or reframe them |
| Transformation Office Lead | Transformation Office | Portfolio-level view of incoming business case quality; flags high-risk assumptions before they inflate the portfolio baseline |
| CFO / Finance | Executive | Confidence score on business cases before investment committee review; replaces the implicit "discount by 30%" mental model with data |
| CEO / Executive Sponsor | Executive | Dashboard view of assumption accuracy trend — are business cases getting better or worse over time? |
| Investment Committee | Executive | Structured assumption risk summary attached to every business case submission — no extra work required |

---

## Goals & Success Metrics

### Primary Goal
Reduce the gap between projected and realized benefits across the Amplify
portfolio by improving the quality of business case assumptions at intake.

| Metric | Baseline | Target | Measurement |
|---|---|---|---|
| Average benefits realization rate (actual vs. projected at submission) | Established from customer baseline data at onboarding | +15 percentage points above baseline within 4 quarters of feature adoption | Amplify benefits tracking module — actual vs. submitted projection |
| Business cases revised after Verify feedback before submission | 0% (no mechanism exists) | ≥ 40% of cases with High-risk assumptions are revised | `Assumption_Revised__c` flag on business case record |
| Time from business case submission to investment committee review | Varies by org | No change — Verify must not add friction to submission timeline | Submission timestamp → committee review date |
| Executive confidence in portfolio baseline (quarterly survey) | Establish baseline at launch | ≥ 20% improvement at 6 months | Quarterly NPS-style survey to TO and CFO users |

### Secondary Goal
Build a proprietary industry dataset that makes Amplify's benchmarking
more valuable over time — a compounding competitive moat.

| Metric | Baseline | Target | Measurement |
|---|---|---|---|
| Customers opted into anonymized benchmark pool | 0 (feature doesn't exist) | ≥ 60% of new customers opt in within 90 days of feature launch | Opt-in tracking in settings |
| Benefit projection accuracy by initiative type (benchmark coverage) | No data | Benchmarks covering ≥ 8 initiative types within 12 months of launch | Internal data coverage report |

---

## Functional Requirements

### FR-01 — Business Case Intake Integration (Critical)

Amplify Verify activates when a new initiative's `Project Brief` is submitted
with at least one financial benefit line item populated. This is a non-blocking
step — the Initiative Owner can submit the business case with or without engaging
the Verify output. The feature does not gate progress.

**Trigger conditions (all must be met):**
- `Project.Status` changes to `Submitted for Review` or equivalent
- At least one `Benefits_Line_Item__c` record exists with:
  - A `Benefit_Type` selected (e.g. Cost Saving, Revenue Uplift, Productivity
    Improvement, Risk Reduction)
  - A projected value (`Projected_Annual_Value`) greater than zero
  - A `Realization_Start_Date` defined

**Trigger exclusions:**
- Initiatives with `Project_Type = 'BAU'` (business-as-usual) — these are not
  strategic transformation initiatives and lack the benefit structure Verify
  requires
- Initiatives with `Total_Investment < $50,000` — below this threshold the
  overhead of Verify feedback is disproportionate
- Initiatives already completed or closed

**Output timing:**
Verify analysis completes within 60 seconds of submission trigger. The Initiative
Owner and the TO Lead are notified via Amplify's in-platform notification system.
The result does not delay the submission — it is surfaced alongside the submitted
business case, not before it.

Acceptance criteria:
- Verify output available within 60 seconds on 95% of submissions
- Fallback behavior if analysis cannot complete (e.g. insufficient comparable
  data): display "Insufficient historical data for this initiative type —
  consider requesting a comparable benchmark from your TO Lead" rather than
  a failed or empty state
- Admin can disable Verify for specific initiative types or programs via settings

---

### FR-02 — Assumption Benchmarking Engine (Critical)

For each benefit line item submitted, Verify compares the projection against
two data sources in priority order:

**Source 1 — Organizational history (preferred)**
The organization's own completed initiatives in Amplify, filtered by:
- Same `Benefit_Type`
- Same `Initiative_Category` (e.g. Process Automation, Technology Implementation,
  Organizational Restructure, M&A Integration)
- Completed within the last 36 months

The output is:
- `Org_Avg_Realization_Rate` — average % of projected Year 1 benefit
  actually achieved in comparable completed initiatives
- `Comparable_Initiative_Count` — number of initiatives used in the calculation
  (displayed to the user for transparency)
- `Realization_Range` — min/max range of outcomes, not just average

If fewer than 3 comparable initiatives exist in organizational history,
the system supplements with Source 2.

**Source 2 — Industry benchmark pool (supplementary)**
Anonymized, aggregated data from opted-in Amplify customers, filtered by:
- Same `Benefit_Type`
- Same `Industry` (from customer account settings)
- Same `Initiative_Category`

This data is stored and maintained by Amplify, never attributable to individual
customers. Opt-in is managed at the organization level in account settings.

**Benchmark output displayed to user:**

```
Benefit: Cost savings from headcount reduction — $2.1M Year 1

Your organization's comparable initiatives (n=4):
  Average Year 1 realization: 58%
  Range: 42% – 79%
  Typical realization timeline: 14–18 months from go-live

Industry benchmark (n=23, Financial Services, Org Restructure):
  Average Year 1 realization: 51%
  Range: 31% – 74%

Note: Your projection assumes 100% Year 1 realization.
This is above the historical range for your organization
and the industry benchmark. Consider whether a phased
realization curve (e.g. 40% Year 1, 80% Year 2) better
reflects operational reality.
```

Acceptance criteria:
- Benchmark comparisons use only completed initiatives
  (`Project.Status = 'Completed'` or `'Closed'`)
- Minimum sample size of 3 before any benchmark is surfaced
  (smaller samples produce unreliable averages and could be
  gamed or misleading)
- Benchmark data refreshed nightly — not real-time
- Industry benchmark pool data is never attributable to specific
  customers and is never displayed at individual customer level
- Initiative Owner can flag a benchmark as "not comparable" with
  a free-text reason, which feeds back into benchmark quality
  improvement

---

### FR-03 — Assumption Risk Scoring (Critical)

Each benefit line item receives an `Assumption Risk` rating based on
four automated checks:

**Check 1 — Historical accuracy (from FR-02 data)**
```
If Projected_Value > Org_Avg_Realization_Rate × 1.2:
  Risk = High
If Projected_Value > Org_Avg_Realization_Rate × 1.1:
  Risk = Medium
If Projected_Value within Org_Avg_Realization_Rate ± 10%:
  Risk = Low
If Comparable_Initiative_Count < 3 AND no industry benchmark:
  Risk = Unverified
```

**Check 2 — Timeline realism**
The system compares `Benefit.Realization_Start_Date` against the initiative's
stage gate schedule in Amplify:

- If the earliest benefit is projected to realize before the
  final delivery stage gate: `Timeline_Risk = High`
- If the benefit start date falls within 30 days of the final
  stage gate: `Timeline_Risk = Medium`
- Otherwise: `Timeline_Risk = Low`

**Check 3 — Dependency completeness**
The system checks whether the business case references dependencies in Amplify:

- Are the dependencies logged as linked projects in Amplify?
- If logged, are any dependencies currently `Status = 'At Risk'`
  or `'Delayed'`?
- If a dependency is delayed, the projected benefit start date
  is automatically flagged as unrealistic

**Check 4 — Benefit type precedent**
Has this benefit type been claimed in a previous business case by
this organization? If yes, was the benefit validated at project close?

- `Benefit_Type` claimed before, realization validated: precedent positive
- `Benefit_Type` claimed before, realization not validated at close: flag
  with "This benefit type was claimed in [N] previous initiatives but was
  not formally validated at close. Consider how realization will be measured."
- `Benefit_Type` never claimed before: `Risk = Unverified`

**Composite `Assumption_Risk` per benefit line:**
The four checks combine to produce a single risk level:
- Any `High` check → composite `High`
- Two or more `Medium` checks → composite `Medium-High`
- One `Medium` check, all others Low/Unverified → composite `Medium`
- All `Low` or `Unverified` → composite `Low` or `Unverified`

**Display:**
Risk ratings appear inline in the Project Brief alongside each benefit line item.
A `Risk` badge (color-coded: green/amber/red/grey) sits next to the projected
value. Clicking expands the detail: which checks fired and why.

Acceptance criteria:
- Risk scoring completes as part of the 60-second Verify analysis (FR-01)
- All risk logic is rule-based and deterministic — no LLM in the risk scoring
  itself, which ensures consistency and auditability
- Risk rating logic is documented in Amplify's admin help center so TOs
  can explain ratings to Initiative Owners without ambiguity
- Initiative Owner can provide a written rebuttal for any High risk rating;
  the rebuttal is attached to the business case record and visible to the TO
  and investment committee

---

### FR-04 — Value Confidence Score (High)

The business case receives an overall `Value Confidence Score` (VCS), a
0–100 composite score displayed prominently at the top of the Project Brief.

**VCS calculation:**

```
VCS = weighted average of all benefit line item scores

Per benefit line item score:
  Assumption_Risk = Low:         100 points
  Assumption_Risk = Medium:       65 points
  Assumption_Risk = Medium-High:  40 points
  Assumption_Risk = High:         15 points
  Assumption_Risk = Unverified:   50 points (neutral — not penalized for
                                  being new, but not credited as low-risk)

Weight per line item = Projected_Annual_Value of that line
                       / Total_Projected_Annual_Value of all lines

(Higher-value benefit lines have more weight in the overall score)
```

**VCS display tiers:**

| Score | Label | Color | Meaning |
|---|---|---|---|
| 80–100 | High confidence | Green | Assumptions are consistent with historical realization patterns |
| 60–79 | Moderate confidence | Amber | Some assumptions warrant review before investment committee |
| 40–59 | Low confidence | Orange | Material assumptions are at high risk; recommend revision or documented rationale |
| 0–39 | Requires attention | Red | Significant gap between projected and historically achievable value |

**What VCS is not:**
The VCS is explicitly not a pass/fail gate and is never surfaced as a
recommendation to approve or reject an initiative. It is a data tool for
the TO and investment committee, not an automated decision-maker.

This distinction is communicated in the UI: below every VCS display:
*"This score reflects the consistency of benefit assumptions with historical
data. It does not assess strategic priority, organizational readiness, or
executive judgment — all of which remain with the investment committee."*

Acceptance criteria:
- VCS displayed on Project Brief for all initiatives that complete Verify analysis
- VCS visible to: Initiative Owner, TO Lead, Executive Dashboard users
- VCS historical trend visible to TO Lead (how has average incoming VCS changed
  over time? Are business cases improving?)
- VCS not visible in any exported report that goes to the initiative's
  external stakeholders (clients, board advisors, etc.) — internal tool only
- Admin can toggle VCS display on/off per program type

---

### FR-05 — Revision Workflow (High)

When an Initiative Owner's business case receives a `High` Assumption Risk
on any benefit line, or a VCS below 60, Verify surfaces a structured revision
prompt — not a rejection.

**Revision options surfaced per High-risk benefit:**

1. **Reforecast using realization curve**
   One-click to apply the organization's historical average realization
   curve to the projected benefit (e.g. auto-populate 58% in Year 1,
   82% in Year 2, 100% in Year 3 based on org history). The original
   projection is preserved in a `Submitted_Projection` field for audit.

2. **Document rationale for higher projection**
   Free-text field: "Why does this initiative's benefit projection exceed
   historical benchmarks?" The rationale is attached to the benefit record
   and surfaces in the investment committee view. Forces the Initiative
   Owner to articulate — not just submit — their optimistic assumption.

3. **Mark as pilot with post-delivery validation gate**
   For novel benefit types with no precedent (`Unverified`), the Initiative
   Owner can flag the benefit as "pilot — validate at Stage Gate 3." This
   adds an automatic `Benefit_Validation_Task` to the initiative's Stage
   Gate 3 checklist in Amplify, ensuring the claim is revisited at the
   appropriate point in delivery.

4. **Proceed without revision**
   The business case can always be submitted as-is. The Verify output is
   preserved on the record — a permanent, visible audit trail that the
   assumption risk was flagged and not addressed.

Acceptance criteria:
- Revision options presented inline in Project Brief; no new page or modal required
- Reforecast applies immediately to the projected value with a visual before/after
  comparison; Initiative Owner can undo
- Free-text rationale is mandatory if proceeding without revision on a
  `High` risk item (cannot be blank — minimum 50 characters)
- All revisions and their timestamps are logged on the `Deal_Debrief__c`
  equivalent `Verify_Audit_Log__c` child record
- TO Lead receives a notification summary of all revision decisions made
  before a business case enters the investment committee queue

---

### FR-06 — Portfolio Assumption Intelligence Dashboard (Medium)

A dedicated Amplify Verify dashboard for TO Leads and CFOs showing aggregate
trends across the portfolio's incoming business cases.

**View 1 — VCS Trend Over Time**
Line chart: average VCS of submitted business cases by quarter.
The goal is a rising trend — it means business cases are improving in quality
over time as Initiative Owners internalize Verify feedback.

**View 2 — Assumption Risk by Benefit Type**
Heat map: `Benefit_Type` (rows) × `Assumption_Risk` level (columns) × count.
Identifies which benefit types are systematically over-projected in the
organization. (e.g. "Productivity improvement from technology implementation
is consistently rated High Risk — this org has never achieved projected
productivity benefits from tech programs.")

**View 3 — Realization Gap: Projected vs. Actual (Completed Initiatives)**
For all completed initiatives, show projected vs. actual benefit by
`Initiative_Category`. This is the foundational data that feeds the
benchmarking engine — making it visible gives the TO evidence to use in
investment committee and board conversations.

**View 4 — Revision Rate by Initiative Owner**
Which Initiative Owners revised their business cases after Verify feedback,
and which proceeded without revision? This is a leading indicator of
organizational assumption discipline — not a performance metric (revision
is not always the right answer), but a signal for the TO about where
coaching conversations may be valuable.

Acceptance criteria:
- Dashboard refreshes nightly
- Filters: date range, initiative category, initiative owner, program
- All views exportable to CSV for board reporting
- View 4 (revision rate by owner) visible only to TO Lead and above —
  not to peers

---

## Out of Scope — v1

- **AI-generated business case writing.** Verify analyzes and challenges
  business cases — it does not generate them. A future feature could assist
  Initiative Owners in structuring assumptions more rigorously, but that is
  a v2 capability.
- **Real-time market benchmarks.** The benchmark pool is derived from Amplify
  customers' historical data. External market data (industry reports, consultant
  benchmarks, public M&A synergy databases) is not integrated in v1.
- **Automatic benefit target adjustment.** Verify never modifies the projected
  value in a submitted business case without explicit Initiative Owner action.
  The revision tools (FR-05) are all opt-in.
- **Predictive project outcome scoring.** Verify assesses the business case
  assumptions at intake, not the probability of delivery success. Predicting
  delivery outcomes (schedule, cost, scope risk) is a separate capability in
  Amplify's risk management module.
- **Integration with external financial planning tools** (Anaplan, Adaptive
  Insights, Oracle EPM). The data exchange API is designed for v2 integration,
  but in v1 all benefit data is entered directly into Amplify's Project Brief.

---

## Technical Considerations

*Note: These are product-level constraints, not an engineering spec.*

**Data model additions:**

```
Benefits_Line_Item__c (extension of existing record)
  Assumption_Risk__c          (Picklist: Low / Medium / Medium-High /
                               High / Unverified)
  VCS_Score__c                (Number 0–100)
  Org_Avg_Realization_Rate__c (Percent — from benchmark engine)
  Comparable_Count__c         (Integer — transparency field)
  Risk_Check_Detail__c        (Long Text — JSON of 4-check results)
  Revision_Action__c          (Picklist: None / Reforecast Applied /
                               Rationale Documented / Pilot Flagged /
                               Proceeded Without Revision)
  Revision_Rationale__c       (Long Text — mandatory if proceeding
                               without revision on High risk)

Verify_Audit_Log__c (new child object)
  Initiative__c               (Lookup to Project)
  Verify_Run_Date__c          (DateTime)
  VCS_At_Submission__c        (Number — snapshot, not recalculated)
  High_Risk_Count__c          (Integer)
  Revision_Rate__c            (Percent — what % of High items were revised)
  Analyst_Override__c         (Boolean — TO manually overrode a risk rating)
  Override_Reason__c          (Long Text)
```

**Benchmarking engine:**
The benchmarking query runs server-side on submission. It is a structured
database query against completed initiatives — not an LLM. The LLM is used
only for generating the plain-language explanation text (the "Note: Your
projection assumes..." narrative in FR-02). This separation is intentional:
the scoring logic (FR-03) must be deterministic and auditable; only the
communication layer benefits from natural language generation.

**Data privacy for benchmark pool:**
Customer data contributed to the cross-customer benchmark pool is:
- Aggregated at the benefit type + initiative category level before storage
- Never attributable to a specific customer, organization, or industry region
  more granular than the customer's selected `Industry` setting
- Governed under Amplify's existing data processing agreement with a specific
  addendum for benchmark pool participation
- Deletable: if a customer leaves Amplify or withdraws from the pool,
  their contributed data is removed from aggregate calculations within 30 days

**LLM usage:**
The plain-language explanations generated in FR-02 use Amplify's existing
LLM infrastructure. Prompt inputs: benefit type, projected value, comparable
count, org average realization rate. Output: one to two plain-language
sentences. Max 80 words. No customer-identifying data in the prompt. Cached
for 24 hours per benefit type/projection combination to reduce API costs.

---

## Edge Cases

| Scenario | Handling |
|---|---|
| New Amplify customer with no completed initiatives yet | Verify runs on industry benchmark only; displays "No organizational history yet — showing industry benchmark only (n=X). Your own data will improve this analysis as you complete initiatives." |
| Initiative with a completely novel benefit type (e.g. first sustainability-related benefit claim in the org) | `Assumption_Risk = Unverified`; FR-05 option 3 (pilot with validation gate) is recommended; no penalty to VCS beyond Unverified weighting |
| Initiative Owner submits and immediately revises — does VCS update? | Yes — VCS is recalculated live as benefit line items are edited before final submission. After submission, VCS is locked as a snapshot on `Verify_Audit_Log__c` |
| TO Lead disagrees with a risk rating (e.g. they know this initiative has external guarantees not visible in Amplify) | Analyst override: TO can manually change a risk rating with a mandatory written reason. Override is logged and visible to executive users |
| Completed initiative had benefits that were never formally closed out (realized or not) | These initiatives are excluded from the benchmark pool until a TO Lead marks them with a `Benefits_Close_Status`. Dashboard View 3 flags initiatives missing close-out data. |
| Two benefit line items with identical projections but different `Benefit_Type` | Each is assessed independently; they may receive different risk ratings |

---

## Open Questions

- [ ] **Pricing model:** Is Verify included in all Amplify tiers or a premium
  add-on? Recommendation: include for Enterprise tier as a differentiator for
  the Transformation Office and CFO buyer; offer as an add-on for smaller
  accounts. The feature's value scales with portfolio size — it's most compelling
  to customers with 20+ active initiatives.

- [ ] **Benchmark pool launch strategy:** The cross-customer benchmark pool is
  most valuable when it has sufficient data. What is the minimum customer count
  and initiative count before benchmarks are surfaced (vs. showing only
  organizational history)? Recommendation: minimum 10 opted-in customers and
  50 completed initiatives per initiative category before that category's
  benchmark is activated.

- [ ] **What happens when Verify is consistently wrong?** If Initiative Owners
  find that their High-risk flagged assumptions actually realized fine, they will
  stop trusting the tool. A feedback loop is needed: at initiative close-out,
  Initiative Owners are prompted to rate the Verify feedback's accuracy. This
  feeds into benchmark quality improvement and — eventually — a visible
  track record of Verify's predictive accuracy.

- [ ] **Should Verify run on existing in-flight initiatives?** Running Verify
  retrospectively on active initiatives could surface valuable risk signals
  (e.g. "this initiative's benefits were High-risk at submission and it's now
  6 months in with no realization data"). Valuable for the TO, but could
  create noise or anxiety for Initiative Owners mid-delivery. Flagged for
  post-launch consideration.

- [ ] **Customer communication:** How Amplify frames Verify to customers is
  critical. It must not be positioned as "our AI says your business case is
  wrong." It must be positioned as "we're giving you the data to have a better
  conversation with your investment committee." The language in the UI, the
  onboarding materials, and the sales pitch all need to reflect this framing.

---

## Appendix A — Why This Is Strategically Important for Amplify

Amplify's current competitive positioning is execution visibility: it shows
transformation leaders what is happening across their portfolio in real time.
This is valuable, but it is increasingly table-stakes in the strategy execution
software market. Competitors including Planview, Workfront, and Planisware
offer comparable execution tracking.

Amplify Verify creates a compounding competitive advantage through three mechanisms:

**1. Network effects from the benchmark pool**
Every new customer that completes initiatives in Amplify and opts into the
benchmark pool makes the benchmarking engine more accurate for all other
customers. This is a data flywheel — the more customers use Amplify, the
better Verify becomes, which makes Amplify more valuable, which attracts
more customers. This advantage is unavailable to a point solution or a
consultancy. It requires platform-scale data accumulation.

**2. Switching cost deepening**
Once a customer's historical initiative data is embedded in their Verify
benchmarks, leaving Amplify means losing their most accurate predictor of
future benefit realization. The organizational knowledge about their own
assumption accuracy — built up over years of completed initiatives — lives
in Amplify. This meaningfully deepens retention.

**3. CFO and board-level relevance**
Amplify currently serves Transformation Offices and Initiative Owners.
Verify creates a direct use case for the CFO: structured, data-backed
scrutiny of business case assumptions before capital is committed. This
expands Amplify's buyer profile upward in the organization — from
operational users to financial decision-makers — and increases average
contract value.

As Amplify's founder has written, the difference between transformation
programs that deliver and those that don't is not execution effort — it
is whether value is actively governed from the moment it is declared.
Verify makes that governance possible before the first dollar is spent.

---

## Appendix B — Competitive Landscape

| Competitor | Primary Positioning | Assumption Validation Capability |
|---|---|---|
| Planview | Portfolio and work management for IT and transformation | No business case assumption validation; tracks execution against plan, not plan quality |
| Workfront (Adobe) | Work management for marketing and creative | No strategy execution or benefits realization functionality |
| Planisware | Project and portfolio management for R&D and engineering | Stage gate management but no AI assumption benchmarking |
| Monday.com | Team work management | No enterprise transformation or benefits realization features |
| Challenger (consulting-led) | Bain, McKinsey, BCG transformation advisory | Assumption validation done manually by consultants; no platform, no compounding data, no self-serve |

The gap across all existing solutions: none of them learn from an organization's
own historical benefit realization data to challenge incoming business cases.
They all track forward from an approved plan. Verify challenges the plan
before it is approved — using data the organization already has.

---

*This document was written as a portfolio case study. All Amplify Now product
descriptions are based on publicly available information at amplify-now.com as
of March 2026. Founder quotes are sourced directly from the Amplify blog.
Transformation research statistics are cited from McKinsey.com, Bain & Company,
and MIT Sloan Management Review as referenced inline.*
