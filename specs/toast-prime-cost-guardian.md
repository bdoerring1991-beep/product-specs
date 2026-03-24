# PRD: Toast IQ Prime Cost Guardian
## Proactive Prime Cost Intelligence for Restaurant Operators

**Status:** Case Study — Portfolio
**Author:** Blake Doerring
**Last Updated:** March 2026
**Document Type:** Product Requirements Document
**Intended Audience:** Hiring managers, product teams, restaurant technology professionals

---

> **Case study framing:** This PRD identifies and addresses a specific gap
> in Toast IQ's current AI capabilities based on publicly available product
> information at pos.toasttab.com and Toast's public announcements. Restaurant
> industry economics are sourced from the National Restaurant Association,
> Bloomberg Law, and industry benchmarks as cited inline. This is a portfolio
> case study — not an internal Toast document.

---

## The Gap This PRD Addresses

Toast IQ launched in late 2025 as a conversational AI assistant embedded
in Toast's platform. By operator accounts it is genuinely useful: complex
reports in five seconds, margin analysis for new menu items, beverage
performance summaries, and staff productivity insights on demand.

The operative phrase is "on demand." Toast IQ is a reactive tool. It answers
questions restaurant operators think to ask. It does not watch the single most
important financial ratio in a restaurant's business — prime cost — and alert
the operator when it is drifting toward unprofitable territory before the
damage is done.

This is the gap. Not a missing feature in a menu of features. A structural
limitation in how the product is oriented: it looks backward at what happened,
and it waits for you to ask.

**Toast IQ Prime Cost Guardian** is a proactive monitoring layer built into
Toast IQ that watches prime cost continuously, alerts operators the moment
it drifts past configurable thresholds, explains in plain language which specific
items, shifts, or days are causing the drift, and recommends concrete corrective
actions — all without the operator needing to open a report or type a question.

---

## Why Prime Cost Is the Right Problem to Solve

### The math of restaurant survival

The restaurant industry operates on margins that leave almost no room for
error. The National Restaurant Association estimates that the average restaurant
operates on a pre-tax profit margin of roughly 3–5% of sales. For every $100
in revenue, $3–$5 is profit. Everything else goes to costs.

Two cost lines dominate: food (cost of goods sold, or COGS) and labor. Together,
they are called **prime cost** — the single most important operational metric
in a restaurant. The National Restaurant Association data shows that for a
typical restaurant, food and labor costs each run approximately one-third of
revenue. Prime cost combined — food plus labor — should ideally stay at or
below 65% of sales to support profitability. At 68%, a restaurant with a 5%
profit margin begins losing money. At 70%, the loss is severe.

The NRA's 2025 Restaurant Operations Data Abstract found that full-service
restaurants that reported a pre-tax profit in 2024 ran labor costs at a median
of 34.2% of sales. Full-service operators who reported a pre-tax loss ran
labor costs at 34.1% — a difference of just one-tenth of one percentage point.
The line between profit and loss is that thin.

Food and labor costs have each risen 35% since 2019, according to NRA data.
Average menu prices have risen 31% over the same period — not quite enough
to keep pace. The operational reality for most independent restaurant operators
in 2025 is that they are managing on a razor's edge where a 2-point drift in
prime cost can eliminate the entire profit margin for the week.

### The information timing problem

Here is the paradox Toast IQ does not yet solve: the data that would allow
an operator to catch a prime cost problem early exists in Toast in real time.
Every food cost input, every shift hour worked, every transaction processed
flows through the system. But prime cost visibility typically reaches operators
one of two ways:

**Weekly review (best case):** An operator manually pulls a report at the
end of the week, calculates food cost percentage from purchases divided by
sales, adds their labor cost percentage from the payroll report, and compares
the combined figure to their target. By the time this happens, 7 days of
margin-eroding decisions have already been made — 7 days of shifts over-staffed
relative to actual sales, 7 days of high-cost menu items under-priced.

