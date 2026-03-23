# PRD: JustiFi Revenue Intelligence
## AI-Powered Merchant Product Fit Engine for Vertical SaaS Platforms

**Status:** Case Study — Portfolio  
**Author:** Blake Doerring
**Last Updated:** March 2026  
**Document Type:** Product Requirements Document  
**Intended Audience:** Hiring managers, product teams, embedded fintech professionals

---

> **Case study framing:** This PRD identifies and addresses a specific product gap
> in JustiFi's current platform based on publicly available information at
> justifi.ai. Market statistics are sourced from EY-Parthenon, Bain & Company,
> UBS, and Tidemark Capital as cited inline. This is a portfolio case study,
> not an internal JustiFi document.

---

## The Gap This PRD Addresses

JustiFi's value proposition rests on two promises: help platforms monetize through
embedded payments, then grow that monetization through beyond-payments products —
BNPL, embedded lending, embedded insurance, and instant payouts.

The platform integration story is strong. JustiFi's AI-assisted developer
experience generates production-ready code on demand and reduces the typical
integration timeline from weeks to days. Merchant onboarding is AI-assisted.
The Engage™ service provides a dedicated team of fintech experts to drive adoption.

The problem is what happens after a merchant activates.

JustiFi's current beyond-payments offering is a toggle model: platforms enable
BNPL, lending, or insurance features in their product, and merchants encounter
those features if and when they naturally navigate to them. JustiFi's own
website states explicitly: "If less than 100% of your customers utilize your
platform's fintech features, you're leaving money on the table."

EY-Parthenon and Finix's 2024 survey of platforms and merchants found that
among platforms that have embedded payments, only 58% achieve greater than
50% merchant utilization. That gap widens dramatically for beyond-payments
products, which require merchants to understand and trust a fintech product
they didn't come to the platform for in the first place.

The root cause is not product quality. It is timing and targeting. Platforms
broadcast beyond-payments features to their entire merchant base and wait to
see who converts. There is no mechanism that tells a platform: "These 47 specific
merchants in your portfolio are strong candidates for BNPL right now, based on
their transaction patterns — here's why, and here's the message that will land."

JustiFi already owns the data that makes this possible. Every transaction flowing
through JustiFi's infrastructure is a signal: transaction frequency, average order
value, chargeback rate, seasonal patterns, ACH vs. card mix, payout timing
preferences. No competitor has this data at the platform level. JustiFi does —
and currently it powers reporting dashboards. It does not yet power predictive
product-fit recommendations.

**JustiFi Revenue Intelligence** is the AI layer that converts JustiFi's
transaction data advantage into a predictive merchant activation engine —
moving platforms from broadcast fintech marketing to precision product placement.

---

## Company and Product Context

JustiFi is an embedded fintech platform built specifically for vertical SaaS
platforms and franchise networks. Founded by operators who built a multi-billion-dollar
SaaS platform themselves, JustiFi offers what their team describes as "what we
wish we had" — a full-stack white-label fintech infrastructure without PayFac
complexity.

JustiFi's current product suite includes:

**Core infrastructure:** Embedded Payments via PayFac Direct™ pricing (card, ACH,
in-person terminals, Apple/Google Pay), white-label merchant onboarding, and a
Unified Fintech Checkout™ (UFC) that enables multiple products to be toggled on
within a single checkout experience.

**Beyond-payments products:** Embedded BNPL, Embedded Lending, Embedded Insurance
(including season-interruption insurance for youth sports and similar vertical-specific
products), and Instant Payouts.

**Platform tools:** An omnichannel dashboard with unified payment reporting,
AI-assisted developer integration (code generation, real-time technical Q&A),
AI-powered merchant onboarding, and flexible revenue management with customizable
merchant pricing and fee splitting.

**Engage™:** A dedicated team of fintech strategists — described as the firm's
Chief Fintech Officer and specialists — who use AI-powered insights to optimize
merchant adoption, identify revenue opportunities, and provide go-to-market support.
The about page notes that AI-powered insights within Engage™ are "coming soon."

**What JustiFi does not yet have:** A scalable, in-platform AI feature that
continuously analyzes each platform's merchant portfolio and generates
prioritized, specific recommendations about which merchants are ready for which
beyond-payments products — and surfaces those recommendations to the platform
in actionable form rather than requiring Engage™ intervention.

---

## Problem Statement

### The adoption gap is the biggest risk to JustiFi's revenue model

