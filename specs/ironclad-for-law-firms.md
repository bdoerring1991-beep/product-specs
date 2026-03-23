# PRD: Ironclad for Law Firms
## Multi-Client Contract Intelligence for Legal Practice

**Status:** Case Study — Portfolio  
**Author:** Blake Doerring
**Last Updated:** March 2026  
**Document Type:** Product Requirements Document  
**Intended Audience:** Hiring managers, product teams, legal tech professionals

---

> **Case study framing:** This PRD identifies and addresses a structural gap
> in Ironclad's current product and market positioning. All Ironclad product
> capabilities referenced are sourced from ironcladapp.com and public press
> releases current as of March 2026. Attorney workload and billing statistics
> are sourced from Bloomberg Law, the American Bar Association, and ALM/Law.com
> as cited inline. This document is a portfolio case study — not an internal
> Ironclad document.

---

## The Gap This PRD Addresses

Ironclad's buyer personas, as listed on ironcladapp.com, are: Legal Ops,
General Counsel, Procurement, and IT. Every feature in the platform — Jurist
AI assistant, Redlining Agent, Workflow Designer, Salesforce integration,
Renewal Agent — assumes the user is an in-house lawyer managing their own
company's contracts.

Law firms are structurally absent from Ironclad's product and positioning.
This is not an oversight — it is a deliberate market focus. But it creates
a specific, addressable gap at a moment when law firms are under significant
pressure to modernize and AI is reshaping the economics of legal work.

The core tension Ironclad has not yet resolved for law firms is this: Ironclad
makes contract work faster. In a billable hour model, faster means less revenue.
Until a product addresses this tension directly — by connecting AI-assisted
contract efficiency to billing value rather than billing time — law firms have
a rational economic reason not to adopt.

This PRD proposes **Ironclad for Law Firms**: a law firm–specific product tier
that adds multi-client context, matter-based organization, per-client AI
playbook management, and a billing bridge that makes AI efficiency an economic
argument for adoption rather than against it.

---

## Problem Statement

### Law firms are structurally different from in-house teams

Ironclad's current architecture assumes a single-organization context:
one standard playbook, one set of approved fallback positions, one approval
hierarchy, one Salesforce org to integrate with.

Law firm attorneys work in a fundamentally different structure:

**Multi-client context.** A corporate transactional associate at a mid-size
firm may manage contracts simultaneously for 12–20 active clients, each with
different risk tolerances, preferred terms, and negotiation history. Switching
from Client A's NDA review to Client B's SaaS agreement means switching not
just documents but entire playbooks, precedent libraries, and billing contexts.
Ironclad has no mechanism for this. Every workflow, playbook, and AI prompt in
Ironclad is scoped to one organization.

**Matter-based billing.** Law firms organize all work by "matter" — a specific
legal engagement for a specific client, with its own budget, billing code, and
time tracking record. Every email, every document review, every negotiation
session is logged against a matter number that flows into the firm's billing
system. Ironclad organizes contracts by workflow type and contract category.
These two organizational structures are incompatible without a deliberate
integration layer.

**The billable hour conflict.** Bloomberg Law's 2024 Attorney Workload and Hours
Survey found that attorneys work an average of 48 hours per week but bill only
36 of those hours — a 12-hour weekly gap attributable to non-billable
administrative work including time tracking, document management, and
communication logistics. Ironclad's AI features reduce the hours required for
contract work. In a billable hour model, that reduction directly reduces the
invoice. Partners at firms evaluating Ironclad face a rational economic objection:
"If Jurist cuts contract review from 4 hours to 1 hour, do we charge the client
for 4 hours of work we didn't do, or 1 hour of work that represents less
revenue?" Neither answer is satisfying without a rethinking of the billing model.

### The market opportunity is large and underserved