**Monthly close (common case):** Many independent operators don't have the
time or systems sophistication for weekly prime cost reviews. They see the
damage at the end of the month when their accountant or bookkeeper processes
everything. Corrective action at this point is 3–4 weeks late.

Toast IQ can answer a question about prime cost in five seconds if you know
to ask it. But an operator managing a dinner service on a busy Friday night,
simultaneously seating tables, managing a kitchen ticket backup, and handling
a reservation dispute, is not opening the Toast IQ chat and asking about their
trailing 7-day prime cost percentage. They're running the restaurant.

The Guardian watches the ratio for them. It surfaces the alert when something
needs attention. It tells them what changed and why. It does this whether they
asked or not.

---

## Proposed Solution

**Toast IQ Prime Cost Guardian** is a continuous background monitoring system
embedded in Toast IQ that:

1. Calculates prime cost in real time across three rolling windows (daily,
   weekly, and period-to-date) and compares the result to each operator's
   configured target
2. Sends a proactive alert — through the Toast Now mobile app and in-platform
   notification — the moment prime cost drifts more than a configurable number
   of percentage points above the operator's target
3. Provides a drill-down explanation that identifies the specific driver of
   the drift: which cost line (food or labor), which category within that line,
   which shift, day, or menu item
4. Recommends a specific, actionable response the operator can take within
   that business day to begin correcting the drift

The Guardian is not a report. It is not a dashboard. It is an alert with
context and a suggested action — designed to be read and acted on in 60 seconds
by an operator who is in the middle of running a restaurant.

---

## Target Users

| Persona | Restaurant Type | Current Prime Cost Awareness | What Guardian Changes |
|---|---|---|---|
| Independent owner-operator | Single-location FSR (full-service) | Reviews cost weekly or monthly; discovers problems after the fact | Real-time alert with specific driver — catches drift within 24 hours |
| Multi-unit regional operator | 3–8 location casual chain | Monitors location-by-location; manually compares to benchmarks | Cross-location alert showing which location is drifting and why |
| General Manager (GM) | High-volume QSR or FSR | Runs daily labor check manually; food cost is often a weekly guess | Daily prime cost score surfaced before opening — ahead of the shift |
| Executive Chef | Kitchen-focused, independent FSR | Tracks food cost intuitively; no systematic alert when COGS drifts | Specific item-level COGS alerts when a high-volume item's cost jumps |
| Multi-unit VP of Operations | 10–50 location group | Reviews weekly P&L per location; can't catch real-time drift | Portfolio prime cost view flagging outlier locations requiring attention |

---

## Goals & Success Metrics

### Primary Goal
Reduce prime cost overruns among Toast operators by giving them earlier,
more specific visibility into drift — measured by whether Guardian-enabled
operators correct prime cost problems faster than they did before.

| Metric | Baseline | Target | Measurement |
|---|---|---|---|
| Median days between prime cost drift event and operator corrective action | Establish at launch from historical data | Reduction of 3+ days vs. pre-Guardian baseline | Internal analysis of prime cost trend data before/after Guardian activation |
| Prime cost alert acknowledgment rate (operator opens alert within 24hrs) | N/A | ≥ 70% | Alert engagement analytics in Toast Now app |
| Guardian feature activation rate (% of eligible operators who configure and enable) | N/A | ≥ 40% within 6 months of launch | Feature flag activation data |
| Operator-reported confidence in understanding their prime cost | Establish baseline CSAT at launch | ≥ 20% improvement at 6-month survey | Quarterly product NPS survey with feature attribution |

### Secondary Goal
Deepen the value of Toast IQ as a proactive intelligence layer, not just
a reactive query tool — increasing daily engagement with Toast IQ overall.

| Metric | Baseline | Target | Measurement |
|---|---|---|---|
| Daily active users of Toast IQ among Guardian-enabled operators | Establish at launch | +25% vs. non-Guardian operators | Toast IQ engagement analytics |
| Toast IQ sessions initiated from Guardian alert (operator clicks alert → enters IQ chat) | N/A | ≥ 30% of alerts generate follow-on IQ session | In-app funnel analytics |

