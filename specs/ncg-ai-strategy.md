# AI Strategy Assessment: Next Century Growth Investors

**Document Type:** Internal Strategic Assessment  
**Prepared:** March 2026  
**Author:** Blake Doerring
**Audience:** NCG Portfolio Managers, COO, CCO

---

> **How to read this document:** This is not a proposal to change NCG's investment
> process. NCG's mosaic research approach, team debate culture, and sell discipline
> are the source of its 27-year track record. This document proposes AI applications
> that amplify what the team already does — covering more ground, surfacing signals
> earlier, and removing time spent on tasks that don't require experienced judgment.
>
> Each initiative below is scoped for a 10-person firm. No initiative requires a
> dedicated engineering team. All are implementable via API-connected tooling or
> off-the-shelf AI platforms with appropriate data handling agreements.

---

## Firm Context

NCG was founded in 1998 and is 80% owned by its active portfolio manager partners. The team has over 100 years of combined investment experience working together at NCG.

NCG screens the universe of publicly traded companies to find companies in each market cap segment that are growing revenue at high rates, builds a mosaic using information from company managements combined with analyst, trade, and industry contacts developed over 25 years, and spends over half its time continuing to research companies in the portfolio to make sure fundamentals remain strong.

The firm runs four separate account strategies (Micro Cap, Small Cap, SMID Cap, Large Cap), two mutual funds, and a UCITS fund for non-US investors — all managed by the same five portfolio managers. The firm has one institutional sales and marketing professional, John Solecki, who joined in 2022 with 26 years of prior industry experience.

This context shapes the AI opportunity set: the highest-leverage applications are those that multiply a small team's research capacity, surface signals earlier in portfolio monitoring, and give a single institutional sales professional the output and responsiveness of a larger team.

---

## Regulatory Framing

NCG is an SEC-registered investment adviser subject to the Investment Advisers Act of 1940. Before implementing any AI application touching investment research or client communications, the following applies:

**SEC guidance on AI in investment advice (2023–2025):**
The SEC has issued guidance and proposed rules indicating that AI tools used in investment decision support must not conflict with an adviser's fiduciary duty. Key requirements include: material disclosures to clients when AI plays a role in generating advice or communications, recordkeeping of AI-generated outputs used in the investment process, and human oversight of AI-generated recommendations before they are acted upon.

**Practical implications for NCG:**
- No AI initiative in this document proposes replacing portfolio manager judgment. All AI outputs are inputs to human review — not executable signals.
- Any AI-generated content in client-facing materials (commentary, letters, reports) must be reviewed and approved by a portfolio manager before distribution.
- AI vendors must agree to data processing agreements that prohibit training on NCG's proprietary research, client data, or trading activity.
- The CCO (Kelly McNulty) should review vendor agreements and disclosure language for any client-facing AI application before launch.
- Form ADV Part 2A disclosures should be updated to reflect AI tool usage in research and communication workflows if material.

---

## The Five Initiatives

The following five initiatives are ordered by ease of implementation and time-to-value. They are not mutually exclusive — all five can be pursued simultaneously without conflict.

---

## Initiative 1 — Portfolio Surveillance Intelligence

**Category:** Research efficiency  
**Time to value:** 60–90 days  
**Estimated weekly time saved per PM:** 3–5 hours  
**Primary users:** All five portfolio managers

### The Problem

NCG spends over half its time continuing to research companies in the portfolio to make sure fundamentals remain strong, with the belief that the biggest risk to portfolios is an issue at one of their companies causing a sudden, unforeseen slowdown in growth.

This monitoring is currently done through a combination of earnings calls, SEC filings, news reading, and contact networks. With five portfolio managers covering companies across four cap strategies, the surface area is large. Signals that matter — a key executive departure, a competitor press release, a customer win in a job posting, a shift in R&D headcount — can appear in sources the team has no systematic way to monitor at scale.

The problem is not that NCG misses obvious signals. It is that important signals often sit in low-visibility sources: SEC Form 4 filings, job postings (a leading indicator of business momentum), patent applications, supplier announcements, and trade press — not the financial media the team already reads closely.

### The Solution