The United States has approximately 449,633 law firms as of 2025 (IBISWorld).
The segment most relevant to Ironclad's contract intelligence capabilities —
firms handling transactional work including M&A, commercial contracts, financing,
real estate, and employment agreements — numbers in the tens of thousands.

The Am Law 100 and Am Law 200 firms collectively employ over 100,000 attorneys.
Mid-size firms (50–250 attorneys) number approximately 2,000–3,000 nationally.
Boutique transactional firms (10–50 attorneys) number in the tens of thousands.

These firms collectively handle millions of contracts annually — NDAs,
commercial agreements, licensing deals, employment contracts, vendor agreements —
that are structurally identical to the contracts Ironclad's platform already
handles for in-house teams. The work is the same. The organizational context
and economic model are different.

### The competitive window is real and time-limited

Law firms are actively evaluating AI contract tools in 2025–2026. Thompson
Reuters' 2024 Future of Professionals report found that 79% of law firm
professionals believe generative AI will have a high or transformational impact
on their work within the next five years. Harvey AI, Luminance, and Kira
Systems are all actively targeting law firm buyers with contract AI products.

Ironclad's competitive advantages — a billion contracts processed, Jurist's
purpose-built legal AI, the Redlining Agent, the Workflow Designer — are
directly applicable to law firm contract work. If Ironclad does not develop
a law firm–specific offering, a competitor will, and the opportunity to expand
from in-house to law firm use cases closes.

---

## Proposed Solution

**Ironclad for Law Firms** is a product tier built on Ironclad's existing
platform with five additions specific to the law firm context. It does not
replace or modify the existing in-house product. It is a parallel configuration
layer that law firms activate when they provision their Ironclad account.

**The five additions:**

1. **Multi-Client Workspace** — A client-switching layer that scopes all
   Ironclad features (playbooks, AI prompts, contract libraries, approval
   workflows) to the currently active client context, and enforces ethical
   walls between client matters.

2. **Matter-Based Contract Organization** — Contracts organized by client
   matter number, not just contract type, with matter metadata flowing to and
   from the firm's billing platform.

3. **Per-Client AI Playbook Management** — Each client gets their own AI
   playbook: preferred terms, fallback positions, risk tolerances, and
   negotiation history that Jurist and the Redlining Agent use as context
   when working on that client's contracts.

4. **Billing Bridge** — An AI feature that converts contract work performed
   in Ironclad into structured billing narratives — the detailed time entries
   attorneys need to justify their invoices — and routes them to the firm's
   time and billing system.

5. **Alternative Fee Arrangement (AFA) Pricing Intelligence** — A matter-level
   dashboard that shows how much time AI efficiency saved on a matter, enabling
   partners to make data-backed decisions about flat fee and capped fee pricing.

---

## Target Users

| Persona | Role | Primary Pain Point | What Ironclad for Law Firms Gives Them |
|---|---|---|---|
| Transactional Associate | Contract drafting and review | Switching between 15+ active client matters; no system that knows each client's preferences | Per-client playbooks that travel with the matter; Jurist trained on each client's history |
| Senior Associate / Counsel | NDA and commercial agreement negotiation | Rebuilding context every time a matter resumes after days away | Matter timeline showing every AI-assisted action, redline, and approval decision |
| Partner | Billing, pricing, and client relationship | Cannot justify flat fee pricing without knowing what a matter actually costs to deliver | AFA Pricing Intelligence dashboard showing true delivery cost per matter type |
| Legal Operations Director (BigLaw) | Firm-wide technology adoption | Cannot deploy Ironclad without per-client data separation and conflict check integration | Multi-Client Workspace with ethical wall enforcement and conflict flag triggers |
| Billing Coordinator | Time entry and invoice preparation | Attorneys submit vague or missing time entries; hours reconstructed at end of week | AI-generated billing narratives drafted at the moment of contract work |

---

## Goals & Success Metrics

### Primary Goal
Make Ironclad's AI contract capabilities economically rational for law firms
by resolving the billable hour conflict and adding the multi-client context
that in-house CLM architectures lack.