---

## Functional Requirements

### FR-01 — Prime Cost Calculation Engine (Critical)

The engine calculates prime cost on a continuous basis using data Toast
already owns: sales data from the POS, labor cost data from Toast Payroll
and Toast Labor Management, and food cost data from Toast Food Cost Management
(where enabled) or manual COGS inputs (where operators use a simplified
cost-of-goods approach).

**Prime cost formula:**

```
Prime Cost % = (Food Cost + Labor Cost) / Total Net Sales × 100

Food Cost = cost of ingredients used in the period
  (from inventory depletion if Toast Food Cost is enabled, OR
   from manual operator-entered COGS % per menu category if not)

Labor Cost = all wages, including hourly + overtime + tips paid out
  (from Toast Payroll or Toast Labor Management)

Total Net Sales = gross sales minus voids, comps, and refunds
  (from Toast POS transaction data)
```

**Three rolling windows calculated continuously:**
- **Daily:** Today's prime cost % (resets at each business day open)
- **7-day rolling:** Trailing 7-day prime cost % (smooths day-to-day variance)
- **Period-to-date:** Current accounting period (operator-configured: weekly,
  biweekly, or 4-week period)

**Benchmark comparison:**
Each rolling window is compared against:
1. The operator's configured target prime cost % (set during onboarding;
   default 65% if not configured)
2. The same period in the prior year (year-over-year comparison)
3. Operator's own 90-day rolling average (trend context)

**Data freshness:**
Daily and 7-day figures refresh every 4 hours during business hours (6 AM –
midnight local time), and nightly for the full day's calculation. Period-to-date
figures refresh daily at 6 AM. Labor cost data has a natural lag when payroll
has not been processed — the engine uses scheduled hours × wage rate as a
proxy for actual labor cost until payroll data confirms.

**Handling food cost data gaps:**
Toast Food Cost Management integration provides real-time COGS tracking linked
to menu item recipes and ingredient costs. For operators who do not use Toast
Food Cost Management, the Guardian uses operator-entered COGS % per menu
category (e.g., "all entrées: 32%") as a proxy. This approach produces a
directional rather than precise food cost figure and is labeled accordingly
in the UI. The onboarding flow prompts operators to either connect Food Cost
Management or configure COGS estimates, and explains the accuracy difference.

Acceptance criteria:
- Prime cost calculation runs continuously with ≤ 4-hour data lag during
  business hours
- Calculations clearly display the data inputs used (actual vs. estimated
  food cost, scheduled vs. confirmed labor)
- Year-over-year comparison clearly disabled and labeled for locations with
  < 12 months of Toast history
- Engine handles timezone correctly for operators in multiple time zones
- All calculations consistent with Toast's existing financial reporting
  — no discrepancy between Guardian figures and manual report pulls

---

### FR-02 — Alert Configuration and Delivery (Critical)

Operators configure their Guardian alert thresholds during onboarding. The
alerts are proactive push notifications — the operator does not need to open
any app or report to receive them.

**Configurable thresholds:**

| Setting | Default | Range | Description |
|---|---|---|---|
| Prime cost target % | 65% | 50–80% | The operator's own target for prime cost as a % of sales |
| Alert trigger — daily drift | +3 points above target | 1–10 points | How far above target to trigger a same-day alert |
| Alert trigger — 7-day drift | +2 points above target | 1–8 points | How far above 7-day rolling to trigger a weekly trend alert |
| Alert quiet hours | 2 AM – 6 AM | Configurable | Hours during which alerts are suppressed |
| Alert recipient(s) | Account owner | Any Toast user with manager access | Who receives the alert — owner, GM, chef, or any combination |

**Alert types:**

*Type 1 — Daily Spike Alert (same-day, urgent)*
Fires when today's prime cost % exceeds the operator's target by more than
the configured daily trigger threshold. Delivered as a push notification in
Toast Now mobile app and an in-platform banner in Toast web.

