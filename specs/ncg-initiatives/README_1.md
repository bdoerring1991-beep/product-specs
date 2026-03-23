# NCG AI Initiatives — Implementation Build Guides

This folder contains step-by-step technical build guides for each of the five
AI initiatives outlined in the [NCG AI Strategy Assessment](../ncg-ai-strategy-assessment.md).

Each file answers the same question: **given the strategy, exactly how do you build it?**

---

## Folder Structure

```
ncg-ai-strategy-assessment.md       ← Parent strategy document (one level up)
ncg-initiatives/
  README.md                          ← This file
  initiative-1-portfolio-surveillance.md
  initiative-2-universe-screening.md
  initiative-3-sell-signal-layer.md
  initiative-4-client-communications.md
  initiative-5-sales-intelligence.md
```

---

## Initiative Index

| # | File | Category | Priority | Est. Build Time | Monthly Cost |
|---|---|---|---|---|---|
| 1 | [Portfolio Surveillance](./initiative-1-portfolio-surveillance.md) | Research efficiency | Start now | 6–8 weeks | $2,500–$5,500 |
| 2 | [Universe Screening](./initiative-2-universe-screening.md) | Research capacity | Phase 2 | 8–10 weeks | $1,500–$3,500 |
| 3 | [Sell Signal Layer](./initiative-3-sell-signal-layer.md) | Risk management | Phase 2 + CCO | 10–12 weeks | $2,000–$4,000 |
| 4 | [Client Communications](./initiative-4-client-communications.md) | Biz dev / client value | Start now | 4–6 weeks | $600–$1,800 |
| 5 | [Sales Intelligence](./initiative-5-sales-intelligence.md) | Business development | Phase 2 | 8–10 weeks | $2,500–$5,000 |

---

## How to Read These Files

Each guide is structured identically so that any implementation can be handed
off to a developer, a fractional CTO, or a technical consultant without
re-explaining context. Every file contains:

- **Architecture overview** — how the pieces connect at a system level
- **Prerequisites** — vendor accounts, API keys, and data agreements needed before writing a line of code
- **Step-by-step build** — numbered implementation phases with real code
- **Prompt templates** — the exact LLM prompts used in each AI layer
- **Testing checklist** — how to validate the build before going live
- **Go-live checklist** — operational and compliance steps before switching on
- **Cost breakdown** — itemized monthly and one-time costs
- **Ongoing maintenance** — who does what, and how often

---

## Shared Technical Decisions (Apply to All Initiatives)

These decisions are made once and apply across all five builds.

**Language:** Python 3.11+. All scripts are designed to run on a small cloud
instance (AWS EC2 t3.medium or equivalent) or as scheduled Lambda functions.
No on-premise infrastructure required.

**LLM:** Anthropic Claude API (`claude-sonnet-4-6` for production, `claude-haiku-4-5`
for high-volume low-complexity tasks like classification). Zero Data Retention
enabled on the Anthropic API to prevent NCG data from being used in training.

**Secrets management:** AWS Secrets Manager or a `.env` file with strict access
control. API keys for AlphaSense, Revelio, SEC EDGAR, and Anthropic never appear
in source code or version control.

**Recordkeeping:** All AI-generated outputs (digests, briefings, alerts, drafts)
are automatically archived to an S3 bucket with a 7-year retention policy to
satisfy SEC Books and Records requirements under Rule 204-2 of the Advisers Act.

**Data isolation:** No client account data, no proprietary research notes, and
no NCG trading activity ever enters any AI API call. All LLM inputs are
constructed exclusively from public data sources.

**Version control:** All code lives in a private GitHub repository accessible
only to NCG's designated technical contact and any authorized contractors.

---

## One-Time Setup (Do This Before Any Initiative)

The following setup steps are shared infrastructure that every initiative builds on.
Complete these before starting any individual initiative build.

### 1. AWS Account Setup

```bash
# Create a dedicated AWS account for NCG AI infrastructure
# Do not use a personal or shared account

# Install AWS CLI
pip install awscli
aws configure  # Enter access key, secret, region (us-east-1 recommended)

# Create S3 bucket for recordkeeping
aws s3 mb s3://ncg-ai-recordkeeping --region us-east-1

# Enable versioning on the bucket
aws s3api put-bucket-versioning \
  --bucket ncg-ai-recordkeeping \
  --versioning-configuration Status=Enabled
```

### 2. Anthropic API Setup

```bash
# Install the Anthropic Python SDK
pip install anthropic

# Store API key in environment (never hardcode)
export ANTHROPIC_API_KEY="your-key-here"

# Test connection
python3 -c "
import anthropic
client = anthropic.Anthropic()
message = client.messages.create(
    model='claude-sonnet-4-6',
    max_tokens=100,
    messages=[{'role': 'user', 'content': 'Reply OK if connected.'}]
)
print(message.content[0].text)
"
```

### 3. Base Requirements File

Create `requirements.txt` at the root of the project repo:

```
anthropic>=0.25.0
requests>=2.31.0
pandas>=2.0.0
boto3>=1.34.0
python-dotenv>=1.0.0
schedule>=1.2.0
slack-sdk>=3.27.0
beautifulsoup4>=4.12.0
lxml>=5.0.0
```

### 4. Environment File Template

Create `.env.template` (never commit `.env` itself):

```
ANTHROPIC_API_KEY=
ALPHASENSE_API_KEY=
REVELIO_API_KEY=
SEC_EDGAR_USER_AGENT=NCG-AI-System contact@ncgrowth.com
SLACK_BOT_TOKEN=
AWS_ACCESS_KEY_ID=
AWS_SECRET_ACCESS_KEY=
S3_RECORDKEEPING_BUCKET=ncg-ai-recordkeeping
```

---

*All build guides reference this README for shared context.
Last updated: March 2026.*
