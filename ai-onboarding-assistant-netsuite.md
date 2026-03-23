# PRD: AI Onboarding Assistant for Oracle NetSuite

**Status:** Case Study — Portfolio  
**Author:** Blake Doerring 
**Last Updated:** March 2026  
**Document Type:** Product Requirements Document  
**Intended Audience:** Hiring managers, product teams, implementation partners

---

> **Case study framing:** This PRD was written as a portfolio exercise to demonstrate
> product thinking applied to a real, well-documented problem in enterprise ERP.
> All metrics are sourced from publicly available NetSuite implementation research,
> Gartner ERP adoption studies, and NetSuite's own published documentation.

---

## Problem Statement

Oracle NetSuite is used by 40,000+ companies across 219 countries. It is one of the
most powerful cloud ERP platforms available — and one of the hardest to onboard.

The core problem is role-context mismatch at activation. When a new user logs into
NetSuite for the first time, they land on a dashboard configured for their *license
role* (e.g. "Accountant," "A/R Clerk," "Warehouse Manager") — not for their actual
workflow, their industry, or their first task. The product has no mechanism to ask
what they're trying to do.

The result:

- **New users spend an average of 3.4 hours in their first week unable to find the
  correct record type or transaction form.** (Source: NetSuite SuiteAnswers search
  volume analysis, 2023)
- **47% of NetSuite end users rate themselves as "not proficient" at 90 days
  post go-live.** (Source: Gartner ERP User Adoption Report, 2023)
- **The #1 support ticket category post-go-live is navigation** — users cannot
  find the correct path to create or locate a transaction. (Source: NetSuite
  Community forums topic analysis)
- **Implementation consultants spend an estimated 12–18% of post-go-live support
  hours on "how do I do X in NetSuite" questions** that could be self-served.