An AI-powered portfolio surveillance layer that monitors a configurable set of data sources for each portfolio company and surfaces material signals in a structured daily digest — organized by urgency and signal type.

**What it monitors per company:**
- SEC filings: 8-Ks, 10-Qs, 10-Ks, proxy statements, Form 4 insider transactions
- Earnings call transcripts: language change detection (e.g. guidance language shifts, changes in how management describes pipeline or demand)
- Job postings: headcount growth/contraction by department as a leading revenue indicator. A company adding 40 R&D roles while cutting 15 sales roles tells a different story than its IR deck.
- Patent filings: new IP filings or continuations in core product areas
- Trade and industry press: coverage in sector-specific publications beyond mainstream financial media
- Supplier and customer announcements: any public mentions of the portfolio company in third-party filings or press releases
- Congressional and regulatory activity: legislation or agency actions relevant to companies in healthcare, financials, or technology sectors

**Output format — Daily Digest:**
Each morning, each portfolio manager receives a structured digest for their assigned monitoring list. The digest is organized into three sections:

*Urgent (review today):*
Items that may affect position sizing decisions — unexpected 8-K filings, insider selling clusters, material guidance language changes, key executive departures. Maximum 3–5 items. These surface immediately via email/Slack, not just in the daily digest.

*Watch (review this week):*
Slower-moving signals that provide mosaic color — new job posting clusters, incremental patent filings, supply chain mentions. 5–10 items.

*Background (FYI):*
Routine filings, scheduled updates, analyst estimate revisions. Logged for reference, not highlighted.

**Competitive differentiation:**
This is where the initiative earns its keep in a growth-focused, microcap-heavy strategy. Large caps are covered by armies of sell-side analysts. NCG's edge in micro and small cap is identifying what others miss early. AI surveillance of low-visibility signals (job postings, patent filings, Form 4 clusters) is especially high-value in the smaller cap segments where information asymmetry is greater.

### Implementation Approach

**Recommended tooling:**
Tier 1 (start here): AlphaSense or Tegus for earnings call transcript analysis and filings; custom AI alert layer built on top using the Anthropic API or OpenAI API to generate the natural language digest summaries from structured data feeds.

Tier 2 (add within 6 months): LinkUp or Revelio Labs for job posting data as a leading business momentum indicator; Quandl or S&P Global for normalized SEC filing data.

**Estimated cost:** $2,000–$5,000/month in vendor subscriptions + API costs. Negligible relative to the analyst time it replaces or supplements.

**Data privacy:** No client data, no trading activity, and no proprietary research notes enter any AI system. Inputs are exclusively public data sources. Output (the digest) is internal only.

### Regulatory Notes

SEC recordkeeping rules require that AI-generated research summaries used in the investment process be retained as business records for the required period (currently 5 years for most records). The daily digest should be archived automatically. The CCO should confirm recordkeeping configuration before go-live.

---

## Initiative 2 — Universe Screening Acceleration

**Category:** Research capacity  
**Time to value:** 90–120 days  
**Estimated time saved:** 4–6 hours per new idea cycle  
**Primary users:** Portfolio managers conducting initial screens

### The Problem

NCG screens the universe of publicly traded companies to find companies in each market cap segment that are growing revenue at high rates. The initial screen is relatively mechanical — it filters by revenue growth rates. The harder work, as NCG describes it, is determining which companies have the rare mix of ingredients to sustain high growth.

Currently, once a company passes the quantitative screen, the team begins the qualitative work manually: reading 10-Ks, listening to earnings calls, mapping competitive dynamics, and assessing management quality. This is irreplaceable work. But the transition from "passes the screen" to "PMs have enough context to decide whether to invest research time" involves hours of reading that AI can substantially compress.

### The Solution

An AI-assisted pre-diligence layer that takes companies passing NCG's quantitative screen and generates a structured research briefing — not a buy/sell recommendation, but a 2-page synthesized summary of what is publicly known — so the PM can decide in 10 minutes whether to spend 10 hours on deeper diligence.

