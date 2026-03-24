# AI Strategy Assessment: Actus Nutrition

**Document Type:** Internal Strategic Assessment
**Prepared:** March 2026
**Author:** Blake Doerring
**Audience:** Executive Leadership, R&D, Commercial, Marketing, Operations

---

> **How to read this document:** This is not a proposal to automate what makes
> Actus competitive. Actus's edge is scientific depth, application expertise,
> and the ability to take ingredient concepts all the way to commercial production.
> No AI initiative in this document touches those functions — they propose to
> amplify them. The goal is more customer problems solved, more markets reached,
> more documentation produced, and more market intelligence captured — without
> adding headcount to do it.
>
> Each initiative is scoped for a mid-size B2B manufacturer. None requires a
> dedicated AI team. All are implementable via API-connected tooling or
> off-the-shelf platforms with appropriate data protection agreements.

---

## Company Context

Actus Nutrition (formerly Milk Specialties Global, rebranded October 2024) was
founded in 1945 as the Midwest Dried Milk Company — an early pioneer in upcycling
whey from a cheesemaking byproduct into a usable ingredient. Today Actus is the
largest whey protein processor in North America, operating 14 manufacturing
facilities across the United States, employing 1,400+ people, and exporting to
47+ countries.

The business operates across two primary segments:

**Human Nutrition** — Food ingredients including whey proteins (WPC, WPI,
hydrolysates), milk proteins (MPC, MPI), casein and caseinate, specialty and
custom ingredients (lactoferrin, alpha-lactalbumin, MFGM, probiotics, encapsulated
caffeine), and carbohydrates. Private label manufacturing through two production
capabilities: extrusion (healthy snacks and cereals at Clara City, MN) and
ready-to-mix (sports nutrition powders at Wautoma, WI).

**Animal Nutrition** — Livestock products including milk replacers, energy
ingredients, and nutritional supplements; companion animal nutrition including
private label and species-based formulations.

Notable competitive positions: the largest WPI plant in North America by volume
(Fond du Lac, WI); North America's only fresh curd casein and caseinate plant
(Jerome, ID, opened 2023); and a vertically integrated model that lets Actus
control quality, cost, and scheduling from raw ingredient intake through finished
private label product.

The innovation team covers custom ingredient development, unique formulations,
application testing, concept design, commercial trials, and flavor systems — the
full arc from concept to commercialized product. This technical depth is Actus's
primary competitive differentiator in a commodity-adjacent ingredient market.

---

## The AI Opportunity at Actus

Actus's business model creates five distinct AI leverage points:

**Scale without headcount.** The technical sales and R&D teams are finite. Every
inbound formulation question answered by AI is time returned to solving problems
that genuinely require a scientist or a seasoned account manager.

**Documentation volume.** Managing technical specifications, regulatory dossiers,
certificates, and export documentation across hundreds of SKUs and 47 export
markets is an enormous ongoing operational task. AI can dramatically compress
it without sacrificing accuracy.

**Market intelligence at speed.** The global functional food and sports nutrition
markets move fast. Knowing which CPG companies are launching high-protein bars
or clear protein beverages before they issue an RFQ — and being positioned as
the technical resource when they do — requires market monitoring at a scale
no commercial team can do manually.

**Content as demand generation.** Actus's scientific blog content is a genuine
B2B demand-generation asset. Accelerating the production of that content means
more touch points, more search visibility, and more inbound interest — without
adding marketing staff.

**Sustainability reporting as a commercial requirement.** Major CPG customers
increasingly require supply chain sustainability data as a condition of doing
business. Automating data collection and reporting protects existing customer
relationships and opens new ones in sustainability-conscious markets, particularly
in Europe.

---

## Data & IP Protection — Non-Negotiable Foundation

Before any initiative begins, this framing must be agreed upon by leadership.

**Actus's formulation knowledge is its crown jewel.** The database of how each
Actus ingredient behaves at different pH, temperature, processing conditions, and
application types — accumulated over 80 years of application testing — is
proprietary intellectual property that no competitor can replicate quickly. This
knowledge must never enter any third-party AI model's training data.