Example notification:
> 🔴 **Prime cost alert — Hardwood Cafe**
> Today's prime cost is 72.4% — 7.4 points above your 65% target.
> Labor is the driver. Tap to see what's happening.

*Type 2 — Trend Alert (weekly pattern)*
Fires when the 7-day rolling prime cost has been above target for 3+
consecutive days, indicating a pattern rather than a one-day anomaly.
Delivered as a Slack message (if Toast Slack integration is enabled) or
email, in addition to in-app notification.

Example notification:
> 🟡 **Prime cost trend alert — 3-day pattern**
> Your 7-day prime cost has been above target for 3 days.
> Food cost is 34.8% this week vs. your 30% target.
> This started Tuesday. Tap for details.

*Type 3 — Period Forecast Alert (forward-looking)*
Fires when the current period-to-date prime cost, extrapolated through
the end of the period at the current run rate, would end the period more
than 2 points above target. This is the only forward-looking alert — it gives
operators warning before the period closes, not after.

Example notification:
> 🟡 **Period forecast — on track to miss target**
> At your current run rate, this period will close at 67.8% prime cost
> — 2.8 points above your 65% target.
> 6 days remain in the period. Tap to see your options.

Acceptance criteria:
- Push notification delivered within 15 minutes of threshold breach
- All three alert types independently configurable — operators can enable
  or disable each type separately
- Multi-location operators receive location-specific alerts, not aggregate
  alerts — a drift at Location 2 does not appear as if it applies to Location 1
- Alerts suppressed for locations with < 30 days of Toast data (insufficient
  baseline for meaningful comparison)
- Alert fatigue design rule: a maximum of 2 alerts per location per calendar
  day across all alert types — the highest-priority active alert wins if
  multiple thresholds are breached simultaneously

---

### FR-03 — Drill-Down Explanation (Critical)

Every alert links to a structured explanation screen within Toast IQ that
answers: what is driving this drift, and how significant is each driver?

This is the most important design challenge in the product. The explanation
must be specific enough to be actionable and simple enough to be read in
60 seconds by an operator mid-service.

**Explanation screen structure:**

**Header:**
Prime cost summary for the relevant window (today / 7 days / period-to-date),
target, and variance — in large, legible numbers readable at a glance.

**Driver breakdown — two panels side by side:**

*Food cost panel:*
- Food cost % today vs. target vs. prior period
- Top 3 menu categories by COGS % (only shown if Toast Food Cost Management
  is enabled; greyed with "upgrade to see item detail" if not)
- If a category has jumped >2 points vs. last week, flagged with a brief
  plain-language note: *"Proteins are 38% this week vs. 31% last week —
  possible portioning issue or supplier price change"*

*Labor cost panel:*
- Labor cost % today vs. target vs. prior period
- Breakdown by daypart: morning, lunch, afternoon, dinner, late-night
- Specific shift comparison: "Dinner shift labor was 38% of dinner sales —
  your target is 30%. 4 hours of overtime were logged."
- If a specific role category is the driver: "Kitchen labor is running
  high — 8 hours above schedule for today"

**Day-of-week pattern note (when relevant):**
If the drift is concentrated on specific days (e.g., prime cost is on-target
Mon–Thu but 8 points above on weekends), a pattern note surfaces: *"Your
prime cost spikes on weekends — primarily labor-driven. This has been the
pattern for 6 of the last 8 weekends."*

**What happened vs. what's changing:**
The explanation distinguishes between two types of problems that require
different responses:
- A **structural drift** (the ratio has been gradually worsening over 3+ weeks)
  — indicates a systemic issue like a supplier price change or overstaffing
  relative to sales
- A **spike event** (the ratio was fine yesterday and bad today) — indicates
  a specific event like an unexpectedly slow shift, a large comp event, or
  a substitution of a higher-cost ingredient

This distinction is surfaced explicitly at the top of the explanation screen.

Acceptance criteria:
- Explanation screen loads within 2 seconds of tapping the alert
- Explanation uses plain language throughout — no accounting jargon, no
  acronyms without definitions, no terminology that assumes financial training