**What the briefing covers:**
- Business model summary (how the company makes money, primary revenue streams, unit economics if disclosed)
- Growth driver analysis: what is actually driving revenue growth? Is it volume, pricing, new products, geographic expansion, or market share gain? This distinction matters enormously for sustainability assessment.
- Competitive position: who are the primary competitors, and what does public evidence (job postings, customer reviews, patent filings, press) suggest about competitive dynamics?
- Management track record: how has management's guidance compared to actual results over the last 8 quarters?
- Key risk flags: any red flags in SEC filings, insider selling patterns, customer concentration, or audit disclosures?
- Analyst consensus vs. independent signal: where do sell-side estimates sit, and do alternative data signals (job postings, web traffic, app downloads where applicable) corroborate or diverge?

**What the briefing explicitly does not include:**
- A recommendation to buy, hold, or sell
- Price targets or valuation opinions
- Any claim about NCG's position or strategy

The briefing is a document that compresses public information. The PM's judgment — the mosaic — is what the briefing feeds. Not replaces.

**Efficiency math:**
If each new idea currently requires 6–8 hours of initial reading before a PM can determine whether to pursue deep diligence, and AI pre-briefings reduce that to 45 minutes of reading a structured summary — and NCG evaluates 50–100 new ideas per year across strategies — this initiative saves 250–500 PM hours annually. At a boutique firm where every PM hour is scarce and valuable, that is a material capacity expansion.

### Implementation Approach

This can be built as a lightweight internal tool using the Anthropic or OpenAI API with structured prompts for each section of the briefing. Inputs are public SEC filings, earnings call transcripts (from AlphaSense or direct sources), and job posting data.

Alternatively, Tegus, Visible Alpha, or similar platforms now offer AI-generated company summaries that can serve as a starting point, with NCG customizing the template to match its specific research checklist.

**Estimated cost:** $1,000–$3,000/month depending on data sourcing approach.

---

## Initiative 3 — Sell Discipline Signal Layer

**Category:** Risk management  
**Time to value:** 90 days (built on surveillance infrastructure from Initiative 1)  
**Primary users:** All portfolio managers  
**Regulatory sensitivity:** High — see notes below

### The Problem

NCG believes the biggest risk to its portfolios is an issue at one of its companies causing a sudden, unforeseen slowdown in growth. The firm's process includes admitting mistakes quickly and selling the impacted stock.

This sell discipline is a stated competitive advantage. The faster NCG identifies that a company's growth trajectory has changed, the less capital is exposed to the drawdown.

The challenge is that fundamental deterioration often shows up in leading indicators before it appears in reported financials. A company whose growth is slowing will often show the following sequence: first in job postings (hiring freezes in growth functions), then in customer behavior (web traffic, app store ratings, churn signals in public data), then in management language on earnings calls (subtle hedging, shifting from specific to vague guidance), then in reported revenue. By the time it is visible in revenue, the stock has often already moved.

### The Solution

A dedicated sell-signal monitoring layer — built on the same surveillance infrastructure as Initiative 1 — that applies specific algorithmic rules to portfolio companies and flags patterns associated with growth deceleration before they appear in financials.

**Sell signal indicators monitored:**

*Leading indicators (appear 2–4 quarters before revenue slowdown):*
- Job posting decline: net reduction in open roles in revenue-generating functions (sales, go-to-market, customer success) over rolling 90-day window. Historically predictive of revenue deceleration in SaaS and tech-enabled companies. (Source: Revelio Labs research, 2023)
- Management language shift: AI analysis of consecutive earnings call transcripts flagging statistically significant shifts from specific to vague language in growth commentary. Phrases like "we expect robust growth" becoming "we expect growth to continue" carry signal.
- Insider selling pattern: cluster analysis of Form 4 filings — distinguishing routine pre-planned 10b5-1 sales from discretionary sales by multiple insiders in a short window
- Customer review velocity: for companies with significant B2B customer bases, a declining G2/Capterra/Trustpilot review volume or sentiment score is an early churn indicator

*Concurrent indicators (appear 0–2 quarters before revenue impact):*
- Guidance language deterioration: narrowing or lowering of guidance ranges
- Customer concentration increase: when disclosed, rising dependence on top customers reduces predictability
- Sales cycle lengthening: language in earnings calls referencing elongated enterprise sales cycles — historically a leading indicator of SMB/mid-market weakness spreading upward