**Required for all initiatives:**
- All AI vendor agreements must explicitly prohibit using Actus data to train
  or improve external models (zero-data-retention clauses)
- Customer formulation project data, custom ingredient specifications, and
  R&D trial results are never used as AI inputs — these belong to customers
  and to Actus's proprietary knowledge base
- All AI outputs (generated documents, drafted content, formulation suggestions)
  are reviewed by qualified Actus personnel before being shared externally
- A data classification policy distinguishing public, internal, and confidential
  data categories must be established before Initiative 1 is deployed — this
  determines what can and cannot be used as AI context

---

## The Five Initiatives

The following five initiatives are ordered by time-to-value. They are not
mutually exclusive — all five can run simultaneously.

---

## Initiative 1 — AI Formulation Concierge

**Category:** R&D and commercial capacity
**Time to value:** 60–90 days
**Estimated weekly time saved:** 8–15 hours across technical sales and R&D
**Primary users:** Technical Sales/Account Managers, R&D Scientists, Private Label team

### The Problem

Actus's commercial model is built on technical partnership. When a sports
nutrition brand is developing a new clear whey protein beverage, or a cereal
manufacturer wants to boost protein content without affecting texture, they come
to Actus not just for ingredients but for answers. "Which grade of WPI performs
best at pH 3.5?" "What's the heat stability of your instantized WPC80 in a
UHT application?" "Can I hit a 30g protein target in a 355mL RTD while maintaining
flavor and clarity?"

These are not simple questions. They require knowledge of the functional
properties of specific Actus products in specific application conditions. Today,
they require a scientist or senior technical sales representative to answer —
often after some internal consultation. Response times vary. When an account
manager is on the road or a scientist is in application testing, a customer
waits.

The problem compounds across 47 export markets. International distributors and
customers often need application guidance in their own languages, across time
zones where Actus's US-based team is unavailable. Every hour of delay in
providing formulation guidance is an opportunity for a competitor to step in.

### The Solution

An internal AI assistant — the Formulation Concierge — trained on Actus's
publicly available product documentation, published application data, technical
specification sheets, and the ingredient knowledge base the team is willing
to codify. It is not trained on proprietary customer projects or confidential
R&D trial data.

**What it can answer:**

- Product selection: "Which Actus whey protein grade is most suitable for a
  clear acidic beverage at pH 3.0?"
- Functional properties: "What is the heat stability profile of instantized
  WPI in a retort application?"
- Certification routing: "Can I use your grass-fed WPC in a kosher-certified
  product?"
- Application guidance: "What incorporation rate do you recommend for
  alpha-lactalbumin in an infant formula context?"
- Comparative guidance: "What are the differences between WPC80, WPI, and
  milk protein concentrate for a high-protein bar application?"
- Troubleshooting: "My beverage using your MPI is showing gelation at 85°C
  — what would you suggest?"

**What it cannot answer (and explicitly defers to the team):**
- Custom formulation development requiring application testing
- Pricing and commercial terms
- Customer-specific project details
- Questions requiring lab validation of performance claims
- Regulatory opinions or claims approvals

**Deployment:**
The Concierge is deployed as an internal tool accessible to account managers
and the R&D team — not customer-facing in v1. Account managers use it during
calls to quickly retrieve technical guidance. The R&D team uses it to triage
incoming inquiries and determine which require deep scientific attention vs.
standard application support.

A customer-facing version (embedded on actus.com as a technical assistant)
is a v2 consideration pending a quality review of v1 accuracy over 90 days
of internal use.

### Implementation Approach

**Step 1 — Knowledge base assembly (Weeks 1–4):**
The R&D and technical marketing teams compile Actus's publicly available and
internally shareable technical knowledge:
- All published technical data sheets and spec sheets (already exist; need
  to be compiled in structured format)
- Published application guides and formulation suggestions from the website
  and trade materials
- Ingredient functional properties database (pH stability, heat stability,
  solubility profiles, emulsification behavior) — to be codified by R&D
- Certification compatibility matrix (which products carry which certifications)
- Published blog content from actus.com (already public, high technical value)

This knowledge base is the AI's grounding source. It is reviewed by the R&D
team before being indexed to confirm accuracy. No customer project data is
included.