- All figures on the explanation screen are traceable to the underlying
  Toast data — operators can tap any figure to see the raw source data
- Explanation is generated by an AI language model using structured data
  inputs, not a static template — language adapts to the specific pattern
  of data for that operator's alert
- Explanation never attributes a cause it cannot substantiate from data
  — if the driver is ambiguous, the screen says so explicitly

---

### FR-04 — Recommended Actions (High)

At the bottom of every drill-down explanation, the Guardian surfaces 1–3
specific, actionable recommendations calibrated to the type and severity
of the drift — with one-tap implementation for recommendations that can be
executed directly in Toast.

**Recommendation examples by drift type:**

*Labor-driven daily spike — dinner shift overtime:*
> **For tonight:** Check if any open server positions can be cut at 8 PM if
> traffic holds at current pace. Your last 4 Fridays averaged table turns
> dropping 40% after 9 PM.
> [View tonight's schedule →]

*Food cost — protein category drift:*
> **This week:** Review your beef burger and chicken entrée portion sizes —
> both are in your top 5 sellers and protein costs are 7 points above target.
> A 0.5 oz portion adjustment on each would recover approximately 1.2 points
> of food cost.
> [View menu item costs →]

*Period forecast — on track to miss target:*
> **Before the period closes:** You have 6 days to recover 2.8 points.
> Your most effective lever is Thursday–Saturday labor — those 3 evenings
> drive 58% of your weekly prime cost. If you reduce scheduled labor by
> 2 hours on each of those evenings and hit your sales projections, you
> recover 1.9 points.
> [View schedule for Thursday →]

*Multi-location — one location dragging portfolio:*
> **Location 3 (Oak St) is driving your portfolio miss.** Its prime cost
> is 71.2% vs. 63.4% average for your other locations this week. The gap
> is labor — Oak St is running 6 more labor hours per day than comparable
> volume days at your other locations.
> [View Oak St labor detail →]

**Design constraints for recommendations:**
- A maximum of 3 recommendations per alert — more creates decision paralysis
- Each recommendation must be specific to the operator's own data, not
  generic advice — "reduce portion sizes" is not acceptable; "your beef burger
  portion size has increased 0.4 oz vs. your standard recipe" is
- Recommendations that can be acted on within Toast (schedule changes,
  menu item review) include a direct navigation link
- Recommendations involving supplier negotiations or pricing decisions include
  a note that these require the operator's own judgment — Toast provides
  the data, the operator makes the call
- The word "should" never appears in recommendations — phrasing is always
  observational ("your Tuesday labor runs 4 points above target") or
  conditional ("if you reduce scheduled hours by 2 on Friday, you recover
  approximately X points"), never directive

Acceptance criteria:
- Recommendations generated by Claude using structured data inputs and
  a constraint-enforced prompt — tested against 50+ historical prime cost
  scenarios before launch
- Recommendations reviewed by a licensed restaurant operations consultant
  and a Toast customer success representative during QA — no recommendation
  ships that those reviewers would not personally give to a restaurant operator
- One-tap navigation links to Toast schedule, menu, or payroll work for
  100% of recommendations that reference those modules
- A "Not helpful" feedback button on every recommendation, with a required
  reason select (Wrong data / Not relevant to my situation / Too vague /
  Already tried this / Other) — feeds into recommendation quality improvement

---

### FR-05 — Multi-Location Portfolio View (Medium)

For operators managing 3 or more locations, the Guardian adds a portfolio
prime cost view accessible from the Toast Multi-Location Dashboard.

**Portfolio view components:**

**Location heat map:**
A simple table or card grid showing all locations ranked by current prime
cost % — green (at or below target), yellow (1–3 points above), red (3+
points above). Updates every 4 hours. Lets a VP of Operations scan the
portfolio in 10 seconds and identify which location needs attention.

**Outlier flag:**
Any location running prime cost more than 4 points above the portfolio
average is flagged as an outlier with a brief explanation: *"Oak St is 7.8
points above your portfolio average. The gap is labor-driven — 6 extra
labor hours/day vs. comparable locations."*

**Period trajectory comparison:**
A small sparkline chart for each location showing prime cost trend over the
current period — makes it immediately visible which locations are improving,
which are stable, and which are deteriorating.

Acceptance criteria:
- Portfolio view loads within 3 seconds for up to 50 locations
- Location cards link directly to that location's individual Guardian
  explanation screen — no separate navigation required
- Portfolio view is read-only for GMs — only operators and owner accounts
  can see cross-location prime cost data (prevents inappropriate cross-location
  visibility between separately managed locations)

---

### FR-06 — Toast IQ Natural Language Integration (High)

Guardian alerts are the proactive surface. Toast IQ is the conversational
surface. When an operator receives a Guardian alert and wants to go deeper,
they should be able to continue the conversation in Toast IQ using natural
language — and Toast IQ should have full context of the active alert.

**Example conversation flow:**

*Alert received:*
> 🔴 Today's prime cost is 72.4% — 7.4 points above your 65% target.
> Labor is the driver. Tap to see what's happening.

*Operator taps → reads explanation → opens Toast IQ:*

> **Operator:** Why is my labor cost so high today? Is this normal for a
> Tuesday?
>
> **Toast IQ:** Today's labor cost is 38.2% of sales, versus your Tuesday
> average of 31.4% over the last 8 weeks. Two things stand out. First,
> your lunch shift ran 45 minutes longer than scheduled — 3 servers stayed
> late. Second, sales from 2 PM–5 PM were 22% below your Tuesday average
> for the last month, which means the same labor hours produced less revenue
> than usual. The over-scheduling and the slow afternoon together account
> for most of today's labor gap. Do you want to look at what tonight's
> dinner staffing schedule looks like?

This is the core interaction design principle: the Guardian catches it and
delivers the alert; Toast IQ explains it conversationally and helps the
operator think through a response.

**Technical requirement:** When an operator opens Toast IQ within 4 hours of
receiving a Guardian alert, Toast IQ is pre-loaded with the alert context —
the specific prime cost data, the identified driver, and the recommended
actions from the alert. The operator does not need to re-explain what happened.
The conversation starts from shared awareness.

Acceptance criteria:
- Toast IQ context injection from Guardian alert confirmed working for 100%
  of alert-triggered IQ sessions in testing
- Context injection does not override the operator's ability to ask unrelated
  questions — it surfaces as a suggested starting point, not a forced path
- Toast IQ responses referencing Guardian data are traceable to the underlying
  calculations — operators can ask "how did you calculate that?" and get
  a clear answer

---

## Out of Scope — v1

- **Automated corrective actions.** The Guardian recommends actions; it never
  takes them automatically. Automated schedule cuts, menu price changes, or
  vendor order modifications are not in scope at any version — these decisions
  require operator judgment.
- **Ingredient-level cost tracking for non-Food-Cost-Management users.** Item-level
  COGS drill-down requires Toast Food Cost Management integration. Operators
  who use category-level COGS estimates get directional guidance only at the
  category level — not item-by-item.
- **Benchmarking against other restaurants.** Cross-operator anonymized benchmarks
  ("your prime cost vs. similar restaurants in your market") are a future
  consideration. Toast's data across 165,000+ restaurant locations could make
  this extremely powerful. However, it raises data sensitivity questions that
  require a separate product and privacy review. Out of scope for v1.
- **Catering and off-premise events.** Prime cost calculations for catering orders
  and large off-premise events have different cost structures than in-restaurant
  service. v1 focuses on in-restaurant operations. Catering cost tracking is
  a v2 extension.
- **Franchise-level rollup.** Franchise groups with 50+ locations have different
  analytics needs (franchisee-vs-franchisor benchmarking, royalty-adjusted
  margins). Franchise-specific prime cost views are a v2 feature built on the
  same engine.

---

## Technical Considerations

*Note: These are product-level constraints, not an engineering spec.*

**Data already owned by Toast:**
The Prime Cost Guardian requires no new data sources. Every input — POS
transaction data, payroll data, labor scheduling data, and food cost data
for Toast Food Cost Management users — is data Toast already holds. This is
a new analytical and alerting layer on existing infrastructure, not a new
data acquisition challenge.

**Labor cost proxy accuracy:**
The most important data quality issue in the system is labor cost lag. Payroll
data becomes final only after a payroll run. Until then, the Guardian uses
scheduled hours × configured wage rates as a proxy. For operators with
irregular wage rates (variable tip-out, multiple pay tiers, overtime) this
proxy may diverge from actual labor cost. The UI should communicate clearly
when it is showing a proxy vs. confirmed payroll figure, and the alert thresholds
should account for proxy inaccuracy with a small confidence buffer.

**AI generation for explanations and recommendations:**
The drill-down explanation text and recommendations are generated by Claude
using structured data inputs. The prompt is strictly constrained:
- Input: structured JSON of prime cost figures, drivers, day-of-week patterns,
  location comparisons, and historical context
- Output: plain-language explanation and 1–3 specific recommendations
- Constraint: outputs must be grounded in the input data — no general restaurant
  advice, no claims that cannot be sourced from the input
- Format: maximum 150 words for the explanation, maximum 50 words per recommendation

All generated text is archived on the alert record for the life of the
account — both for operator reference and for ongoing model quality review.

**Alert delivery infrastructure:**
Push notifications to Toast Now mobile app use Toast's existing notification
infrastructure. No new notification channel needs to be built. Slack integration
for trend alerts uses Toast's existing Slack connector (already available for
other Toast alerts).

---

## Edge Cases

| Scenario | Handling |
|---|---|
| Restaurant has an unusually slow day (weather, local event) that spikes prime cost — not an operational problem | Explanation screen includes a note when today's sales are >25% below the prior 4-week same-day average: "Sales are significantly below your typical [Tuesday]. This may be inflating your prime cost % without reflecting a true cost problem." Operator can acknowledge and dismiss the alert. |
| Food cost jumps because of a one-time supplier price change, not an operational issue | Explanation flags the category-level COGS jump and notes "This pattern started [date] — check if a recent supplier invoice reflects a price change." Provides a path to update base COGS % in settings. |
| Alert fires during a closed period (restaurant is between shifts) | Alert delivered normally — operators review on their own schedule. No action required during a closed period. Alert does not auto-resolve. |
| Multi-location operator has locations in very different market contexts (e.g., downtown vs. suburban) | Portfolio view shows each location's prime cost vs. that location's own configured target — not vs. a single portfolio-wide target. Targets are configured per location at onboarding. |
| Operator acknowledges a Guardian alert and prime cost is still above target 48 hours later | A follow-up "unresolved" nudge is sent at the 48-hour mark: "Your prime cost alert from [date] is still active. Prime cost is now [X]% — [Y] points above target." Maximum one follow-up per alert. |
| New operator — no historical data for baseline | Guardian activates after 30 days of Toast data. During the first 30 days, a setup banner in Toast IQ invites the operator to configure their prime cost target and explains what Guardian will do when it activates. |

---

## Open Questions

- [ ] **Food cost proxy accuracy disclosure:** How prominently should the
  distinction between "actual food cost" and "estimated food cost (category
  %" be displayed? If an operator makes a business decision based on a Guardian
  alert that turns out to be driven by an inaccurate food cost proxy, there is
  a trust risk. Recommendation: display the data quality level (actual vs.
  estimated) on both the alert and the explanation screen, and suppress the
  food cost driver analysis for operators without Food Cost Management.
  The labor driver analysis remains fully accurate regardless.

- [ ] **Threshold defaults by restaurant segment:** The default 65% prime cost
  target may not be appropriate for all restaurant types. QSRs can profitably
  operate with lower prime cost targets; fine dining may have higher acceptable
  prime cost due to higher check averages. Should Toast pre-set different defaults
  by restaurant type (collected during the Toast onboarding flow)? Recommendation:
  yes — segment-specific defaults reduce onboarding friction and produce more
  relevant alerts from day one.

- [ ] **Recommendation liability:** Can Toast be held responsible if an operator
  follows a Guardian recommendation and it harms their business? Recommendations
  to reduce scheduled labor hours, for example, could — if applied incorrectly —
  lead to understaffing and service failures. Recommendation: all recommendations
  include a brief disclaimer that they are data-informed suggestions requiring
  operator judgment, and Toast's terms of service should explicitly address
  AI-generated operational recommendations. Legal review required before launch.

- [ ] **Competitor gap analysis:** Square for Restaurants, Aloha, and Lightspeed
  are all investing in analytics features. Does any competitor currently offer
  a proactive prime cost alert? Research needed before launch to ensure the PRD's
  competitive positioning is accurate. If a competitor has launched something
  similar in the period between this writing and launch, the differentiation
  angle shifts to Toast's cross-module data depth (payroll + POS + food cost
  in one system) rather than the novelty of the alert mechanism.