JustiFi monetizes primarily through transaction revenue — a percentage of the
payments flowing through the platform. Beyond-payments products generate
additional revenue streams: BNPL interest or fees, lending origination revenue,
insurance premiums, and instant payout fees.

The unit economics of beyond-payments products only work at scale. A platform
with 500 merchants where 60 use BNPL generates modest incremental revenue.
The same platform where 300 merchants use BNPL generates transformative revenue
for both JustiFi and the platform itself. The difference between these two
outcomes is activation — and activation is currently left largely to chance.

Three structural forces drive the adoption gap:

**1. Generic outreach doesn't convert fintech products.** BNPL is not a product
every merchant needs at every moment. A merchant whose average transaction is
$45 has little use for BNPL. A merchant whose average transaction is $800 and
whose customers frequently abandon at checkout has an urgent use case for it.
Platforms that offer BNPL uniformly to all merchants see low adoption because
most of the outreach is irrelevant to most recipients. Relevant outreach requires
knowing which merchants fit which products — and that requires transaction data
analysis at a level platforms cannot do themselves.

**2. Timing is product-specific and not obvious.** Embedded lending is most
compelling to a merchant who has just had three months of revenue growth and is
asking themselves how to expand. Instant payouts are most compelling to a
merchant who just experienced a cash flow crunch after a slow week. Season-
interruption insurance is most compelling to a youth sports platform merchant
three weeks before registration opens for a new season. The right product at the
wrong time doesn't convert. Platforms have no visibility into these timing signals.

**3. Platform operators are not fintech salespeople.** A vertical SaaS platform
built for field service management has a product team focused on dispatch,
scheduling, and job management — not on identifying which of their 300 contractors
is a good candidate for a business line of credit. Asking platforms to manually
analyze their merchant portfolio for fintech fit is asking them to do work they
are not equipped for and did not hire for. JustiFi's Engage™ service addresses
this with human expertise, but it does not scale to a full portfolio analysis
for every platform, every month.

### The data advantage JustiFi is not yet fully exploiting

JustiFi processes every transaction for every merchant on every platform using
its infrastructure. This creates a proprietary dataset that, properly analyzed,
contains strong predictive signals for each beyond-payments product:

**BNPL signals:** High average order value (>$200), increasing cart abandonment
signals in platform data, merchant category consistent with considered purchases,
growth in new customer acquisition rate.

**Embedded lending signals:** Consistent revenue growth over 3+ months, high
payment processing volume relative to prior period, seasonal patterns suggesting
expansion periods, low chargeback rate (creditworthiness signal).

**Embedded insurance signals:** Vertical-specific triggers (youth sports platforms:
registration season approaching; event platforms: large upcoming events registered;
field service: weather-exposed work categories); new merchant activation timing.

**Instant payouts signals:** Irregular payout timing requests, ACH return history
suggesting cash flow sensitivity, rapid growth in transaction volume outpacing
payout schedule.

No competitor has this data for the same merchant at the same platform context.
Stripe Connect, Adyen Platforms, and Rainforest are all processing payments, but
none of them also own the platform relationship, the merchant onboarding context,
and the beyond-payments product suite in a single integrated stack. JustiFi does.

---

## Proposed Solution

**JustiFi Revenue Intelligence** is an AI feature embedded in JustiFi's existing
platform dashboard that continuously analyzes each platform's merchant portfolio
and generates a ranked, prioritized list of merchant-product activation
opportunities — with specific reasoning, recommended messaging, and expected
revenue impact per merchant — updated weekly.

It operates at two levels:

**Platform level:** The platform operator sees a Revenue Intelligence dashboard
showing: total untapped revenue opportunity in their merchant portfolio, ranked
list of activation opportunities by expected revenue impact, and product-level
conversion trend over time (are more merchants activating BNPL this quarter
than last?).

**Merchant level:** Each activation opportunity includes the specific merchant,
the product recommended, the signal that triggered the recommendation, a
plain-language explanation of why this merchant fits this product now, suggested
outreach messaging the platform can use (customizable, white-labeled), and
expected revenue contribution if activated.

The feature does not contact merchants directly. It equips the platform to do so.
JustiFi's white-label positioning means the merchant's relationship is with the
platform — Revenue Intelligence gives the platform the intelligence to act on
that relationship with precision.

---

## Target Users