| Metric | Baseline | Target | Measurement |
|---|---|---|---|
| Law firm accounts as % of total Ironclad customer base | ~0% (product not positioned at law firms) | 10% of new logos within 18 months of launch | CRM account type tagging |
| Billing narrative adoption rate (% of contract actions with AI narrative generated) | N/A | ≥ 65% of contract actions in law firm accounts | In-product event tracking |
| Average time from contract action to time entry submission | Industry baseline: 2–3 days (attorneys reconstruct at week's end) | ≤ 4 hours | Billing Bridge timestamp vs. matter time entry timestamp |
| Associate time saved per NDA review cycle | Bloomberg Law baseline: significant non-billable overhead per matter | Measured at 6-month customer review | Customer success survey + matter analytics |
| Per-client playbook creation rate | N/A | ≥ 1 playbook per active client within 90 days of onboarding | Playbook records per account |

### Secondary Goal
Position Ironclad as the enabling technology for law firms transitioning
to alternative fee arrangements.

| Metric | Baseline | Target | Measurement |
|---|---|---|---|
| Matters with AFA pricing enabled | N/A | ≥ 20% of matters in active law firm accounts | Matter billing type field |
| Partner satisfaction with AFA pricing confidence | N/A — establish baseline at launch | ≥ 4.0/5.0 at 6-month survey | Quarterly NPS survey to partner users |

---

## Functional Requirements

### FR-01 — Multi-Client Workspace (Critical)

The Multi-Client Workspace is the foundational architectural addition that
makes all other law firm features possible. It is a client-switching layer
that sits above Ironclad's existing organization-level configuration.

**How it works:**

Every user in a law firm Ironclad account belongs to one or more client
workspaces. Each client workspace is a scoped environment with its own:
- Contract library (only contracts related to that client)
- AI playbook (Jurist and Redlining Agent behavior for that client's matters)
- Approval workflow (client-specific signing authority and escalation rules)
- Template library (NDAs, MSAs, SOWs, and other templates the firm has
  used or customized for this client)
- Activity history (all Jurist interactions, redlines, and negotiations
  for this client's contracts)

When an attorney opens Ironclad, they see a client switcher in the top
navigation bar. Selecting a client sets the active workspace context — all
AI features, contract views, and templates reflect that client's configuration
until the attorney switches.

**Ethical wall enforcement:**
This is a legal ethics requirement, not a UX preference. In law firms, an
attorney working on a matter for Client A must be prevented from accessing
Client B's contracts if a conflict exists between A and B. Ironclad's
Multi-Client Workspace enforces this through permission rules that block
cross-client access when a conflict flag is set by the firm's Legal
Operations Director or conflicts management system.

Conflict flags are set manually in v1. Integration with dedicated conflicts
management systems (Intapp Conflicts, IntelliCheck) is a v2 integration via
Ironclad's existing API layer.

**Workspace isolation rules:**
- AI chat history from Client A's workspace never bleeds into Client B's context
- Jurist's training on Client A's preferred terms does not inform redlines for
  Client B's contracts
- Search results in the contract repository are scoped to the active workspace
  — an attorney searching "limitation of liability" sees only results from
  the currently active client's contracts
- A workspace can be archived when a client relationship ends; archived
  workspaces are read-only but fully searchable for conflict checks

Acceptance criteria:
- Switching client workspaces takes ≤ 2 seconds with no page reload
- All AI context (Jurist history, playbook, redline preferences) resets
  cleanly on workspace switch
- Conflict-flagged workspaces are inaccessible to flagged attorneys —
  access attempt triggers an in-app alert and logs the attempt for the
  Legal Ops Director
- Workspace isolation is enforced at the data layer, not just the UI layer —
  cross-workspace data access is impossible regardless of UI state

---

### FR-02 — Matter-Based Contract Organization (Critical)

Law firms do not organize work by contract type. They organize by client
matter — a specific legal engagement with its own code, budget, and team.
All time, all documents, and all communications for a matter are tagged
to that matter number in the firm's billing system.

Ironclad for Law Firms adds matter-based organization as the primary
structural layer within each client workspace.

**Matter record in Ironclad:**

```
Matter
  matter_number         (Text — firm's internal matter code, e.g. "12345-001")
  matter_name           (Text — "ABC Corp / Widget Co Asset Purchase")
  client                (Lookup — to client workspace)
  lead_attorney         (Lookup — to Ironclad user)
  team_members          (Multi-select — Ironclad users with access)
  billing_rate_code     (Text — billable rate code for billing bridge)
  fee_arrangement       (Picklist — Hourly / Flat Fee / Capped Fee / Contingency)
  estimated_hours       (Number — partner's estimate at matter open)
  status                (Picklist — Active / On Hold / Closed)
  opened_date           (Date)
  closed_date           (Date)
  contracts             (Related list — all contracts linked to this matter)
  billing_entries       (Related list — all AI-generated billing narratives)
```

**What changes when a matter is the organizing unit:**
- Every contract created, imported, or reviewed in Ironclad is tagged to a
  matter at creation — not retroactively
- Jurist AI interactions are logged against the matter, making the matter's
  activity log a complete record of all AI-assisted work for billing and
  audit purposes
- Matter dashboards show: contracts in progress, contracts executed,
  total AI actions taken, time estimates from the Billing Bridge, and
  fee arrangement status

**Matter templates for common law firm transaction types:**
Ironclad for Law Firms ships with pre-configured matter templates for:
- Non-Disclosure Agreement matter (standard NDA review and negotiation)
- SaaS Vendor Agreement matter (inbound vendor contract review)
- Commercial Licensing matter (outbound or inbound IP licensing)
- M&A Transaction matter (NDA → LOI → Definitive Agreement sequence)
- Employment Agreement matter (offer letters, employment contracts, separation)

Each template pre-populates the matter structure, links the appropriate
standard templates, and sets the default AI playbook to a starting
configuration the firm can customize.

Acceptance criteria:
- Every contract in Ironclad for Law Firms must be associated with a matter
  before AI features (Jurist, Redlining Agent) are available — matter
  association is enforced, not optional
- Matter number field accepts any alphanumeric format to match the firm's
  existing billing system codes without reformatting
- Matter dashboard loads all associated contracts and billing entries within
  3 seconds for matters with up to 500 contracts
- Matter records are exportable to CSV for billing system reconciliation

---

### FR-03 — Per-Client AI Playbook Management (Critical)

Ironclad's existing playbook feature defines standard positions an
organization takes in contract negotiations — what terms they prefer,
what fallbacks are acceptable, what clauses are non-negotiable.

In the in-house context, one company has one playbook.

In the law firm context, each client has their own playbook — and the
firm may have negotiated on behalf of that client dozens of times,
building a body of knowledge about what that client has historically
accepted and rejected in negotiations.

**Per-client playbooks in Ironclad for Law Firms:**

Each client workspace has a dedicated playbook that governs how Jurist
and the Redlining Agent behave when working on that client's contracts.
The playbook has three sources of input:

**1. Firm-defined standard positions**
The firm's Legal Ops Director or designated attorney configures the baseline
positions for each clause type: preferred language, acceptable alternatives,
and non-negotiable positions. This mirrors Ironclad's existing playbook
configuration.

**2. Client-specific overrides**
Attorneys can override the firm's standard positions at the client level.
Client A may accept a 30-day cure period on breach; Client B has instructed
the firm never to accept more than 15 days. These client-specific overrides
are stored in the client workspace's playbook and inform all AI redlines
for that client's contracts.

**3. Negotiation history learning (v2)**
In v1, playbook entries are manually configured. In v2, Ironclad learns
from negotiation outcomes — when a redline the firm proposed was accepted
by a counterparty in a prior matter, that position is flagged as
"historically successful" in the playbook. When a position was consistently
rejected and the firm ultimately accepted a counterparty's language, that
outcome informs future redline recommendations. This requires explicit
attorney review before any historical outcome updates the playbook —
the AI recommends; the attorney approves.

**Playbook portability:**
When a new matter is opened for a client, the client's playbook is
automatically applied. Attorneys do not configure AI behavior per contract —
they configure it once per client and it applies consistently.

Acceptance criteria:
- Playbook changes at the client level are versioned — the history of what
  a playbook said on any given date is preserved and auditable
- Playbook entries display which attorney last updated each position and when
- Jurist and Redlining Agent display which playbook entry informed each
  redline suggestion — attorneys can see the reasoning, not just the output
- Firm-level standard positions and client-level overrides are visually
  distinguished in the playbook editor

---

### FR-04 — Billing Bridge (High)

This is the feature that makes Ironclad's AI efficiency an economic argument
for adoption rather than against it. It connects contract work performed
in Ironclad to the billing narrative entries that law firm billing systems require.

**The problem it solves:**
Bloomberg Law's 2024 survey found that attorneys work an average of 48 hours
per week but only bill 36 — a 12-hour gap. A significant portion of that
gap is attributable to the delay between doing work and recording it. Attorneys
review a contract at 2 PM, answer emails until 4 PM, take three calls, then
try to reconstruct their time entries at 7 PM. Details are lost. Entries are
vague. "Review and revise NDA" tells a billing coordinator nothing about
what was actually done or why it took the time it did.

**How the Billing Bridge works:**

Every action an attorney takes in Ironclad — opening a contract, running
a Jurist query, reviewing a redline, approving a clause, executing a
workflow step — is logged as a timestamped activity record on the associated
matter.

At the end of each activity session on a matter (defined as a period of
activity followed by 30+ minutes of inactivity), the Billing Bridge generates
a draft billing narrative using Claude:

*Example: Attorney reviewed and revised a Software License Agreement for
Client ABC. Used Jurist to analyze limitation of liability clause against
client playbook, proposed three redlines to indemnification section, reviewed
counterparty's markup, and approved execution-ready version. Total Ironclad
session: 47 minutes.*

The attorney reviews the draft narrative, edits it if needed, confirms the
time, and submits it to the matter's billing queue. From the billing queue,
entries are exported to the firm's billing platform (Clio, Aderant, Elite 3E,
or Tabs3) in a single batch at the end of the day.

**What the Billing Bridge captures:**
- Session duration (start and end timestamps of Ironclad activity)
- Contract name and type reviewed
- Jurist queries run and their purpose (extracted from the query text)
- Redlines proposed, accepted, and rejected
- Approvals and workflow steps completed
- Number of clauses reviewed and flagged
- Counterparty markups reviewed

**What it does not do:**
- Automatically submit time entries to the billing system without attorney review
- Generate time entries for work done outside Ironclad (phone calls, emails,
  court appearances)
- Recommend a number of hours to bill — the attorney always sets the billable
  time, the AI describes what happened

**The billing model bridge:**
Ironclad for Law Firms takes no position on whether firms should bill by
the hour or by the matter. The Billing Bridge works with both:
- For hourly matters: the attorney confirms the actual time spent and
  the AI narrative accompanies the time entry as a detailed description
- For flat fee and capped fee matters: the time entry is logged as
  non-billable but the activity record contributes to the AFA Pricing
  Intelligence dashboard (FR-05)

Acceptance criteria:
- Billing narrative draft generated within 60 seconds of session end
- Draft narrative is editable before submission — attorney has full control
- Narrative never includes dollar amounts, rates, or billing recommendations
- Export formats supported in v1: CSV (universal), Clio API integration,
  Aderant CSV import template
- Attorneys can configure session timeout threshold (default 30 minutes;
  configurable 15–120 minutes per user preference)
- All generated narratives archived on the matter record permanently —
  they are legal records under state bar ethics rules governing billing

---

### FR-05 — AFA Pricing Intelligence (Medium)

Alternative Fee Arrangements — flat fees, capped fees, blended rates,
subscription retainers — are growing in corporate legal work but adoption
remains limited because partners lack reliable data on what a matter
actually costs to deliver. Without that data, pricing a flat fee is
guesswork, and partners price conservatively to avoid losing money,
which means clients pay more than necessary or reject AFA offers entirely.

**The Billing Bridge (FR-04) generates the data that makes AFA pricing possible.**

AFA Pricing Intelligence is a matter-level analytics dashboard that shows:

**Matter Cost Breakdown:**
For every closed matter that used Ironclad, the dashboard shows:
- Total attorney hours logged in Ironclad (from Billing Bridge records)
- Hours by activity type (drafting vs. review vs. negotiation vs. approval)
- Hours by attorney seniority (partner time vs. associate time)
- AI acceleration factor: estimated time the matter would have taken
  without AI assistance, based on the same matter type in the same period
  prior to Ironclad adoption

**Matter Type Benchmarks:**
Across all closed matters of the same type (e.g., "SaaS vendor NDA"),
the dashboard shows:
- Average hours per matter type in the firm's own history
- Variance range (fastest and slowest matters of this type)
- Which clauses consistently consumed the most time
- Which clause types were most frequently escalated to senior review

**AFA Pricing Recommendation:**
For any new matter being opened, a partner can run a pricing estimate
based on the firm's historical data:
- "Based on your last 14 SaaS vendor NDA matters, the median delivery
  time was 3.2 hours. A flat fee of $X at your blended rate covers the
  median with a 20% buffer."

The recommendation is a starting point. The partner sets the price.
The AI provides the data.

Acceptance criteria:
- Dashboard requires minimum 5 closed matters of the same type before
  showing benchmarks — below this threshold, displays "Insufficient
  history for this matter type"
- All pricing estimates clearly labeled as data-informed estimates,
  not guarantees — partners are responsible for all pricing decisions
- Dashboard visible to partners and Legal Ops Directors only —
  associates do not see firm-wide billing data
- Matter type categorization is user-configurable — firms can define
  their own matter type taxonomy if the defaults don't match
- Data used in AFA recommendations is the firm's own historical data only —
  no cross-firm benchmarking in v1 (raises ethical and competitive sensitivity)

---

## Out of Scope — v1

- **Conflicts integration.** Conflict checks against third-party systems
  (Intapp Conflicts, IntelliCheck) are a v2 API integration. In v1, conflict
  flags are set manually by Legal Ops.
- **Full billing system integrations beyond Clio and Aderant CSV.** Elite 3E,
  Tabs3, and other billing platforms are v2. Manual CSV export covers all
  systems in v1.
- **Negotiation history learning.** The per-client playbook is manually
  configured in v1. AI learning from historical negotiation outcomes is v2
  and requires attorney review workflows before any learning is applied.
- **Court filing and litigation document management.** Ironclad for Law Firms
  is scoped to transactional contract work — NDAs, commercial agreements,
  vendor contracts, licensing. Litigation document management is a different
  product category entirely.
- **Client portal.** Enabling law firm clients to access their own contract
  repository through a self-service portal is a v2 consideration that requires
  security architecture review.
- **Cross-firm benchmarking for AFA pricing.** Aggregate, anonymized benchmarks
  across Ironclad's law firm customer base could eventually be valuable for
  AFA pricing — but raises competitive sensitivity and ethics questions.
  Deferred to a future pricing study.

---

## Technical Considerations

*Note: These are product-level constraints, not an engineering spec.*

**Architecture approach — configuration layer, not fork:**
Ironclad for Law Firms should be implemented as a configuration layer on
top of Ironclad's existing platform, not as a separate codebase. The Multi-Client
Workspace is a scoping mechanism over the existing organization model.
Matter records are a new object type within the existing data model.
The Billing Bridge is a new event processing pipeline feeding a new
record type. This approach allows law firm features to inherit all future
Ironclad platform improvements without a parallel maintenance burden.

**Ethical wall enforcement — data layer requirement:**
The ethical wall requirement for client workspace isolation must be enforced
at the database query layer, not just at the UI layer. An API call made by
a user with a conflict flag against Client B's workspace must return an
authorization error, not just a blank UI state. This is a security requirement,
not a UX preference. Under state bar ethics rules, inadvertent access to
a conflicted client's information is a reportable ethics violation.

**Billing Bridge — AI prompt design:**
The narrative generation for billing entries uses the Jurist AI infrastructure
already in Ironclad. The prompt must be carefully designed to:
- Generate specific, defensible billing descriptions (not vague summaries)
- Never suggest a number of hours or a dollar amount
- Use the legal billing vocabulary attorneys expect ("reviewed and revised,"
  "analyzed and commented on," "negotiated and finalized")
- Be configurable by the firm — some firms have specific billing narrative
  style guides their attorneys follow

**Data handling for law firm clients:**
Law firms have heightened data confidentiality obligations under attorney-client
privilege and state bar ethics rules. Ironclad's existing enterprise data
handling (SOC 2 Type II, data processing agreements, zero data retention
on AI model training) is the baseline. Law firm accounts should additionally
be offered:
- Data residency options (US-only data storage for domestic law firm clients)
- Explicit contractual prohibition on any AI training on law firm client data
- Annual compliance certification that Ironclad provides to support firm-level
  ethics compliance reviews

---

## Edge Cases

| Scenario | Handling |
|---|---|
| Attorney attempts to open a contract in a conflict-flagged workspace | Access blocked at data layer; in-app notification: "You have a flagged conflict on this client. Contact your Legal Ops Director." Action logged on matter record. |
| Two attorneys from the same firm work on the same matter simultaneously | Standard concurrent editing conflict resolution — same as Ironclad's existing co-editing behavior; last-save wins with version history |
| Client workspace contains 500+ active matters | Matter list paginates; search-first UI rather than browse — attorneys type matter number or client name to navigate rather than scrolling |
| Billing Bridge generates a narrative the attorney finds inaccurate | Attorney edits the draft narrative freely before submission; edited narratives are flagged as "attorney-modified" in the audit log for quality improvement |
| Firm switches from hourly to flat fee mid-matter | Fee arrangement field on the matter is editable; existing Billing Bridge entries for that matter are re-tagged retroactively as informational rather than billable |
| Attorney works on a contract entirely outside Ironclad (e.g., Word) then imports | Billing Bridge only captures activity within Ironclad; imported contracts receive no AI narrative. Attorney can manually add a time entry with a blank narrative. |

---

## Open Questions

- [ ] **Pricing model:** Should Ironclad for Law Firms be a separate SKU with
  law firm–specific pricing, or an add-on module to the standard Enterprise tier?
  Law firms have different procurement cycles (annual retainer vs. per-seat SaaS),
  and some may prefer matter-based pricing rather than per-seat. Recommendation:
  offer both per-seat and matter-volume pricing at launch to test market preference.

- [ ] **Sales motion:** Ironclad's current sales team is trained on selling to
  in-house legal and procurement buyers. Law firm buyers — partners, Legal Ops
  Directors, Chief Information Officers — have different procurement processes,
  different evaluation criteria, and different ROI frameworks. Does Ironclad
  build a dedicated law firm sales segment, partner with legal tech resellers,
  or pursue bar association and legal conference channels? This is a go-to-market
  question that should be answered before investing in full law firm product
  development.

- [ ] **Jurist training data for law firm context:** Ironclad has processed over
  one billion contracts and trained Jurist on that data. How much of that data
  comes from law firm contract work (which tends to involve more complex,
  heavily negotiated agreements) vs. in-house corporate contracts (which tend
  toward standard commercial templates)? If Jurist's training data skews
  corporate-standard, its redline quality on complex M&A agreements may
  underperform. This should be evaluated before marketing Jurist to BigLaw.

- [ ] **Bar ethics opinion requirement:** Some state bars require law firms to
  obtain an ethics opinion before using AI tools that have access to client
  confidential information. Ironclad should proactively prepare a law firm
  client FAQ and standard ethics opinion template addressing the question of
  whether using Ironclad with client confidential contracts is permissible
  under applicable rules of professional conduct.

- [ ] **Conflict integration priority:** Conflicts checking is marked out of
  scope for v1, but it may be a blocking requirement for BigLaw adoption.
  Large firms are unlikely to deploy a multi-client contract platform without
  automated conflict enforcement. A pilot with a mid-size firm (50–150 attorneys)
  where conflicts are simpler may be the right v1 customer segment.

---

## Appendix A — Competitive Landscape

| Competitor | Primary Positioning | Law Firm Specific? | Gap vs. Ironclad for Law Firms |
|---|---|---|---|
| Harvey AI | LLM for lawyers — drafting, research, contract review | Yes — built for law firms | No CLM infrastructure (workflow, repository, e-signature). Pure AI layer, not end-to-end. |
| Luminance | AI contract review and due diligence for law firms and legal teams | Yes — law firm focused | Strong document review but weaker on workflow automation and matter-based billing integration. |
| Kira Systems | Contract analysis and due diligence | Yes — legacy law firm tool | Primarily due diligence extraction; not a CLM. No drafting, workflow, or billing features. |
| ContractPodAi | Enterprise CLM with AI | No — in-house focus | Similar gap to Ironclad: in-house architecture, no multi-client workspace. |
| Clio (Manage) | Law practice management platform | Yes — built for law firms | Strong on billing and matter management; weak on AI contract intelligence, redlining, and playbooks. |

**The unique position Ironclad for Law Firms occupies:**
Harvey and Luminance are pure AI layers — they analyze and draft but don't
manage the full contract lifecycle. Clio manages the practice but doesn't
provide contract AI. Ironclad for Law Firms is the only option that combines
enterprise-grade CLM infrastructure (workflow, repository, e-signature, AI
redlining) with law firm–specific multi-client architecture and billing
integration. No current competitor occupies this space.

---

## Appendix B — Why This Is Strategically Important for Ironclad

Ironclad recently surpassed $200M in ARR and was named a Leader in the
2025 Gartner Magic Quadrant for Contract Lifecycle Management. The in-house
CLM market is maturing — the largest in-house buyers have already selected
platforms, and growth will increasingly require either moving up-market
(larger enterprise complexity) or expanding to adjacent buyer segments.

Law firms are the largest adjacent buyer segment with directly applicable
contract volume. Unlike moving into new verticals (healthcare compliance,
government procurement), the law firm expansion requires no new AI capability
— it requires new organizational architecture and one new integration category
(billing systems). The underlying contract intelligence is already built.

The Billing Bridge is specifically the feature that unlocks this market.
Without it, law firms face a rational economic objection to AI adoption:
efficiency reduces billable hours, billable hours are revenue. With it,
AI efficiency becomes a capability argument for winning and retaining
sophisticated clients who want faster turnaround, lower cost certainty,
and better quality — which is increasingly what corporate legal buyers demand
from their outside counsel.

---

*This document was written as a portfolio case study. All Ironclad product
capabilities are based on publicly available information at ironcladapp.com
and associated press releases as of March 2026. Attorney workload statistics
are sourced from Bloomberg Law's 2024 Attorney Workload and Hours Survey
and the American Bar Association. Market size data is sourced from IBISWorld.
This document does not constitute legal advice.*