*Lagging confirmation (visible in financials):*
- Revenue growth deceleration vs. prior quarter and vs. own prior guidance
- Gross margin compression not explained by deliberate investment
- Billings-to-revenue divergence (for subscription businesses)

**Output:**
When two or more leading indicators fire simultaneously for a portfolio company, the system generates an alert — not a sell recommendation — delivered immediately to the relevant portfolio manager. The alert includes: which indicators fired, the source data, the company's current position size and strategy weight, and a link to the most recent earnings transcript for context.

The PM decides what to do with it. The system provides no guidance on that decision.

**Why this is high-value for NCG specifically:**
NCG's sell discipline is already strong relative to most growth managers. The goal is not to change the discipline — it is to give the team more time before a deteriorating signal becomes obvious to the market. In micro and small cap, where positions may be less liquid, early detection creates optionality that late detection does not.

### Regulatory Notes

This is the highest-sensitivity initiative from a regulatory perspective. The SEC's 2023 guidance on AI in investment processes specifically addresses predictive algorithms that inform trading decisions. Key requirements:

- The sell signal system must be documented as a research tool, not a discretionary signal. All alerts are inputs to PM review, not automated triggers.
- The methodology (which indicators fire, thresholds, data sources) must be documented in writing and reviewed by the CCO
- If clients ask how investment decisions are made, the Form ADV Part 2A must disclose that AI tools are used as research inputs in the monitoring process
- Vendor agreements for data sources (Revelio, AlphaSense, etc.) must include provisions prohibiting the use of NCG's usage patterns or investment behavior as training data

---

## Initiative 4 — Client Communication Accelerator

**Category:** Business development and client value  
**Time to value:** 30–60 days  
**Primary users:** John Solecki (institutional sales), portfolio managers (quarterly commentary)  
**Revenue impact:** Direct — better materials and faster response enable institutional sales at scale

### The Problem

NCG has one institutional sales professional covering a national and international (UCITS) client and prospect base. John Solecki joined in 2022 with 26 years of institutional sales experience, prior roles at Morgan Dempsey, Horizon Kinetics, and Mesirow Financial, and co-authored a white paper on hidden value in spin-offs.

The constraint is not experience — it is bandwidth. Institutional sales at boutique asset managers requires producing high-quality, personalized materials: RFP responses (which can run 50–150 pages), custom attribution analyses for specific consultants, strategy-specific pitch decks tailored to prospect type (endowment vs. corporate pension vs. family office), and timely responses to client questions about portfolio positioning.

Additionally, the quarterly commentary — NCG's primary client-facing content — is written by portfolio managers whose primary value is research, not writing. AI can make the production of that commentary faster without changing the judgment and analysis it contains.

### The Solutions

**4a — RFP Response Acceleration**

Institutional RFPs are highly structured but extremely time-consuming. A single RFP response can consume 20–40 hours of research, writing, and formatting time.

AI can reduce this to 4–6 hours by:
- Maintaining a structured "answer library" of NCG's responses to the 200+ most common RFP questions, organized by question type (philosophy, process, risk management, team, track record, fees, operations)
- When a new RFP arrives, AI maps each question to the best existing answer from the library and generates a draft response with appropriate formatting
- John reviews, edits, and customizes — especially for questions requiring current positioning or prospect-specific context
- The PM review ensures accuracy and tone before submission

This is not AI writing about NCG's investment process. It is AI assembling NCG's own words — maintained, reviewed, and periodically updated by the team — in response to standardized questions. The analogy is a very fast, very organized research assistant who has read every previous RFP NCG has ever submitted.

**4b — Quarterly Commentary Production Support**

NCG's quarterly letter reviews general market and portfolio trends, followed by more in-depth comments on each portfolio, including performance attribution, new investments, and sector positioning.

The quarterly commentary is NCG's most important recurring client communication. It is also one that requires portfolio manager time to produce — time that competes directly with research.

AI can assist in two ways:
First, structure generation: given performance data inputs (which the team already has), AI drafts the performance attribution tables, generates the sector positioning summary, and structures the document. The PMs write the qualitative analysis and market commentary — the part that requires judgment — and the AI handles the structural scaffolding.

