# n8n Workflow Collection

> A progressive, production-inspired collection of **automation and AI agent workflows** built with [n8n](https://n8n.io) — spanning lead management, web scraping, multimodal document processing, multi-agent personal assistants, and Retrieval-Augmented Generation (RAG) pipelines.

Whether you are **learning workflow automation**, **evaluating automation engineering skills**, or **looking for ready-to-import templates**, this repository is structured as a clear progression from foundational integrations to advanced AI agent architectures.

---

## 🎯 Why This Repository?

- **For Recruiters & Hiring Managers:** Demonstrates end-to-end automation engineering — REST APIs, OAuth2 integrations, custom JavaScript data normalization, batching with rate-limit pacing, RPA bridging (UiPath), AI tool-calling agents, sub-agent orchestration, and vector-database RAG pipelines.
- **For Learners:** Workflows are numbered in a progressive learning path (`1` → `10`), with plain-English explanations, ASCII architecture diagrams, and exact node breakdowns so you can import and understand each pattern in minutes.

### Core Capabilities Demonstrated

| Domain | Technologies & Patterns |
|---|---|
| **AI Agents & Orchestration** | LangChain AI Agents in n8n, Google Gemini, Structured Output Parsing, Window Buffer Memory, Sub-Agent Workflow Delegation |
| **RAG & Vector Databases** | PDF Ingestion, Recursive Character Text Splitting, Gemini Embeddings, In-Memory Vector Store & Pinecone Serverless |
| **Multimodal AI** | Telegram Voice Transcription, Receipt/Invoice Vision OCR, PDF Text Extraction |
| **Data & Lead Pipelines** | Apify Web Scraping, Array Splitting/Aggregation, JS Sorting & Currency Normalization, Google Sheets Upserts |
| **Messaging & Workspace** | Gmail API (OAuth2), Google Calendar, Google Contacts, Google Drive Triggers, Telegram Bot API |
| **Hybrid RPA** | Shell-triggered UiPath Robot execution from n8n |

---

## 📂 Workflows at a Glance

### Progressive Learning Path (`1` → `10`)

| # | Workflow | Level | Trigger | Key Stack |
|---|---|---|---|---|
| 1 | [Form Lead → Sheets + Email Responder](#1-form-lead--sheets--email-responder) | Beginner | Web Form | n8n Form, Google Sheets, Gmail |
| 2 | [Qualified Lead Processor & Email Digest](#2-qualified-lead-processor--email-digest) | Beginner | Manual / Schedule | Filter, Aggregate, JavaScript, Sheets, Gmail |
| 3 | [AI Email Inbox Sorter](#3-ai-email-inbox-sorter) | Intermediate | Hourly Schedule | Gmail, Google Gemini, Switch |
| 4 | [Google Maps Lead Scraper & Sheet Sync](#4-google-maps-lead-scraper--sheet-sync) | Intermediate | Schedule | Apify Actor, Batch Loop, Wait, Sheets Upsert |
| 5 | [Telegram Invoice Data Extractor](#5-telegram-invoice-data-extractor) | Intermediate | Telegram File | Telegram Bot, Gemini Vision OCR, PDF Extract, JS, Sheets |
| 6 | [AI Email Assistant with Contacts Lookup](#6-ai-email-assistant-with-contacts-lookup) | Advanced AI | Chat Trigger | AI Agent, Gemini, Memory, Google Contacts, Gmail |
| 7 | [Telegram Personal Assistant (Multi-Agent)](#7-telegram-personal-assistant-multi-agent) | Advanced AI | Telegram (Text/Voice) | Voice Transcription, Email Sub-Agent, Calendar Sub-Agent (`7.1`) |
| 8 | [Website Chatbot](#8-website-chatbot) | Intermediate AI | Public Chat Webhook | Chat Trigger, AI Agent, Gemini, Window Memory |
| 9 | [RAG Chatbot (In-Memory Vector Store)](#9-rag-chatbot-in-memory-vector-store) | RAG | Drive / Chat Trigger | Google Drive, PDF Extract, Text Splitter, Gemini Embeddings, In-Memory Store |
| 10 | [RAG Chatbot with Pinecone](#10-rag-chatbot-with-pinecone) | Production RAG | Drive / Chat Trigger | Pinecone API, Pinecone Vector Store, Gemini Embeddings, AI Agent |

### Foundational Utilities

| Utility | Purpose | File |
|---|---|---|
| **Gmail Attachment Extractor** | Pulls email attachments and writes binary files to disk | [`gmail with attach read_write.json`](./gmail%20with%20attach%20read_write.json) |
| **Gmail Body Processor** | Parses email attachments, saves locally, labels, marks read, and replies | [`gmail with body read_write.json`](./gmail%20with%20body%20read_write.json) |
| **n8n → UiPath RPA Trigger** | Launches a local UiPath desktop robot via shell command | [`n8n Trigger UIipath.json`](./n8n%20Trigger%20UIipath.json) |

---

## 🔍 Workflow Details

### 1. Form Lead → Sheets + Email Responder

**File:** [`1.Form Lead to Sheets + Email Responder.json`](./1.Form%20Lead%20to%20Sheets%20%2B%20Email%20Responder.json)

**What it does:**
Hosts a "Contact Us / Get a Quote" web form directly in n8n. When a prospect submits their Name, Email, Service (`SEO` or `ADS`), and Budget, the workflow logs the lead into Google Sheets and sends an automated confirmation email.

```text
n8n Form Trigger (Name, Email, Service, Budget)
    ├──→ Google Sheets (Append lead row)
    └──→ Gmail (Send confirmation email)
```

**Skills demonstrated:** Web form triggers, Google Sheets API, automated email responses, lead capture.

---

### 2. Qualified Lead Processor & Email Digest

**File:** [`2.Qualified Lead Processor to Google Sheets & Email.json`](./2.Qualified%20Lead%20Processor%20to%20Google%20Sheets%20%26%20Email.json)

**What it does:**
Processes incoming lead arrays, filters out inquiries below `$1,000`, logs qualified leads to Google Sheets, sorts them from highest to lowest budget using custom JavaScript, formats a clean summary, and emails a prioritized digest to the sales team.

```text
Trigger → Split Out → Filter (Budget ≥ $1,000)
    → Google Sheets (Append row)
    → Aggregate → JS (Sort descending) → JS (Format digest) → Gmail (Send report)
```

**Skills demonstrated:** Array splitting/aggregation, conditional filtering, custom JavaScript sorting & formatting, automated reporting.

---

### 3. AI Email Inbox Sorter

**File:** [`3.Email inbox.json`](./3.Email%20inbox.json)

**What it does:**
Polls Gmail every hour for new messages, sends the sender, subject, and body to Google Gemini for classification, and automatically applies the matching Gmail label (`Promotions`, `Socials`, `Personal`, `Sales`, or `Misc`).

```text
Gmail Trigger (Hourly)
    → Google Gemini (Classify email)
        → Switch (Route by category)
            → Gmail (Apply label)
```

**Skills demonstrated:** Event-driven scheduling, LLM text classification, conditional routing, Gmail label automation.

---

### 4. Google Maps Lead Scraper & Sheet Sync

**File:** [`4.Google Maps Cleaner Lead Scraper & Sheet Sync.json`](./4.Google%20Maps%20Cleaner%20Lead%20Scraper%20%26%20Sheet%20Sync.json)

**What it does:**
Runs on a schedule to trigger an Apify Google Maps scraper, filters for high-rated businesses (`totalScore >= 4.8`), loops through results in batches, and upserts records into Google Sheets (matching by website) with built-in `Wait` delays to respect API rate limits.

```text
Schedule Trigger → Apify Actor (Google Maps Scraper)
    → Filter (Rating ≥ 4.8)
        → Loop Over Items → Google Sheets (Upsert by Website) → Wait (Rate-limit delay)
```

**Skills demonstrated:** Third-party scraping APIs (Apify), data quality filtering, batch iteration, rate-limit pacing, database-style upserts.

---

### 5. Telegram Invoice Data Extractor

**File:** [`5.Invoice Data Extractor_ Telegram to Sheets.json`](./5.Invoice%20Data%20Extractor_%20Telegram%20to%20Sheets.json)

**What it does:**
Turns a Telegram bot into an automated receipt and invoice intake pipeline. Users send a PDF or PNG invoice to the bot; n8n routes by file type, extracts text (via PDF parser or Gemini Vision OCR), parses structured line items into strict JSON, normalizes currencies/numbers in JavaScript, logs each item to Google Sheets, and replies with a summary on Telegram.

```text
Telegram Trigger → Download File → Switch (MIME type)
    ├── PDF → Extract Text ──────┐
    └── PNG → Gemini Vision OCR ─┴→ Gemini (Structured JSON)
                                        → JS (Normalize currency/numbers)
                                        → Split Out → Google Sheets (Append rows)
                                        → Telegram (Send confirmation)
```

**Skills demonstrated:** Multimodal AI & OCR, PDF parsing, structured JSON extraction, JavaScript data normalization, bidirectional Telegram bot UX.

---

### 6. AI Email Assistant with Contacts Lookup

**File:** [`6.AI Email Assistant with Google Contacts Lookup.json`](./6.AI%20Email%20Assistant%20with%20Google%20Contacts%20Lookup.json)

**What it does:**
A chat-based AI assistant powered by Gemini (`gemini-3.6-flash`) with conversation memory and structured output parsing. Before sending an email on the user's behalf, the agent enforces a rule to look up the recipient's address in Google Contacts first.

```text
Chat Trigger → AI Agent (Gemini + Buffer Memory + Structured Output)
    ├── Tool: Google Contacts (Search recipient email)
    └── Tool: Gmail (Send email)
```

**Skills demonstrated:** AI tool-calling agents, prompt-enforced tool sequencing, Google Contacts API, structured JSON output.

---

### 7. Telegram Personal Assistant (Multi-Agent)

**Files:**
- Main Orchestrator: [`7.Personal Assistant.json`](./7.Personal%20Assistant.json)
- Calendar Sub-Agent: [`7.1Sub Calender Agent.json`](./7.1Sub%20Calender%20Agent.json)

**What it does:**
A voice-and-text Telegram personal assistant powered by a multi-agent architecture. If the user sends a voice note, n8n downloads and transcribes it using Gemini before passing the text to the main AI Agent. The orchestrator delegates inbox management to an internal **Email Sub-Agent** (with Google Contacts + Gmail tools) and calendar tasks to an external **Calendar Sub-Agent** (`7.1Sub Calender Agent.json`) via n8n's `Call n8n Workflow Tool`.

```text
Telegram Trigger (Text or Voice)
    ├── Voice → Download Audio → Gemini Transcribe ─┐
    └── Text ───────────────────────────────────────┴→ Main AI Agent (Orchestrator)
                                                            ├── Email Sub-Agent (Contacts + Gmail)
                                                            └── Tool Workflow → 7.1 Calendar Sub-Agent
                                                                    → Telegram Reply
```

**Skills demonstrated:** Multi-agent orchestration, sub-workflow delegation, voice-to-text multimodal routing, per-chat session memory.

---

### 8. Website Chatbot

**File:** [`8. Website chatbot.json`](./8.%20Website%20chatbot.json)

**What it does:**
A lightweight, embeddable public webhook chatbot powered by Google Gemini and Buffer Window Memory for multi-turn website visitor conversations.

```text
Public Chat Trigger (Webhook) → AI Agent (Gemini + Window Buffer Memory)
```

**Skills demonstrated:** Public chat webhooks, conversational memory management, rapid chatbot deployment.

---

### 9. RAG Chatbot (In-Memory Vector Store)

**File:** [`9.RAG.json`](./9.RAG.json)

**What it does:**
A complete two-part Retrieval-Augmented Generation (RAG) pipeline using n8n's built-in In-Memory Vector Store:
1. **Ingestion Pipeline:** Watches a Google Drive folder for new PDFs, extracts text, chunks it with a Recursive Character Text Splitter, embeds chunks with Google Gemini Embeddings, and stores them in memory.
2. **Query Pipeline:** A chat-triggered AI Agent with Buffer Window Memory queries the vector store as a tool (`topK: 10`) and answers strictly from retrieved company knowledge.

```text
[Ingestion] Google Drive Trigger → Download PDF → Extract Text
                → Recursive Text Splitter + Gemini Embeddings → In-Memory Vector Store

[Retrieval] Chat Trigger → AI Agent (Gemini + Memory)
                └── Tool: In-Memory Vector Store (retrieve-as-tool)
```

**Skills demonstrated:** RAG architecture, document chunking strategies, vector embeddings, grounded LLM responses.

---

### 10. RAG Chatbot with Pinecone

**File:** [`10.RAG with pinecone.json`](./10.RAG%20with%20pinecone.json)

**What it does:**
Upgrades the RAG pipeline to a persistent, cloud-hosted **Pinecone Serverless** vector database:
1. **Index Setup:** Includes an HTTP Request node to provision the `n8n-vectors` serverless index (`dimension: 3072`, `cosine` metric) via the Pinecone REST API.
2. **Ingestion:** Monitors Google Drive for new PDFs, splits and embeds text with Gemini Embeddings, and upserts vectors into Pinecone under the `docs` namespace (`clearNamespace: true` for clean updates).
3. **Retrieval:** A customer-service AI Agent queries Pinecone (`retrieve-as-tool`) to deliver accurate, grounded answers with conversation memory.

```text
[Setup]     HTTP Request (POST api.pinecone.io/indexes)

[Ingestion] Google Drive Trigger → Download PDF → Extract Text
                → Recursive Text Splitter + Gemini Embeddings → Pinecone Vector Store (docs)

[Retrieval] Chat Trigger → AI Agent (Gemini + Memory)
                └── Tool: Pinecone Vector Store (retrieve-as-tool)
```

**Skills demonstrated:** Production RAG with external vector DBs (Pinecone), REST API index provisioning, namespace management, grounded AI customer support.

---

## 🚀 Getting Started

### Prerequisites

| Service / Tool | Purpose | Used In |
|---|---|---|
| **n8n** (`v1.0+`) | Self-hosted ([Docs](https://docs.n8n.io/hosting/)) or [n8n Cloud](https://n8n.cloud) | All workflows |
| **Google OAuth2** | Gmail, Google Sheets, Google Drive, Google Contacts, Google Calendar | Workflows 1–7, 9, 10 |
| **Google Gemini API Key** | LLM chat, vision OCR, audio transcription, embeddings ([AI Studio](https://aistudio.google.com)) | Workflows 3, 5–10 |
| **Telegram Bot Token** | Bot triggers & replies via [@BotFather](https://t.me/botfather) | Workflows 5, 7 |
| **Apify API Token** | Google Maps scraping actor ([Apify](https://apify.com)) | Workflow 4 |
| **Pinecone API Key** | Serverless vector database ([Pinecone](https://www.pinecone.io)) | Workflow 10 |
| **UiPath Robot** | Local desktop RPA execution | Utility (`n8n Trigger UIipath.json`) |

### How to Import a Workflow

1. Open your n8n instance (e.g., `http://localhost:5678`).
2. Click **Add Workflow** → **⋮ Menu** → **Import from File**.
3. Select any `.json` file from this repository.
4. Open each node with a credential warning and connect your own credentials inside n8n.
5. For **Workflow 7**, import `7.1Sub Calender Agent.json` first and link its workflow ID inside `7.Personal Assistant.json`.
6. For **Workflow 10**, replace `YOUR_PINECONE_API_KEY` in the index-creation HTTP Request node if running index setup manually, and connect your Pinecone credential.

**Or import via n8n CLI:**
```bash
n8n import:workflow --input="10.RAG with pinecone.json"
```

> **Security Note:** All workflow exports keep credentials out of version control (referencing n8n credential IDs or placeholders). Always verify no personal API keys are hardcoded before committing changes.

---

## 🗂️ Project Structure

```text
n8n-Workflows/
├── 1.Form Lead to Sheets + Email Responder.json
├── 2.Qualified Lead Processor to Google Sheets & Email.json
├── 3.Email inbox.json
├── 4.Google Maps Cleaner Lead Scraper & Sheet Sync.json
├── 5.Invoice Data Extractor_ Telegram to Sheets.json
├── 6.AI Email Assistant with Google Contacts Lookup.json
├── 7.Personal Assistant.json
├── 7.1Sub Calender Agent.json
├── 8. Website chatbot.json
├── 9.RAG.json
├── 10.RAG with pinecone.json
├── gmail with attach read_write.json
├── gmail with body read_write.json
├── n8n Trigger UIipath.json
└── README.md
```

---

## 🤝 Contributing

Contributions are welcome! To add a new workflow:

1. Build and test your workflow in n8n.
2. Export it via **☰ Menu → Download** (`.json`).
3. Ensure no plaintext API keys, tokens, or personal credentials remain in the JSON.
4. Update `README.md` with the workflow entry, flow diagram, and prerequisites.
5. Open a pull request describing what the workflow automates.

---

*Built with [n8n](https://n8n.io) — the fair-code workflow automation platform.*