| Persona | Role | Current Pain | What Revenue Intelligence Gives Them |
|---|---|---|---|
| Platform CEO / Founder | Sets fintech monetization strategy | Cannot quantify the gap between current fintech revenue and potential; doesn't know where to focus | Total portfolio revenue opportunity score; trend vs. last quarter; top 5 actions this week |
| Platform Head of Product | Decides which JustiFi features to enable and promote | Enables features broadly with no targeting; doesn't know why adoption is low | Merchant-level fit scores showing which product to prioritize for which segment |
| Platform Customer Success Manager | Manages merchant relationships | Contacts all merchants about all features; low response rates; no data to personalize outreach | Pre-written, data-backed outreach for specific merchants with specific reasoning |
| Platform Marketing Manager | Creates in-app and email campaigns for fintech features | Sends the same message to all merchants; no segmentation | Merchant segments pre-built by product fit score; campaign-ready messaging templates |
| JustiFi Engage™ Team | Provides fintech expertise to platforms | Works from experience and general patterns rather than real-time merchant data | Data-backed conversation with platforms — "your top 10 BNPL opportunities this month are X" |

---

## Goals & Success Metrics

### Primary Goal
Increase beyond-payments product adoption rate across JustiFi's platform portfolio
by converting the merchant-product fit analysis from a manual, episodic exercise
to an automated, continuous, data-driven process.

| Metric | Baseline | Target | Measurement |
|---|---|---|---|
| Average beyond-payments product adoption rate (% of merchants using ≥1 beyond-payments product) | Establish at launch across platform cohort | +20 percentage points within 12 months of feature adoption | JustiFi platform dashboard — product activation flags per merchant |
| Time from merchant eligibility signal to platform outreach | Currently undefined — ad hoc | ≤ 14 days from signal detection to outreach initiated | Signal detection timestamp vs. CRM outreach log |
| Revenue per merchant per month (beyond-payments contribution) | Establish baseline at launch | +35% within 12 months of feature adoption | JustiFi revenue reporting per merchant per product type |
| Platform NPS attributable to Revenue Intelligence | N/A | ≥ 50 from platforms actively using the feature | Quarterly platform NPS survey with feature attribution question |

### Secondary Goal
Make JustiFi's Engage™ service more effective and scalable by giving the team
real-time data to anchor platform conversations.