Second, distribution personalization: the same quarterly content can be adapted into shorter client-specific summaries for institutional investors who want a 1-page executive summary rather than the full letter. AI creates the summary version; John reviews and approves before sending.

**4c — Prospect Research Briefs**

Before any institutional sales meeting, John needs a briefing on the prospect: who they are, what they currently own (from public 13F filings), what their stated investment criteria are, and how NCG's strategies fit their existing allocation.

AI can generate this prospect brief automatically from public sources (13F filings via SEC EDGAR, organization websites, any public investment policy statements or RFP criteria published by the institution) in 10 minutes. What currently takes 2–3 hours of pre-meeting research becomes a quick review of an AI-generated brief.

### Implementation Approach

**4a (RFP library):** Tools like Loopio, Responsive, or a custom GPT-4/Claude-based assistant with a structured knowledge base. Estimated setup time: 4–6 weeks to build the answer library. Ongoing: 30 minutes/week to update with new answers.

**4b (Commentary):** A Claude or GPT-4 assistant with access to the quarter's performance data and a template that reflects NCG's commentary structure and voice. Estimated setup: 2 weeks. The template should be trained on prior quarters' approved commentaries so the AI understands NCG's voice and conventions.

**4c (Prospect briefs):** Python script + LLM API that pulls 13F data from SEC EDGAR, scrapes public org info, and generates a structured brief. Estimated setup: 3–4 weeks with a developer.

**Estimated cost:** $500–$1,500/month across all three tools.

**Regulatory notes for client communications:**
All AI-generated client-facing content must be reviewed and approved by a portfolio manager before distribution. Under SEC rules, communications to clients and prospects are the adviser's responsibility regardless of how they were produced. The firm's supervisory procedures should be updated to include an AI review step in the communication approval workflow.

---

## Initiative 5 — Institutional Sales Intelligence Dashboard

**Category:** Business development  
**Time to value:** 60–90 days  
**Primary users:** John Solecki, Bob Scott (CEO)  
**Revenue impact:** Direct — better pipeline visibility enables prioritized sales activity

### The Problem

Institutional asset management sales operates on long cycles — 12–36 months from first contact to funded mandate. With one sales professional managing a national pipeline, visibility into where prospects are in that cycle, which existing clients are at risk of redemption, and which new prospects are most likely to convert is critical to allocating limited sales time effectively.

NCG currently has no CRM infrastructure specifically built for institutional asset management sales intelligence. Standard CRMs (Salesforce, HubSpot) are not designed for the specific data types and decision cycles of institutional investment sales.

### The Solution

A lightweight AI-enhanced sales intelligence system built on an institutional-grade CRM (Backstop Solutions or Dynamics 365 with a custom asset management configuration) with an AI layer that surfaces the following:

**Pipeline health monitoring:**
- Prospect engagement scoring: based on email open rates, document downloads, and meeting history, flag prospects who have gone cold after initial engagement — a signal that outreach frequency or messaging should change
- Mandate timeline tracking: institutional investors typically issue RFPs on predictable cycles. AI monitors public procurement announcements, board meeting schedules (where public), and industry databases to flag when target institutions may be entering a manager review cycle

**Existing client retention signals:**
- Underperformance sensitivity alerts: when NCG's strategy performance vs. benchmark crosses certain thresholds (e.g. trailing 1-year underperformance exceeding 200bps), flag clients who have historically requested calls or reduced allocations following underperformance. These clients need proactive outreach before they initiate it.
- Redemption risk scoring: based on engagement patterns and performance sensitivity, assign each client a quarterly retention risk score. Low scores trigger John to schedule a proactive check-in.

**New prospect identification:**
- 13F monitoring for target prospects: when an institution's quarterly 13F filing shows they are increasing allocations to small/mid cap growth strategies (competitors of NCG), they are a warm prospect. AI parses 13F filings quarterly and surfaces new institutions entering NCG's target allocation category.
- Consultant database monitoring: investment consultants (Mercer, Aon, Cambridge Associates, NEPC) publish research and manager recommendations. AI monitors public consultant communications for mentions of growth strategies or manager searches relevant to NCG's strategies.