**Step 2 — AI assistant build (Weeks 3–6):**
Build using the Anthropic API (Claude Sonnet model) with the compiled knowledge
base as a retrieval-augmented generation (RAG) source. The assistant is
configured with a system prompt establishing:
- Its role as a technical ingredient assistant for Actus Nutrition
- What categories of questions it can and cannot answer
- Instructions to always recommend engaging the R&D team for questions
  requiring application testing or custom development
- Instructions to cite the source document for any specific technical claim
- Zero-hallucination constraint: if the answer is not in the knowledge base,
  it says so explicitly rather than inferring

**Step 3 — Internal pilot (Weeks 6–10):**
Deploy to 3–4 account managers and 2 R&D scientists as internal users.
Collect feedback on accuracy, coverage gaps, and cases where the assistant
incorrectly answered or appropriately deferred. Use this to improve the
knowledge base before broader rollout.

**Tooling:** Anthropic API + a lightweight RAG framework (LlamaIndex or
LangChain) with Actus's knowledge base indexed in a vector database (Pinecone
or Weaviate). Hosted on AWS. All data stays within Actus's controlled environment —
no Actus data passes through Anthropic's training pipeline under the enterprise
zero-data-retention agreement.

**Estimated cost:** $800–$2,000/month (API costs + vector database hosting).
The primary investment is the 40–80 hours of R&D team time to compile and
review the knowledge base in Weeks 1–4.

### Testing Checklist

- [ ] Run 50 real formulation questions that account managers have received
  in the past 12 months through the Concierge — what percentage are answered
  accurately?
- [ ] Test all certification compatibility questions (halal + kosher + non-GMO)
  for accuracy