---

## Appendix A — Why This Matters Strategically for Toast

Toast processed $160+ billion in annualized GPV as of its most recent public
filings and serves restaurants across the full service model spectrum. Its
AI strategy, as reflected in Toast IQ, is oriented around the system-of-record
advantage: Toast is the only platform with simultaneous access to a restaurant's
POS transactions, payroll, labor scheduling, food costs, online ordering, and
loyalty data. No point solution or bolt-on analytics tool has that integration.

Prime Cost Guardian is the feature that converts that integration advantage
into a daily, tangible operator habit. An operator who receives a Guardian alert
that saves them 2 points of prime cost in a week they would have otherwise
missed — on a 4% margin business — has just experienced Toast IQ as the most
valuable tool in their restaurant. Not the most interesting tool. The most
valuable one.

The strategic argument for this feature is not AI capability. It is retention.
Operators who experience Toast as a system that watches their business and
catches problems they would have missed will not leave for a competitor whose
AI can only answer questions when asked. The stickiness of proactive intelligence
is categorically higher than reactive intelligence.

Toast IQ's expansion announcement framed the product as moving from "smart
features to smart AI assistant." Prime Cost Guardian is the logical next step:
from smart assistant to smart partner — one that notices when something important
is happening and tells you without being asked.

---

## Appendix B — Competitive Landscape