### Implementation Approach

Backstop Solutions is the purpose-built CRM for boutique asset managers and hedge funds — it integrates natively with 13F data, performance reporting, and institutional contact databases (eVestment, Preqin). Adding an AI summary layer on top of Backstop's existing data via API is feasible within 60–90 days.

Estimated cost: Backstop licensing ($2,000–$4,000/month for a firm NCG's size) + AI layer build ($5,000–$10,000 one-time development) + LLM API costs (~$200–$500/month).

---

## Prioritization Matrix

| Initiative | Time to Value | Annual Time Saved | Revenue Impact | Regulatory Complexity | Recommended Priority |
|---|---|---|---|---|---|
| 1 — Portfolio Surveillance | 60–90 days | 750–1,300 PM hours | Indirect (better research) | Low | **Start now** |
| 4 — Client Comm Accelerator | 30–60 days | 200–400 hours (sales + PMs) | Direct (faster RFPs, more capacity) | Medium | **Start now** |
| 2 — Universe Screening | 90–120 days | 250–500 PM hours | Indirect (more ideas evaluated) | Low | **Phase 2** |
| 3 — Sell Signal Layer | 90 days (after Init. 1) | Difficult to quantify — risk reduction | Indirect (loss prevention) | High | **Phase 2 with CCO review** |
| 5 — Sales Intelligence | 60–90 days | 100–200 hours (sales) | Direct (pipeline conversion) | Low | **Phase 2** |

---

## What AI Should Not Do at NCG

This document would be incomplete without being explicit about where AI has no role in NCG's process.

**AI should not generate investment opinions.** The mosaic — NCG's synthesis of management conversations, industry contacts, financial models, and competitive analysis into a conviction about a company's growth trajectory — is the firm's intellectual property and its competitive advantage. No AI tool should be positioned as a substitute for that judgment, and none of the initiatives above attempt to be.

**AI should not communicate directly with clients.** All client communication is a regulated activity. AI can draft; a portfolio manager or John Solecki approves and sends.

**AI should not make or trigger trades.** NCG's trading decisions are made by portfolio managers and executed by Joe Grundfeldt. No AI system in this document touches the trading workflow.

**AI should not ingest proprietary research notes or client data.** The intellectual capital of the firm — internal research memos, portfolio manager debate notes, client account information — must never enter a third-party AI model's training data. All vendor agreements must prohibit this explicitly.

**AI should not replace the team's instinct for admitting mistakes.** NCG's process of admitting mistakes quickly and selling the impacted stock depends on a culture of intellectual honesty among the partners, not on an algorithm telling them they're wrong. The sell signal layer (Initiative 3) is an early warning tool — not an override of that culture.

---

## Immediate Next Steps

The following actions can begin within 30 days with no capital commitment:

1. **CCO briefing:** Kelly McNulty reviews this document and identifies which initiatives require Form ADV disclosure updates, supervisory procedure changes, or vendor agreement review before implementation.

2. **Vendor evaluation — AlphaSense vs. Tegus:** Request demos of both platforms specifically for earnings call transcript AI analysis and SEC filing monitoring. Evaluate against the surveillance use case in Initiative 1. Decision should be made within 45 days.

3. **RFP answer library audit:** John Solecki pulls the last 10 RFP responses submitted and maps the questions to a structured answer library format. This is the foundational work for Initiative 4a and requires no technology — just 2 days of document review.

4. **Identify one quarterly commentary cycle as a pilot:** The Q1 2026 commentary (due April 2026) is a natural first test for Initiative 4b's production support. PMs agree on which sections of the commentary are appropriate for AI-assisted structuring vs. require direct PM writing.

5. **Data handling policy:** Before any AI vendor is engaged, the CCO and COO (Peter Capouch) agree on a written data handling policy covering: what data categories may enter AI systems, which require additional legal review, and how outputs are retained for recordkeeping purposes.

---

*This document was prepared as a strategic assessment based on publicly available information about NCG at ncgrowth.com. All regulatory references are based on publicly available SEC guidance current as of March 2026. This document does not constitute legal or compliance advice — regulatory implementation should be reviewed by qualified securities counsel.*