| Metric | Baseline | Target | Measurement |
|---|---|---|---|
| Engage™ conversation-to-activation conversion (% of Engage™ recommendations that lead to merchant activation within 30 days) | Establish at launch | ≥ 35% | Engage™ activity log vs. merchant product activation records |
| Platforms actively using Revenue Intelligence dashboard weekly | N/A (feature doesn't exist) | ≥ 70% of active platforms within 6 months of launch | Feature engagement analytics |

---

## Functional Requirements

### FR-01 — Merchant Fit Scoring Engine (Critical)

The scoring engine is the analytical core of Revenue Intelligence. It runs
nightly on each merchant in JustiFi's platform portfolio and produces a
product-fit score (0–100) for each of JustiFi's four beyond-payments products:
BNPL, Embedded Lending, Embedded Insurance, and Instant Payouts.

**BNPL Fit Score — inputs and logic:**

| Signal | Weight | Threshold for positive contribution |
|---|---|---|
| Average transaction value (30-day rolling) | 30% | > $150 |
| Transaction value growth (MoM) | 15% | > 0% (any growth) |
| New customer rate (if available from platform data) | 15% | > 20% of transactions from new payers |
| Merchant vertical BNPL fit | 20% | Verticals: home services, fitness, elective healthcare, events, professional services, youth activities |
| Current BNPL activation status | N/A | Merchant not already using BNPL (exclude activated merchants) |
| Chargeback rate (90-day) | 20% (negative) | < 1.5% — higher chargeback rates suppress score |

**Embedded Lending Fit Score — inputs and logic:**

| Signal | Weight | Threshold for positive contribution |
|---|---|---|
| Monthly processing volume growth (3-month trend) | 30% | > 10% MoM growth for 2+ consecutive months |
| Absolute processing volume | 20% | > $10,000/month (minimum lending underwriting threshold) |
| Payment consistency (missed/failed payments) | 20% (negative) | Zero ACH returns or failed payments in last 90 days |
| Account tenure on platform | 15% | > 6 months active processing |
| Seasonal expansion indicator | 15% | Transaction volume in current month > same month prior year by > 15% |

**Embedded Insurance Fit Score — inputs and logic:**
Insurance fit is primarily vertical and event-driven rather than transaction-signal
driven. The scoring engine applies vertical-specific rules:

- Youth sports platforms: score elevates for all merchants 45–60 days before
  typical registration season opens (derived from historical transaction pattern
  on platform — when does this merchant's volume typically spike?)
- Event management platforms: score elevates when a new large event is registered
  in the platform (if event data is accessible via platform API integration)
- Field services platforms: score elevates for merchants in weather-exposed
  categories (exterior work, landscaping, roofing) during spring/fall seasons
- All verticals: score elevates for merchants who processed a large transaction
  that was subsequently disputed or refunded — an insurance conversation is timely

**Instant Payouts Fit Score — inputs and logic:**

| Signal | Weight | Threshold for positive contribution |
|---|---|---|
| Payout timing requests (expedited payout requests in last 60 days) | 35% | ≥ 1 request |
| ACH return rate | 25% (positive) | > 0.5% ACH returns — suggests cash flow sensitivity |
| Transaction volume variability (week-to-week variance) | 20% | High variance (>30% weekly swing) suggests irregular cash flow |
| Current payout schedule | 20% | Standard T+2 or T+3 payout schedule (not already on same-day) |

**Score update frequency:** Nightly for all active merchants. Real-time trigger
updates for Urgent signals (large ACH return event, large disputed transaction)
that should not wait for the nightly batch.

**Score display:** Scores are not shown to platform users as raw numbers. They are
translated into three tiers: Ready (score ≥ 70), Warming (40–69), Not Yet (<40).
Raw scores are used internally and accessible via API for platforms that want to
build their own activation logic on top.

Acceptance criteria:
- Scores computed nightly for 100% of active merchants by 5:00 AM local time
- Real-time urgent triggers processed within 30 minutes of signal event
- Score explanation available for every merchant: which signals drove the score,
  which signals suppressed it, plain language for platform users
- Score history retained for 24 months for trend analysis and model improvement
- Platform can configure score threshold for "Ready" tier to match their
  risk appetite (default 70, configurable 60–85)

---

### FR-02 — Revenue Intelligence Dashboard (Critical)

The platform-facing dashboard that makes scoring actionable. Accessible from
JustiFi's existing omnichannel dashboard — not a separate product.

**Dashboard sections:**

**Portfolio Revenue Opportunity Summary (top of page)**
Four metric cards, one per beyond-payments product:
- Total merchants currently "Ready" for this product
- Estimated monthly revenue if all "Ready" merchants activate
  (calculated as: average revenue per activated merchant of this product type
  × count of Ready merchants)
- Change vs. last month (more or fewer Ready merchants this period)
- Quick action button: "View all Ready merchants for BNPL"

The estimated revenue figure is clearly labeled as a model estimate, not a
guarantee. Methodology is documented and accessible from an info tooltip.

**Top Activation Opportunities (main list)**
A ranked table of the top 25 individual merchant-product opportunities across
the platform's portfolio. Ranked by estimated monthly revenue impact if activated.

| Column | Description |
|---|---|
| Merchant name | Platform's sub-account display name |
| Recommended product | BNPL / Lending / Insurance / Instant Payouts |
| Fit tier | Ready / Warming |
| Primary signal | The single most influential signal driving the recommendation, in plain language |
| Est. monthly revenue | Revenue this merchant would generate if activated |
| Last contacted | Date platform last reached out about this product (from CRM log if connected) |
| Action | "View details" → opens the Merchant Activation Card (FR-03) |

**Product adoption trend (chart)**
Line chart showing activation rate per product over the last 12 months.
Answers the question: is the platform's beyond-payments adoption improving
over time, or is it flat? Filterable by merchant cohort (new merchants vs.
established merchants, by vertical, by processing volume tier).

**Segment explorer (filter panel)**
Platform can filter the opportunity list by: product type, fit tier, merchant
vertical, processing volume range, account tenure, geographic region (if available).
Enables targeted campaign building — "show me all Ready BNPL merchants in the
$200–$500 average transaction range."

Acceptance criteria:
- Dashboard loads full opportunity list within 3 seconds for portfolios up to
  5,000 active merchants
- Revenue estimates clearly labeled as model-derived estimates throughout
- "Last contacted" data only available if platform has connected a CRM integration
  (Salesforce, HubSpot) or manually logs contact activity in JustiFi
- All dashboard data exportable to CSV for platforms that manage outreach in
  external tools

---

### FR-03 — Merchant Activation Card (High)

A single-merchant detail view that gives a platform everything it needs to
make an informed, personalized activation conversation with that merchant.

**Activation card components:**

**Merchant summary strip**
Name, platform sub-account ID, vertical, tenure, processing volume (last 30/90/365
days), current active JustiFi products.

**Fit analysis**
For the recommended product, show:
- Fit tier (Ready / Warming) with score percentile context
  ("This merchant is in the top 15% of your portfolio for BNPL fit")
- Signal breakdown: the three most influential signals, each as a one-sentence
  plain-language explanation
  - *"Average transaction value of $340 is well above the BNPL sweet spot"*
  - *"Processing volume grew 24% last month — customers are spending more"*
  - *"Chargeback rate of 0.3% indicates healthy transaction quality"*
- One risk flag if applicable: the single most significant suppressing signal
  - *"Note: This merchant's customer base skews repeat buyers — BNPL may have
    lower incremental impact than for merchants with higher new-customer rates"*

**Recommended outreach message**
A platform-ready, editable message template for the recommended product,
pre-populated with the merchant's specific signals:

> *Subject: A new option for your [Merchant Name] customers*
>
> *Hi [Contact Name] — based on your recent growth on [Platform Name], we wanted
> to introduce a feature that could help your customers make larger purchases:
> Buy Now Pay Later.*
>
> *Your average transaction of $340 is an ideal fit for BNPL — customers can
> split purchases into installments, which often increases conversion on higher-
> value services. Merchants similar to yours typically see [X]% increase in
> average order value within 90 days of activation.*
>
> *[Platform Name]'s BNPL feature is already available in your account — here's
> how to turn it on in under 5 minutes: [link]*

The template is editable before sending. The platform sends it through their
own communication channels — JustiFi does not send on behalf of the platform.

**Historical context**
If the platform has previously reached out to this merchant about this product:
when, through which channel, and whether activation followed. Prevents repeated
outreach that degrades the merchant relationship.

Acceptance criteria:
- Activation card available for every merchant in the top opportunities list
- Message template editable in-line; character limit 1,000 words
- Template variables (merchant name, transaction value, product name) auto-
  populated from merchant data — no manual field filling required
- Template uses platform's configured product name (white-labeled — if the platform
  has branded BNPL as "FlexPay," the template says "FlexPay," not "BNPL")
- Outreach history section requires CRM integration or manual log entry;
  shows "No outreach recorded" without it rather than false empty state

---

### FR-04 — Revenue Intelligence API (High)

For platforms that want to build their own activation logic on top of JustiFi's
scoring data — triggering in-app notifications, custom email sequences, or
automated campaigns — a REST API exposing the scoring engine's output.

**Key endpoints:**

```
GET /v1/revenue-intelligence/merchants
  Returns: paginated list of all merchants with current fit scores per product
  Params: product_type, fit_tier, min_score, updated_since

GET /v1/revenue-intelligence/merchants/{sub_account_id}
  Returns: full fit score breakdown for one merchant across all products
  Includes: score, tier, signal details, score history (last 90 days)

GET /v1/revenue-intelligence/opportunities
  Returns: ranked opportunity list (same as dashboard, machine-readable)
  Params: product_type, limit, min_revenue_impact

POST /v1/revenue-intelligence/merchants/{sub_account_id}/outreach-logged
  Body: {product_type, channel, contacted_at, notes}
  Effect: logs outreach event, updates "last contacted" in dashboard
  Purpose: enables CRM-integrated platforms to push contact events back
```

**Webhook events:**
Platforms can subscribe to real-time webhook notifications for:
- `merchant.fit_tier_upgraded` — a merchant moves from Warming to Ready
- `merchant.urgent_signal` — a real-time trigger event (ACH return, large dispute)
- `merchant.activated` — a merchant activates a beyond-payments product
  (allows platforms to suppress further outreach automatically)

Acceptance criteria:
- API returns score data within 500ms p95
- Webhook delivery guaranteed with retry logic (3 retries over 24 hours)
- Rate limit: 1,000 requests/minute per platform API key
- Full API documentation in JustiFi's existing developer docs site
- Sandbox environment with synthetic merchant data for platform testing

---

### FR-05 — Engage™ Intelligence Briefing (Medium)

For JustiFi's Engage™ team, Revenue Intelligence generates a weekly briefing
per platform that anchors their strategic conversations in real-time data.

**Briefing contents (auto-generated, delivered to Engage™ team Monday 7 AM):**
- Portfolio health snapshot: activation rates by product vs. prior period
- Top 5 highest-opportunity merchants not yet contacted in the last 30 days
- Merchants who were contacted but did not activate (potential conversation
  starters about friction or objections)
- New merchants added to portfolio this week who already score Ready for a product
- One "quick win" — the single highest expected-revenue merchant activation
  the Engage™ team should prioritize in their platform call this week

**Format:** Slack message to the assigned Engage™ strategist for each platform.
Linked to full dashboard for deeper exploration.

Acceptance criteria:
- Briefing delivered to 100% of platforms with active Engage™ engagements
- Briefing links directly to the relevant dashboard view and merchant activation
  cards — no additional navigation required
- Engage™ team can mark a recommendation as "In conversation" or "Not applicable"
  to suppress it from future briefings without affecting the scoring model

---

## Out of Scope — v1

- **Direct merchant outreach by JustiFi.** Revenue Intelligence equips platforms
  to reach their merchants. JustiFi never contacts a platform's merchants directly —
  the white-label positioning is inviolable.
- **Cross-platform benchmarking in merchant-facing views.** Telling a merchant
  "you have similar BNPL adoption to other merchants your size" is a v2 feature
  that requires careful data handling and merchant consent design.
- **Automated campaign execution.** Revenue Intelligence surfaces opportunities
  and provides message templates. Sending those messages — through email,
  in-app notification, or SMS — is always a platform-initiated action in v1.
  A native campaign execution module (send this template to this segment) is a v2
  consideration once the intelligence layer proves its adoption value.
- **Real-time model retraining.** The scoring model is retrained quarterly
  using activation outcome data. Continuous online learning is a v2 investment
  requiring MLOps infrastructure beyond v1 scope.
- **Franchise-specific scoring.** JustiFi serves franchise networks in addition
  to vertical SaaS platforms. Franchise portfolios have different dynamics
  (franchisee variance from franchisor benchmarks, territory-based scoring).
  Franchise-specific scoring logic is a v2 extension of the same scoring engine.

---

## Technical Considerations

*Note: These are product-level constraints, not an engineering spec.*

**Data inputs — already owned by JustiFi:**
The scoring engine requires no new external data. Every input (transaction values,
volume trends, ACH return rates, chargeback rates, payout timing, merchant vertical,
account tenure) is data JustiFi already processes as part of its core payment
infrastructure. This is the key technical advantage: Revenue Intelligence is a
new analytical layer on existing data, not a new data acquisition requirement.

**Model architecture:**
v1 uses rule-based scoring with configurable weights — not a machine learning model.
This choice is deliberate:
- Rule-based models are explainable. Platform users and Engage™ team members
  can understand exactly why a merchant scored the way they did.
- Rule-based models can be adjusted quickly without retraining. If BNPL fit
  signals prove less predictive in a specific vertical, weights can be recalibrated
  in days, not months.
- Rule-based models are auditable. Regulatory scrutiny of AI in credit-adjacent
  products (lending recommendations) requires explainability that black-box ML
  models cannot provide.

A transition to ML-assisted scoring for the Embedded Lending product specifically
(where predictive accuracy has the highest revenue impact) is the v2 roadmap
item — after v1 accumulates sufficient labeled outcome data (merchant offered
product → activated or not) to train a supervised model.

**White-label integrity:**
All merchant-facing content in message templates, activation flows, and product
names uses the platform's configured branding. "JustiFi" never appears in any
merchant-visible output from Revenue Intelligence. This extends to the API —
product names returned in API responses are the platform's configured white-label
names, not JustiFi's product names.

**Lending recommendation compliance:**
The Embedded Lending fit score is explicitly not a credit decision and must not
be positioned as one. JustiFi's lending products involve a separate underwriting
process. The lending fit score indicates that a merchant appears to meet the
profile for a lending conversation — it does not pre-approve them for a specific
amount or terms. All UI language around lending recommendations must be reviewed
by JustiFi's legal team before launch to ensure compliance with applicable
lending marketing regulations.

---

## Edge Cases

| Scenario | Handling |
|---|---|
| A merchant is Ready for multiple products simultaneously | Show the highest expected-revenue product as primary recommendation; others listed in "Additional opportunities" section of the activation card |
| A merchant already uses all four beyond-payments products | Excluded from Revenue Intelligence opportunity list; appears in "Fully activated" segment for reference |
| A platform has fewer than 10 merchants | Revenue opportunity estimates suppressed (insufficient data for reliable estimate); scoring still runs and individual merchant cards are available |
| Merchant's transaction data shows a sudden anomalous spike (e.g., data entry error, one-time large transaction) | Scoring engine uses 30-day rolling averages rather than single-transaction values; anomalies are smoothed. Single-transaction outliers >3σ from 90-day average are excluded from trend calculations. |
| Platform disputes a specific score ("this merchant is not a BNPL fit") | Platform can suppress a merchant from Revenue Intelligence recommendations with a reason code; suppressed merchants are excluded from scoring for 90 days; suppression data feeds model quality review |
| Merchant activates a product without platform-initiated outreach | Flagged as organic activation in the reporting — separates revenue attributable to Revenue Intelligence from organic activation |

---

## Open Questions

- [ ] **Lending recommendation compliance review:** Before the Embedded Lending
  fit score feature is built, JustiFi's legal and compliance team must confirm
  that surfacing a "this merchant is a lending candidate" signal to platforms
  does not trigger lending marketing regulations (ECOA, fair lending, state-level
  equivalent). The signal is not a credit decision, but the positioning must be
  legally defensible. This is a pre-build requirement, not a post-build fix.

- [ ] **Revenue estimate methodology disclosure:** The estimated monthly revenue
  figures in the dashboard need a documented, disclosed methodology — what
  assumptions are made, what the confidence interval is, and how JustiFi will
  handle cases where estimates significantly miss actuals. This affects both
  legal accuracy and platform trust.

- [ ] **Scoring model performance accountability:** How will JustiFi communicate
  to platforms when the scoring model is performing well vs. poorly? A platform
  that acts on 10 Ready recommendations and sees 0 activations will lose trust
  in the feature. JustiFi needs a model performance scorecard visible to platforms —
  "merchants scored Ready for BNPL activated at X% rate over the last 90 days"
  — so platforms can calibrate how much weight to put on the scores.

- [ ] **Data sharing consent for cross-platform improvement:** If JustiFi wants
  to improve the scoring model using outcome data from all platforms (not just
  each platform's own data), a data sharing consent framework is needed. Platforms
  whose merchants activate at high rates have valuable labeled training data that
  benefits the whole network. How JustiFi structures this sharing — and what it
  offers in return — is a business model question that should be resolved before
  v2 ML model development.

---

## Appendix — Why This Is Strategically Important for JustiFi

JustiFi's competitive positioning against legacy processors rests on three claims:
better economics, more products, and data ownership. The third claim — data
ownership — is currently an infrastructure benefit (platforms own their data, no
vendor lock-in). Revenue Intelligence converts data ownership from a defensive
positioning argument into an active, compounding revenue driver.

The network effect logic: every merchant who activates a beyond-payments product
produces a labeled outcome (this type of merchant, with these signals, activated
this product). Aggregated across JustiFi's growing platform portfolio, those
outcomes train progressively better scoring models. Better models produce higher
activation rates. Higher activation rates generate more revenue for platforms.
More platform revenue increases JustiFi's per-platform economics and reduces
churn. This loop is unavailable to competitors who don't own the full stack.

JustiFi's about page notes that the Engage™ team's AI-powered insights capability
is "coming soon." Revenue Intelligence is the scalable, productized version of
what Engage™ currently does with human expertise. The strategic sequence — prove
it works with Engage™ human experts using early dashboard tooling, then productize
the pattern as a self-serve platform feature — is the right order of operations
for a company at JustiFi's stage.

EY-Parthenon's 2024 research found that 70% of non-financial platforms still view
payments as a utility rather than a growth driver. Revenue Intelligence is the
feature that converts that perception — not through marketing, but through showing
platform operators a dollar figure next to a merchant's name and saying "this
opportunity is waiting for a 5-minute conversation."

---

*This document was written as a portfolio case study. All JustiFi product
descriptions are based on publicly available information at justifi.ai as of
March 2026. Market statistics are sourced from EY-Parthenon/Finix (2024),
Bain & Company (2022), UBS (2023), and Tidemark Capital (2025) as cited inline.
This document does not constitute financial or regulatory advice.*