| Competitor | Prime Cost Feature | Gap vs. Guardian |
|---|---|---|
| Square for Restaurants | Reports section shows labor and COGS as % of sales; no proactive alert | Reactive only; operator must pull the report; no drill-down explanation or recommendations |
| Aloha (NCR Voyix) | Back-office labor cost reporting; no real-time alert | Legacy reporting architecture; not real-time; no AI explanation layer |
| Lightspeed Restaurant | Cost tracking with COGS entry; weekly summary reports | Weekly summary cadence; no proactive alert; no integrated labor data |
| Restaurant365 | Best-in-class prime cost tracking and reporting for multi-unit operators | Standalone software — not embedded in POS; requires separate subscription and data sync; no mobile push alert; powerful but complex for independent operators |
| Toast IQ (current) | Answers prime cost questions if asked; supports profitability analysis for new items | Reactive — requires operator to initiate; no proactive monitoring, no push alert, no period forecast |

The meaningful competitor is Restaurant365 for multi-unit operators, who
already use it and would find Guardian's integrated approach compelling if
it matches R365's analytical depth. For independent single-location operators —
Toast's largest customer segment by count — no current competitor offers
anything close to what Guardian proposes.

---

*This document was written as a portfolio case study. All Toast product
capabilities are based on publicly available information at pos.toasttab.com
and Toast's press releases as of March 2026. Restaurant industry economics
are sourced from the National Restaurant Association's 2025 Restaurant
Operations Data Abstract, NRA inflation research, and industry benchmark
sources as cited inline. This document does not constitute financial or
operational advice for restaurant operators.*