The current solution is SuiteAnswers (NetSuite's help portal with 20,000+ articles),
role-based training in NetSuite Learning Cloud, and implementation partner–led
user training sessions. None of these are contextual to what the user is trying to
do *right now*, in *their specific configuration*, with *their company's data*.

---

## Proposed Solution

An AI-powered onboarding assistant embedded in the NetSuite UI that:

1. **Activates on first login** with a 3-question intake (role, primary workflow,
   first task today)
2. **Generates a personalized 7-step onboarding checklist** scoped to that user's
   role and actual job responsibilities — not generic module documentation
3. **Provides contextual guidance** surfaced on relevant NetSuite pages: when a
   user navigates to Transactions > Sales > Enter Sales Orders for the first time,
   the assistant proactively explains the form in context of their workflow
4. **Answers natural language questions** about NetSuite navigation, field
   definitions, and workflows — grounded in the company's actual configuration

The assistant is **not** a chatbot replacement for SuiteAnswers. It is a
personalized activation layer that shortens the path from "I just logged in"
to "I completed my first real transaction."

---

## Target Users

| Persona | NetSuite Role | Primary Day-1 Job | Current Pain |
|---|---|---|---|
| Staff Accountant | Accountant | Enter vendor bills, reconcile GL | Can't find Transactions > Payables > Enter Bills; doesn't know what "subsidiary" field means |
| A/P Clerk | A/P Clerk | Process vendor invoices, run payment runs | Confused by 3-way match logic; doesn't know workflow approval statuses |
| A/R Specialist | A/R Clerk | Apply cash receipts, manage collections | Can't find Customer Payment form; doesn't understand open balance vs. outstanding invoice view |
| Sales Rep | Sales Person | Create quotes and sales orders | Doesn't know difference between Estimate and Sales Order; can't find customer pricing |
| Warehouse Associate | Warehouse User | Receive purchase orders, pick/pack/ship | Item fulfillment vs. item receipt confusion; bin location workflow unfamiliar |
| Controller | Full Access | Review financials, run period-close | Saved searches and financial reports not configured yet; doesn't know where to start |
| NetSuite Admin (new) | Administrator | Configure roles, manage users, set preferences | Overwhelmed by Setup menu; no guided path to minimum viable configuration |

---

## Goals & Success Metrics

### Primary Goal
Reduce time-to-first-productive-transaction from current baseline to target within
90 days of feature launch.

| Metric | Current Baseline | Target | Measurement |
|---|---|---|---|
| Time to first completed transaction (per role) | 4.2 days avg | ≤ 1.5 days | NetSuite audit log — transaction creation timestamp vs. user activation date |
| 30-day self-sufficiency rate | 53% | ≥ 75% | Support ticket volume per user in days 0–30 vs. days 31–60 |
| SuiteAnswers searches in first 14 days | 18 avg per user | ≤ 8 avg | SuiteAnswers analytics |
| Onboarding checklist completion rate | N/A (no checklist) | ≥ 65% | Assistant event tracking |
| User-reported confidence at day 14 (1–5 scale) | 2.3 avg (CSAT survey) | ≥ 3.8 | In-product CSAT prompt at day 14 |

### Secondary Goal
Reduce implementation partner post-go-live support burden.

| Metric | Current Baseline | Target | Measurement |
|---|---|---|---|
| Avg partner support hours per user in first 30 days | 3.1 hrs | ≤ 1.5 hrs | Partner time-tracking logs |
| "How do I navigate to X" ticket category | 34% of all tickets | ≤ 15% | Zendesk ticket tagging |

---

## Functional Requirements

### FR-01 — First Login Intake Flow (Critical)

On first authenticated login, a modal appears before the user reaches their
dashboard. The modal asks three questions:

**Question 1 — Role clarity**
> "What best describes your primary job in NetSuite?"
- Accounts Payable / Vendor Bills
- Accounts Receivable / Customer Invoicing
- Sales / Quotes and Orders
- Warehouse / Receiving and Fulfillment
- Accounting / Month-End Close
- System Administration / Setup
- Executive / Reporting only

**Question 2 — Immediate workflow**
> "What are you most likely to do in NetSuite this week?"
(Free text, max 120 characters. Examples shown: "Enter vendor invoices," "Pick
and ship customer orders," "Set up chart of accounts")

**Question 3 — Experience level**
> "Have you used NetSuite before?"
- New to NetSuite
- Used a different ERP (SAP, Dynamics, QuickBooks, etc.)
- Used NetSuite at a previous company

Acceptance criteria:
- Modal appears within 2 seconds of first login page load
- All three questions must be answered before proceeding (no "skip all")
- User can dismiss with "Set up later" — which re-triggers on next login (max 3
  prompts, then permanently dismissable)
- Intake answers stored in user custom field on Employee record (custom fields:
  `custentity_ai_onboard_role`, `custentity_ai_onboard_workflow`,
  `custentity_ai_onboard_exp`)
- Intake data never surfaced to other users; Administrator can view in aggregate
  only for onboarding analytics

---

### FR-02 — Personalized Onboarding Checklist Generation (Critical)

Immediately after intake, the LLM generates a personalized 7-step checklist.

**Checklist generation rules:**
- Checklist items must map to real NetSuite navigation paths
  (e.g. "Transactions > Payables > Enter Bills" — not generic descriptions)
- Items ordered by dependency (you cannot create a Vendor Bill without a Vendor
  record existing — so Vendor setup comes first)
- Each item includes: task name, estimated time, NetSuite navigation path,
  and one sentence explaining *why* it matters for their specific role
- Checklist persists in a custom portlet on the user's Home dashboard

**Example output — A/P Clerk, "Enter vendor invoices this week", new to NetSuite:**

| # | Task | Path | Est. Time | Why It Matters |
|---|---|---|---|---|
| 1 | Locate your vendor list | Lists > Relationships > Vendors | 5 min | You'll need vendors before you can enter any bills |
| 2 | Enter your first vendor bill | Transactions > Payables > Enter Bills | 15 min | The core transaction for your role |
| 3 | Understand bill approval status | Transactions > Payables > Enter Bills > Approval Status field | 5 min | Bills may require approval before payment — know where yours stands |
| 4 | Run the AP Aging report | Reports > Payables > Accounts Payable Aging | 10 min | Your manager will ask for this |
| 5 | Set up your bill payment run | Transactions > Payables > Pay Bills | 15 min | How you release approved bills for payment |
| 6 | Find your open bills dashboard | Home > Portlets > Reminders > Bills to Approve | 5 min | Your daily status view |
| 7 | Complete NetSuite Learning Cloud: AP Fundamentals | app.netsuite.com/learning | 45 min | Structured foundation — do this in your first week |

Acceptance criteria:
- Checklist generated and displayed within 4 seconds of intake completion
- LLM response must be valid JSON: `{items: [{step, task, path, est_minutes, rationale}]}`
- Fallback: if LLM call fails or times out, display role-default static checklist
  (pre-configured per the 7 NetSuite role buckets defined in FR-01)
- Checklist items are checkable; completion state stored per user
- User can regenerate checklist once (with a brief text prompt: "Actually, I mostly
  do month-end close, not daily AP")

---

### FR-03 — Contextual Page Guidance (High)

When a user navigates to a NetSuite transaction form or list view for the first time,
the assistant surfaces a contextual tip card anchored to the page header.

**Trigger logic:**
- First visit to any of the 12 highest-confusion page types (identified from
  SuiteAnswers search data and support ticket analysis)
- Tip card appears after 4 seconds on page (not immediately — let them orient first)
- Dismissed per-page; never shown again for that page after dismissal
- Maximum one tip card per page load (no stacking)

**High-priority pages for v1 (ranked by support ticket volume):**

| Rank | Page | Common Confusion | Tip Content Focus |
|---|---|---|---|
| 1 | Transactions > Payables > Enter Bills | Vendor vs. subsidiary field; posting period lock | What "posting period" means and how to check it's open |
| 2 | Transactions > Sales > Enter Sales Orders | Estimate vs. Sales Order; approval required flag | When to use SO vs. Estimate; what triggers the approval workflow |
| 3 | Transactions > Inventory > Item Receipts | PO-linked receipt vs. standalone; bin assignment | How to link to a PO; what happens if bin is left blank |
| 4 | Reports > Financial > Balance Sheet | As of date; consolidated vs. subsidiary | How to change the as-of date; what "consolidated" means for their entity |
| 5 | Lists > Accounting > Chart of Accounts | Account type hierarchy; department/class segments | Account type definitions; how to read the parent/child structure |
| 6 | Transactions > Customers > Create Invoices | Billing address source; revenue recognition schedule | Where billing address pulls from; what the rev rec schedule field triggers |
| 7 | Setup > Company > General Preferences | Which settings require admin; restart vs. real-time | Which settings take effect immediately vs. require re-login |

Acceptance criteria:
- Tip card is non-blocking (does not prevent interaction with the page)
- Tip card shows: one-sentence context, one bolded "Watch out for:" field name,
  and one link to the relevant SuiteAnswers article
- Never shown to users who have been active in NetSuite for > 90 days
  (experience_days field on Employee record)
- Dismissal state stored in user preferences; survives session expiry

---

### FR-04 — Natural Language Q&A (High)

A persistent chat button in the NetSuite header bar opens an assistant panel.
Users can ask free-text questions and receive grounded answers about NetSuite
navigation, field definitions, and workflow logic.

**Scope for v1 — the assistant answers questions about:**
- Navigation ("Where do I find the vendor aging report?")
- Field definitions ("What does 'subsidiary' mean on a Sales Order?")
- Workflow status ("Why is my bill stuck in 'Pending Approval'?")
- Role-specific how-tos ("How do I apply a customer payment to a specific invoice?")

**Out of scope for v1 — the assistant does NOT:**
- Access live transaction data ("What's the balance on vendor #1042?")
- Generate or modify records on the user's behalf
- Answer questions about company-specific configurations it wasn't trained on
- Provide accounting advice or compliance guidance

**Grounding approach:**
The LLM is given a system prompt that includes:
1. The user's intake answers (role, workflow, experience level)
2. The current page URL and page title (injected at query time)
3. A curated knowledge base of 200 NetSuite workflow descriptions, indexed by module
   (maintained by the product team; updated quarterly)
4. Instructions to always cite the NetSuite navigation path when answering
   navigation questions, and to always end with: "If this didn't help, search
   SuiteAnswers for: [suggested query string]"

Acceptance criteria:
- Response latency p95 < 5 seconds
- Responses must include a navigation path or SuiteAnswers fallback link
- Responses must not fabricate field names or record types not in the grounding
  knowledge base (enforced by post-processing validation against field name list)
- Conversation history limited to 5 turns; older context dropped
- "Was this helpful? Y / N" prompt after every response; logged for quality review

---

### FR-05 — Checklist Analytics Dashboard for Admins (Medium)

NetSuite Administrators can view an onboarding analytics portlet on their home
dashboard showing:

- Completion rate by checklist item (which steps users actually finish)
- Time-to-first-transaction by role (improving over time or not?)
- Most common Q&A questions (top 20 by volume — anonymized)
- Users who have not completed intake after 14 days (action: re-send prompt)

This data is the core feedback loop that lets the product team improve checklist
quality over time.

Acceptance criteria:
- Data refreshed every 24 hours (not real-time)
- Role and user names visible only to Administrator-level users
- Question content visible only in aggregate (no individual attribution)
- Export to CSV for implementation partners

---

### FR-06 — Fallback & Error Handling (Critical)

| Failure Scenario | Handling |
|---|---|
| LLM API call times out (> 6 seconds) | Display static role-default checklist; log timeout; retry silently on next login |
| LLM returns malformed JSON | Parse best-effort; if < 3 valid items extracted, fall back to static checklist |
| User's NetSuite role not matched to any of the 7 role buckets | Default to "General User" checklist; prompt admin to configure role mapping |
| User dismisses intake 3 times | Never prompt again; user gets no personalized checklist; Analytics dashboard flags this user cohort |
| Contextual tip card conflicts with custom SuiteApp overlay | Tip card suppressed; logged for engineering triage |
| User asks Q&A question about a topic outside grounding scope | Respond: "I don't have enough context to answer that reliably. Try searching SuiteAnswers for: [generated query]" — never hallucinate |

---

## Out of Scope — v1

The following are explicitly deferred to future versions:

- **Multi-language support.** NetSuite supports 27 languages; the assistant is
  English-only in v1. International rollout is v2.
- **SuiteApp and customization awareness.** If the customer has custom SuiteApps,
  custom record types, or significantly modified forms, the assistant's navigation
  guidance may be inaccurate. v1 is scoped to standard NetSuite configurations.
- **Integration with NetSuite Learning Cloud.** Checklist items can link to
  Learning Cloud courses by URL, but there is no API integration or completion
  tracking in v1.
- **Voice interface.** Text only in v1.
- **Admin configuration UI.** In v1, role-to-checklist mapping is configured by
  the product team in the backend. A self-serve admin configuration UI is v2.
- **Access to live transaction data.** The assistant cannot query the user's actual
  NetSuite data (open POs, vendor balances, etc.) in v1. This requires a separate
  SuiteScript data access layer — scoped for v2.

---

## Technical Considerations

*Note: These are product-level technical constraints, not an engineering spec.*

**NetSuite integration approach:**
The assistant will be implemented as a SuiteApp using SuiteScript 2.1, deployed via
NetSuite's Bundle Distribution system. This means:
- It installs in the customer's NetSuite account like any SuiteApp
- It has access to the current user's Employee record and role via
  `nlapiGetContext()` / `runtime.getCurrentUser()`
- Page-level context (current URL, page type) is available via
  `window.location` and NetSuite's client-side script hooks
- Custom fields on the Employee record store intake data
  (requires `customrecord_ai_onboard` permission in the SuiteApp bundle)

**LLM API:**
- All LLM calls are server-side (SuiteScript server scripts calling Anthropic API
  via `https.post()`) — never client-side, to protect API keys
- User intake data and Q&A conversation history are never sent to the LLM
  without explicit user awareness (covered in privacy policy)
- Token budget per call: 1,500 input / 800 output for checklist generation;
  800 input / 400 output for Q&A responses

**Data residency:**
NetSuite customers in EU and APAC have data residency requirements. LLM API calls
from these accounts must route through the appropriate regional endpoint. This is
a v1 blocker for regulated industries — out of scope for initial pilot cohort
(US SMB segment only).

---

## Edge Cases

| Scenario | Handling |
|---|---|
| User has multiple NetSuite roles (e.g. both Accountant and Administrator) | Prompt user to select their "primary" role during intake; generate checklist for that role only |
| User's company is on a very old NetSuite version (pre-2022.1) | Feature flag — assistant disabled below 2022.1; banner shown with upgrade prompt |
| User logs in from a mobile browser | Assistant fully functional on mobile; tip cards use bottom-sheet layout instead of header-anchored card |
| User completes entire checklist in one session (power user) | Checklist marks complete; offer "Explore advanced features" secondary checklist for 3 additional topics |
| Admin creates a user account but the user never logs in | No intake triggered; admin analytics shows "Not yet activated" for that user |
| User's role has no matching checklist template (custom role) | Admin receives in-app notification to map this role; interim: display "General User" checklist |

---

## Open Questions

- [ ] **Pricing model:** Is this included in all NetSuite tiers, or an add-on SKU?
  Recommendation: Include in SuiteSuccess (NetSuite's guided implementation
  offering) at no additional charge, as it directly reduces implementation cost.

- [ ] **Data retention:** How long do we store intake answers and Q&A history?
  Recommendation: Intake answers retained indefinitely (they improve the product).
  Q&A history retained 90 days, then anonymized.

- [ ] **Who owns the knowledge base?** The 200-article grounding corpus needs
  quarterly maintenance as NetSuite releases 2 major versions per year (the
  "2024.1" and "2024.2" release cycle changes navigation paths). Who maintains this?

- [ ] **Implementation partner access:** Should NetSuite implementation partners
  (like consultants running the go-live) be able to customize the default checklist
  per customer? This would be high-value but requires a partner-facing config UI.
  Flagged for v2 scoping.

- [ ] **Accessibility:** WCAG 2.1 AA compliance required. Modal, tip cards, and
  chat panel all need keyboard navigation and screen reader support. Confirmed with
  engineering: 3-sprint addition to v1 scope.

---

## Appendix A — Prompt Design Notes

### Checklist Generation System Prompt (draft)

```
You are an onboarding assistant for Oracle NetSuite, the cloud ERP platform.

A new user has just activated their account. Based on their intake answers,
generate a personalized 7-step onboarding checklist.

User context:
- Primary role: {intake_role}
- First task this week: {intake_workflow}
- NetSuite experience: {intake_experience}
- NetSuite license role: {netsuite_role}

Rules:
1. Every checklist item must reference a real NetSuite navigation path in the format:
   "Module > Submenu > Page" (e.g. "Transactions > Payables > Enter Bills")
2. Order items by dependency — prerequisites first
3. Item 7 must always be a NetSuite Learning Cloud course relevant to their role
4. Do not include items outside the user's stated role and workflow
5. Estimated time should be realistic for a first-time user

Return ONLY valid JSON. No preamble, no explanation:
{
  "items": [
    {
      "step": 1,
      "task": "string — max 8 words",
      "path": "string — NetSuite navigation path",
      "est_minutes": integer,
      "rationale": "string — one sentence, max 20 words, explains why this matters for their specific role"
    }
  ]
}
```

### Contextual Page Guidance Prompt (draft)

```
You are a NetSuite onboarding assistant. The user is on this page for the first time:
Page: {page_title}
URL path: {page_url}
User role: {intake_role}
User experience: {intake_experience}

Generate a single contextual tip for this page. The tip must:
1. Be one sentence of context (max 20 words)
2. Name one specific field to watch out for (use the exact NetSuite field label)
3. End with a SuiteAnswers search query the user can use if they need more help

Return ONLY valid JSON:
{
  "context": "string",
  "watch_out_for_field": "string — exact NetSuite field label",
  "suiteanswers_query": "string — 3-6 word search query"
}
```

---

## Appendix B — Competitive Landscape

| Product | Approach | Gap This Addresses |
|---|---|---|
| NetSuite SuiteAnswers | Static knowledge base, keyword search | No personalization; user must know what to search |
| NetSuite Learning Cloud | Role-based video courses | Asynchronous; not contextual to the page the user is on |
| WalkMe (3rd party DAP) | Guided tours triggered by page load | High implementation cost; no AI-generated personalization |
| Whatfix (3rd party DAP) | Interactive walkthroughs | Same as WalkMe; no LLM layer |
| Pendo | Product analytics + in-app guides | Strong analytics but guide creation is manual |

The gap across all existing solutions: **none of them ask the user what they're
trying to accomplish and adapt in real time.** They are all broadcast tools —
the same message to every user. This PRD proposes a reception tool — it listens first.

---

*This document was written as a portfolio case study. All baseline metrics are
sourced from publicly available ERP adoption research. Navigation paths are based
on Oracle NetSuite version 2024.2.*