- [ ] Test that questions outside scope are correctly deferred ("I don't have
  enough information to answer that — please contact our technical team")
- [ ] Verify that no responses cite data not in the approved knowledge base
- [ ] Test multi-language queries (Spanish and German at minimum given export
  markets) — does it handle them appropriately?

### Go-Live Checklist

- [ ] R&D Director has reviewed and approved the knowledge base for accuracy
- [ ] Zero-data-retention agreement confirmed with Anthropic
- [ ] Data classification policy established — Concierge knowledge base reviewed
  against it
- [ ] Account manager onboarding: 1-hour session on what the tool does well,
  what it defers, and how to use it during customer calls
- [ ] Escalation path documented: when the Concierge defers, what is the SLA
  for an R&D team member to follow up?

### Ongoing Maintenance

| Task | Frequency | Owner |
|---|---|---|
| Add new product spec sheets to knowledge base | At product launch | Technical Marketing |
| Review accuracy flagged by users ("this answer was wrong") | Weekly | Designated R&D contact |
| Expand knowledge base with new application data | Quarterly | R&D team |
| Audit knowledge base for discontinued or reformulated products | Semi-annual | R&D + Technical Sales |

---

## Initiative 2 — Technical Documentation Intelligence

**Category:** Operational efficiency
**Time to value:** 45–75 days
**Estimated time saved:** 6–10 hours per new product or certification launch
**Primary users:** Regulatory/Quality team, Technical Marketing, R&D

### The Problem

Actus manages a large and growing portfolio of ingredients across multiple
production facilities, each with different certification profiles, under
specifications that must satisfy regulatory requirements in 47+ export markets.
The documentation burden is substantial:

- Technical data sheets for every SKU (hundreds of active products)
- Safety data sheets (SDS) maintained to GHS standards
- Certificates of Analysis templates per product and facility
- Regulatory dossiers for export markets — what is GRAS-approved in the US
  may require a Novel Food application in the EU or a separate registration
  in a Southeast Asian market
- Certification documentation packages (halal, kosher, grass-fed, non-GMO,
  organic, gluten-free) — each certification body has its own documentation
  requirements
- Customer-requested documentation (allergen statements, country-of-origin
  declarations, nutritional compositions)

Each new product launch, facility addition, or certification expansion triggers
a cascade of documentation work. The 2023 opening of North America's only
fresh curd casein plant in Jerome, ID required a complete suite of regulatory
and technical documentation for a novel product category in each of Actus's
export markets. The October 2024 rebrand from Milk Specialties Global to Actus
Nutrition required updating documentation across the entire portfolio.

This work is currently largely manual. It draws on the regulatory team's time
and, for technically complex documents, the R&D team's review. AI can compress
the time to produce first-draft documentation from hours to minutes, freeing
both teams for higher-judgment work.

### The Solution

An AI-assisted documentation system with two components:

**Component A — Document Generation Assistant**
For each document type (technical data sheet, SDS, allergen statement, nutritional
composition summary), a template-driven AI that:
- Takes structured product data as input (ingredient name, protein content,
  moisture, ash, fat, particle size, pH, solubility, microbiological specs,
  certifications held, facility of origin)
- Generates a formatted first-draft document in Actus's standard template
- Flags fields that require regulatory team review before finalization

The document is never final until reviewed and approved by a qualified Actus
team member. The AI produces the draft; the human confirms it.

**Component B — Regulatory Change Monitor**
An AI monitoring layer that watches regulatory publications from key export
markets (FDA, EFSA, Codex Alimentarius, key national food safety authorities)
and flags changes relevant to Actus's product portfolio:

- New labeling requirements for dairy proteins in target markets
- Changes to permitted protein fortification levels in infant nutrition
- Novel Food regulation updates affecting specialty ingredients
- Halal or Kosher standard revisions from certification bodies
- Tariff or customs documentation changes affecting export shipping

This monitoring is currently done on an ad-hoc basis — someone on the
regulatory team reads industry news. Systematizing it means Actus catches
regulatory changes that affect customer commitments before customers raise them.

### Implementation Approach

**Document Generation:** Airtable or similar structured database storing
product specifications → Claude API generating document drafts from template
prompts → human review step built into workflow before any document is finalized
or shared externally.

**Regulatory Monitor:** A scheduled Python script using the Anthropic API to
summarize new regulatory publications from monitored sources weekly, filtered
by relevance to Actus's product categories and export markets. Delivered as
a weekly digest to the regulatory team.

**Estimated cost:** $400–$900/month (API costs + Airtable subscription). The
primary one-time investment is structuring the product database and building
the document templates — estimated 60–80 hours of regulatory team time.

### Go-Live Checklist

- [ ] Legal review of any AI-generated regulatory document before it is used
  as the basis for a customer commitment or export filing
- [ ] Document templates reviewed and approved by Regulatory Director
- [ ] Clear notation on all AI-generated drafts that they require human review
  before distribution — this must be enforced technically, not just as policy
- [ ] Regulatory Monitor source list reviewed and approved — confirm which
  authorities and publications to monitor for each export region

---

## Initiative 3 — Market & Commercial Intelligence

**Category:** Commercial capacity
**Time to value:** 60–90 days
**Estimated time saved:** 3–6 hours per account manager per week
**Primary users:** Account Managers, Sales Leadership, Business Development

### The Problem

Actus sells into markets where timing is everything. A food manufacturer
developing a new high-protein cereal brand is at peak receptivity to Actus's
ingredients and private label capabilities during the concept and formulation
phase — not after they have already qualified a competing supplier. Getting
in front of the right buyer at the right stage of product development is the
core commercial challenge.

Today, commercial intelligence gathering at Actus is largely manual: trade
shows, industry contacts, press monitoring by individual account managers.
There is no systematic mechanism to track which CPG companies are actively
developing products in Actus's target application categories, which brands
are reformulating existing products (often triggered by clean-label or
protein-boost trends), or which distributors in international markets are
expanding their sports nutrition portfolios.

Additionally, account managers preparing for customer meetings currently
reconstruct company research manually — reviewing a customer's website, their
recent product launches, their public statements about nutrition strategy. This
takes 2–3 hours per meeting and is often done under time pressure.

### The Solution

A commercial intelligence system with three components, modeled on the
NCG Initiative approach to multiplying a small team's research capacity:

**Component A — New Product Launch Monitor**
A weekly digest for the commercial team tracking new product launches in
Actus's target application categories:
- High-protein bars and snacks
- Functional and protein beverages (RTD, powder)
- High-protein cereals and breakfast
- Infant and life-stage nutrition products
- Sports nutrition supplements
- Pet nutrition (for the animal nutrition commercial team)

For each relevant launch, the digest notes: brand, product, claimed protein
content and ingredient source, retailer, and market. This tells the commercial
team who is actively building in these categories — warm commercial targets.

**Component B — Account Preparation Brief**
Before any significant customer meeting, an account manager can generate an
AI-prepared brief covering:
- Company overview and recent product launches
- Public statements about nutrition strategy or ingredient sourcing priorities
- Recent relevant trade press coverage
- The specific Actus products and capabilities most likely to be relevant
  to that customer's active development areas
- Suggested conversation openers based on the customer's portfolio gaps

This brief is generated from public sources only — no Actus proprietary data
about that customer is used as input.

**Component C — Trend Signal Report**
Monthly one-page AI-generated summary of emerging ingredient trends in Actus's
target categories, synthesized from trade publications (Food Navigator, Nutrition
Business Journal, New Hope Network), consumer research summaries, and ingredient
industry news. Used by product marketing and the R&D team to anticipate where
demand is heading 12–18 months out.

### Implementation Approach

**New Product Launch Monitor:** Python script + LLM API monitoring food
industry news feeds and new product databases (Mintel GNPD, Spoonshot, or
scraped from trade publications) on a weekly schedule, generating structured
summaries filtered by Actus's target categories.

**Account Brief:** Claude API with a structured prompt taking a company name
and key known context as input, fetching recent web content, and generating
a 1-page brief. Estimated 10 minutes to generate what currently takes 2–3 hours.

**Trend Report:** Monthly LLM synthesis of curated trade publications, delivered
as a 1-page digest to the commercial team and R&D leadership.

**Estimated cost:** $600–$1,400/month including any trade publication API
access required. Primarily LLM API costs are low given report-frequency usage.

---

## Initiative 4 — Content & Scientific Thought Leadership Accelerator

**Category:** Marketing and demand generation
**Time to value:** 30–60 days (fastest to implement)
**Primary users:** Marketing team, R&D scientists (as reviewers)

### The Problem

Actus's Insights & Innovations blog is genuinely differentiated content in
the ingredient manufacturing space. Posts on alpha-lactalbumin's role in
infant formula, lactoferrin's immunological benefits, the science of MFGM,
encapsulated caffeine delivery systems — this is not marketing filler. It
is the kind of technical content that CPG formulators and brand scientists
read and that positions Actus as a knowledge partner rather than a commodity
supplier.

The constraint is production time. Writing a scientifically accurate,
well-sourced post on a topic like "the role of whey protein hydrolysates in
gut health" requires a scientist's knowledge and typically takes 6–12 hours
from research to polished draft. The marketing team cannot produce this content
at scale without drawing heavily on R&D time — time that competes with
application testing and customer projects.

The content backlog is visible: the blog has strong foundational coverage
but publishing frequency could be significantly higher given the depth of
Actus's ingredient portfolio and the volume of new research being published
in dairy nutrition science.

### The Solution

An AI-assisted content production workflow that accelerates the creation of
scientific blog posts, trade materials, and sell sheets — with R&D review as
a mandatory final step before anything is published.

**The workflow:**

**Step 1 — Research synthesis:**
The marketing team or a scientist identifies a topic (e.g., new research on
beta-lactoglobulin's role in gut barrier function). They provide the source
paper(s) or a brief description. The AI synthesizes the research into a
structured draft that:
- Summarizes the science accurately in accessible language for a B2B food
  industry audience
- Connects the research finding to relevant Actus products
- Suggests practical application implications for formulators

**Step 2 — Scientist review:**
An R&D team member reviews the draft for scientific accuracy — not writing
quality, which is the AI's job. They correct any misrepresentations, add
nuance the AI missed, and confirm that product-specific claims are accurate.
Target review time: 45 minutes, not 6 hours.

**Step 3 — Marketing polish:**
Marketing edits for tone, SEO, and brand alignment. Publishes.

**Additional applications of the same workflow:**
- Trade show one-pagers and sell sheets for new products or certifications
- Application guides for specific product categories ("Formulating with Actus
  WPI in Clear Beverages")
- Email newsletter content
- LinkedIn posts adapting blog content for shorter formats
- Export market-specific content translated and adapted by AI for key
  international markets

### Implementation Approach

A simple Claude API integration within the marketing team's existing workflow
tool (Notion, Google Docs, or similar). No new platform needed — this can
be a custom GPT-style assistant with a system prompt configuring Actus's
tone, audience, and content guidelines.

**Estimated cost:** $200–$500/month (minimal API usage given content frequency).
Primary investment is 1–2 days of setup time for marketing to configure
the content templates and tone guidelines.

### Go-Live Checklist

- [ ] R&D Director agrees on review process and expected turnaround time for
  scientific accuracy review (recommend: 2 business days maximum)
- [ ] Marketing establishes which content types require R&D review (scientific
  claims) vs. marketing-only review (general content, SEO posts)
- [ ] Tone and style guidelines document created and used to configure the
  AI assistant
- [ ] Legal confirms AI-generated content policy — no AI-generated content
  published without human review and attribution to Actus

---

## Initiative 5 — Sustainability Reporting Automation

**Category:** ESG and customer compliance
**Time to value:** 90–120 days
**Primary users:** Sustainability/CSR team, Finance, Operations
**Revenue relevance:** Direct — increasingly a prerequisite for European customer relationships

### The Problem

Actus is a genuine sustainability leader in its category — pioneer in whey
upcycling, water surplus operations, and one of few ingredient manufacturers
actively tracking scope 1, 2, and 3 emissions across its full supply chain.
The 2024 Social Responsibility Report reflects significant operational commitment.

The challenge is data collection and reporting scalability. Scope 1 and 2
emissions (direct facility emissions and purchased energy) are manageable across
14 facilities. Scope 3 emissions — the upstream and downstream impact of Actus's
supply chain — involve dozens of raw material suppliers, logistics partners,
and downstream customers. This data is currently collected manually, at
significant ongoing effort.

The commercial urgency is increasing. European food manufacturers operating
under the EU Corporate Sustainability Reporting Directive (CSRD) increasingly
require their ingredient suppliers to provide standardized emissions data.
Large US CPG companies with public net-zero commitments require the same from
their supply chains. An Actus that cannot produce structured scope 3 data
on request is at a disadvantage with sustainability-focused customers — a
growing segment of the most premium and fastest-growing CPG brands.

### The Solution

An AI-assisted sustainability data management system with three functions:

**Function A — Facility Data Consolidation**
Aggregate scope 1 (natural gas, diesel, refrigerants) and scope 2 (electricity)
data from all 14 facilities into a single structured database, with AI-assisted
anomaly detection (e.g., a facility reporting a 40% spike in natural gas usage
is flagged for review before it enters the annual report).

**Function B — Scope 3 Supplier Questionnaire & Analysis**
AI-generated supplier sustainability questionnaires sent on a defined cadence
to Actus's top raw material suppliers (dairy co-ops, packaging vendors,
logistics partners). AI analyzes responses and produces a standardized scope
3 emissions estimate per supplier category. Where suppliers cannot provide data,
AI uses industry-standard emission factors (EPA, IPCC databases) as a
documented proxy with appropriate uncertainty disclosure.

**Function C — Reporting Output Generation**
AI drafts the annual Social Responsibility Report content and customer-facing
sustainability data sheets using the consolidated data. The sustainability
team reviews and finalizes. This compresses reporting production time from
weeks to days.

**Regulatory monitoring add-on:**
The same regulatory monitoring infrastructure built in Initiative 2 can be
extended to track sustainability reporting requirements — CSRD developments,
SEC climate disclosure rules, California climate bills — relevant to Actus's
customer base and export markets.

### Implementation Approach

A structured database (Airtable, Notion, or a purpose-built ESG platform
like Watershed or Greenly for the data layer) with Claude API integration
for data analysis and report drafting. Scope 3 supplier questionnaires can
be automated via email API integration.

**Estimated cost:** $800–$2,000/month depending on whether a purpose-built
ESG data platform is used or a simpler custom-built solution. The purpose-built
platforms (Watershed, Greenly) offer significant time savings for scope 3
data management and are worth evaluating for a company of Actus's scale.

---

## Prioritization Matrix

| Initiative | Time to Value | Time Saved/Mo | Revenue Impact | Complexity | Priority |
|---|---|---|---|---|---|
| 1 — Formulation Concierge | 60–90 days | 30–60 hrs (R&D + sales) | Direct (faster deal cycles, more markets served) | Medium | **Start now** |
| 4 — Content Accelerator | 30–60 days | 20–40 hrs (marketing + R&D) | Indirect (demand generation, brand positioning) | Low | **Start now** |
| 2 — Documentation Intelligence | 45–75 days | 25–50 hrs (regulatory + R&D) | Indirect (operational efficiency, compliance risk reduction) | Medium | **Start now** |
| 3 — Commercial Intelligence | 60–90 days | 15–30 hrs (commercial team) | Direct (deal timing, prospect conversion) | Low-Medium | **Phase 2** |
| 5 — Sustainability Reporting | 90–120 days | 40–80 hrs/year (CSR + operations) | Direct in European markets (qualification prerequisite) | Medium-High | **Phase 2** |

---

## What AI Should Not Do at Actus

**AI should not develop formulations on behalf of customers.** Custom ingredient
development — designing a novel protein hydrolysate for a specific bioavailability
target, or engineering a protein blend that hits a specific amino acid profile —
requires scientific judgment, application testing, and regulatory expertise that
AI cannot replace. The Formulation Concierge handles standard application guidance.
New development belongs to the R&D team.

**AI should not make regulatory determinations.** Whether a specific Actus
ingredient meets a particular country's regulatory requirements for a specific
application is a legal and regulatory opinion that must come from a qualified
human. AI can surface relevant regulatory information; it cannot conclude that
a product is compliant.

**AI should not generate content that makes unsupported health claims.** FDA
regulations on structure/function claims and health claims for food ingredients
are strict and violation-prone. Any AI-generated content touching health benefits
must be reviewed against Actus's approved claims list and legal guidelines before
publication.

**AI should not handle customer negotiation or pricing.** Commercial relationship
management — pricing, contract terms, supplier agreements — requires human
judgment and accountability. AI can support the commercial team with intelligence
and preparation; it does not participate in negotiations.

**AI should not access or process proprietary customer formulation data.**
When a customer shares their formulation details with Actus as part of a
development project, that data is confidential and belongs to the customer.
It must never be used as AI input under any circumstances.

---

## Immediate Next Steps

The following actions can begin within 30 days at no capital commitment:

1. **Data classification policy:** The VP of R&D and General Counsel agree
   in writing on what categories of Actus data can and cannot be used as
   AI inputs. This is the prerequisite for all five initiatives and takes
   approximately one meeting to scope and two weeks to document.

2. **Vendor agreements:** Before any initiative goes live, confirm zero-data-
   retention agreements with Anthropic (API) and any other AI vendors engaged.
   This is a procurement step, not a technical one.

3. **Knowledge base inventory:** The R&D and Technical Marketing teams spend
   two days inventorying existing technical documentation — what spec sheets,
   application guides, and formulation guidelines already exist in structured
   form and can serve as the Concierge's knowledge base. This determines
   Initiative 1's build timeline.

4. **Content pilot:** Marketing selects one upcoming blog topic and runs it
   through a Claude API draft (Claude.ai Pro is sufficient for a free pilot)
   to assess output quality and the time required for R&D review. If the
   review takes under 1 hour and the draft is scientifically sound, Initiative 4
   is effectively validated before any build investment.

5. **Sustainability data audit:** The CSR team assesses which of the 14
   facilities currently provide digital data exports for energy and emissions
   vs. manual reporting. This scopes the complexity of Initiative 5 and
   determines whether a purpose-built ESG platform is necessary or whether
   a simpler custom solution suffices.

---

*This document was prepared as a strategic assessment based on publicly
available information at actus.com as of March 2026. All regulatory references
reflect publicly available guidance. This document does not constitute legal,
regulatory, or compliance advice — specific regulatory and legal questions
should be reviewed by qualified counsel.*
