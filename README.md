# n8n Workflow Collection

> A hands-on collection of real-world **automation workflows** built with [n8n](https://n8n.io) — covering AI-powered email management, lead qualification & routing, Apify web scraping, Telegram multimodal invoice processing, and RPA integration with UiPath.

Whether you're just learning automation, hiring for automation skills, or building production pipelines — this repo shows practical, working examples you can import and run today.

---

## What is n8n?

**n8n** (pronounced *"n-eight-n"*) is an open-source workflow automation tool. Think of it like a visual programming environment where you connect apps, APIs, and services using drag-and-drop "nodes" — no heavy coding required.

- ✅ Free & self-hostable
- ✅ 400+ built-in integrations (Gmail, Sheets, Slack, Telegram, Apify, HTTP, databases…)
- ✅ Supports AI/LLM nodes (Google Gemini, OpenAI, LangChain)
- ✅ Can trigger UiPath robots, run shell commands, and custom JavaScript

---

## 📂 Workflows at a Glance

| # | Workflow | Trigger | What it does |
|---|---|---|---|
| 1 | [AI Email Inbox Sorter](#1-ai-email-inbox-sorter) | Every hour (auto) | Reads new Gmail emails → uses Gemini AI to categorize them → applies Gmail labels |
| 2 | [Form Lead → Sheets + Email Responder](#2-form-lead--sheets--email-responder) | Web form submission | Captures a lead from a contact form → saves to Google Sheets → sends a confirmation email |
| 3 | [Gmail Attachment Extractor](#3-gmail-attachment-extractor) | Manual | Fetches emails → extracts attachments → saves files to disk |
| 4 | [Gmail Body Processor](#4-gmail-body-processor) | Schedule or Manual | Reads email bodies → extracts data → labels, marks as read, sends replies |
| 5 | [n8n → UiPath RPA Trigger](#5-n8n--uipath-rpa-trigger) | Manual | Triggers a UiPath robot via a shell command from within n8n |
| 6 | [Qualified Lead Processor & Email Digest](#6-qualified-lead-processor--email-digest) | Manual or Scheduled | Filters high-value leads ($1,000+) → saves to Sheets → sorts by budget → emails daily digest |
| 7 | [Google Maps Lead Scraper & Sheet Sync](#7-google-maps-lead-scraper--sheet-sync) | Schedule (auto) | Triggers Apify Google Maps scraper → filters high-rated leads (4.8+★) → batch upserts to Sheets |
| 8 | [Telegram Invoice Data Extractor](#8-telegram-invoice-data-extractor) | Telegram message (file) | Receives PDF/Image invoices via Telegram → extracts text with Gemini AI → logs line items to Sheets → replies on Telegram |

---

## 🔍 Workflow Details

### 1. AI Email Inbox Sorter

**File:** [`Email inbox.json`](./Email%20inbox.json)

**What it does in plain English:**
Every hour, this workflow checks your Gmail for new emails. It sends each email's sender, subject, and body to Google Gemini (an AI model). The AI reads it and decides which category it belongs to. Then n8n automatically applies the right Gmail label — so your inbox stays organized without you lifting a finger.

**Flow Diagram:**
```
Gmail Trigger (every hour)
    → Google Gemini AI (categorizes the email)
        → Switch (routes based on category)
            → Apply Gmail Label (Promotions / Socials / Personal / Sales / Misc)
```

**AI Categories:**

| Category | Description | Examples |
|---|---|---|
| **Promotions** | Marketing emails, offers, discounts | "50% Off Our New Course" |
| **Socials** | Social media notifications | "YouTube: New comment on your video" |
| **Personal** | Time-sensitive or personal messages | "Can we talk today?" |
| **Sales** | Client deals, proposals, contracts | "Signed Contract Attached" |
| **Misc** | Everything else | — |

**Skills demonstrated:** Gmail API, LLM/AI integration (Google Gemini), conditional routing, label management, event-driven scheduling.

---

### 2. Form Lead → Sheets + Email Responder

**File:** [`Form Lead to Sheets + Email Responder.json`](./Form%20Lead%20to%20Sheets%20%2B%20Email%20Responder.json)

**What it does in plain English:**
A "Contact Us / Get a Quote" web form is hosted by n8n. When someone fills it in (Name, Email, Service, Budget), the workflow instantly saves their details to a Google Sheet and sends them a confirmation email — all automatically.

**Flow Diagram:**
```
Form Submission (web form)
    → Google Sheets (append new row to lead tracker)
    → Gmail (send confirmation email to the lead)
```

**Form fields collected:**

| Field | Type | Required |
|---|---|---|
| Full Name | Text | ✅ |
| Email | Email | ✅ |
| Service | Dropdown (SEO / ADS) | ✅ |
| Budget | Number | ✅ |

**Skills demonstrated:** Webhook/form triggers, Google Sheets integration, Gmail sending, lead management automation.

---

### 3. Gmail Attachment Extractor

**File:** [`gmail with attach read_write.json`](./gmail%20with%20attach%20read_write.json)

**What it does in plain English:**
Run this manually whenever you want to pull email attachments out of Gmail and save them as files on your computer or server. Useful for archiving invoices, reports, or any file-based emails.

**Flow Diagram:**
```
Manual Trigger
    → Fetch Gmail Messages
        → Edit/Map Fields
            → Convert Attachment to File
                → Save File to Disk
```

| Node | What it does |
|---|---|
| `Manual Trigger` | You click "Run" to start it |
| `Get many messages` | Pulls emails from Gmail inbox |
| `Edit Fields` | Reshapes and maps the data |
| `Convert to File` | Turns attachment data into a usable binary file |
| `Read/Write Files from Disk` | Saves the file locally |

**Skills demonstrated:** Gmail API, binary file handling, data transformation, local file I/O.

---

### 4. Gmail Body Processor

**File:** [`gmail with body read_write.json`](./gmail%20with%20body%20read_write.json)

**What it does in plain English:**
A more advanced Gmail workflow that runs on a schedule. It reads email bodies, extracts structured data (e.g., from attached spreadsheets or CSVs), saves it locally, and then keeps your inbox tidy by labelling emails, marking them as read, and even sending replies.

**Flow Diagram:**
```
Schedule Trigger ─┐
Manual Trigger  ──┴→ Fetch Gmail Messages
                         → Extract Data from File
                         → Save to Disk
                         → Apply Label
                         → Mark as Read
                         → Send Reply Email
```

| Node | What it does |
|---|---|
| `Schedule Trigger` | Runs automatically (e.g., every hour) |
| `Manual Trigger` | Run on demand for testing |
| `Get many messages` | Fetches emails with filters |
| `Extract from File` | Parses data from attachments |
| `Read/Write Files from Disk` | Stores extracted content |
| `Add label to message` | Organises Gmail with labels |
| `Mark a message as read` | Cleans up processed emails |
| `Send a message` | Sends a reply or follow-up |

**Skills demonstrated:** Scheduled automation, Gmail full lifecycle management, file parsing, inbox-zero patterns.

---

### 5. n8n → UiPath RPA Trigger

**File:** [`n8n Trigger UIipath.json`](./n8n%20Trigger%20UIipath.json)

**What it does in plain English:**
This workflow acts as a bridge between n8n and **UiPath** — a leading Robotic Process Automation (RPA) platform. When triggered, n8n runs a system shell command that launches a UiPath robot. This lets you kick off desktop automation (e.g., filling a form, processing files in legacy software) from within a broader n8n pipeline.

**Flow Diagram:**
```
Manual Trigger
    → Execute Command (shell → launches UiPath Robot)
```

| Node | What it does |
|---|---|
| `Manual Trigger` | Starts the workflow on demand |
| `Execute Command` | Runs a shell command to invoke UiPath |

**Skills demonstrated:** RPA integration, n8n ↔ UiPath bridge, shell command execution, hybrid automation (low-code + RPA).

---

### 6. Qualified Lead Processor & Email Digest

**File:** [`Qualified Lead Processor to Google Sheets & Email.json`](./Qualified%20Lead%20Processor%20to%20Google%20Sheets%20%26%20Email.json)

**What it does in plain English:**
Takes a list of incoming leads and filters out low-budget inquiries, qualifying only leads with a budget of $1,000 or more. Qualified leads are saved to Google Sheets. The workflow then aggregates the leads, sorts them from highest to lowest budget with custom JavaScript, builds a clean text summary, and emails a prioritized leads digest to your sales inbox.

**Flow Diagram:**
```
Manual / Trigger
    → Split Out (individual lead records)
        → Filter (Budget >= $1,000)
            → Google Sheets (append qualified lead row)
                → Aggregate (combine items)
                    → JavaScript Code (sort descending by budget)
                        → JavaScript Code (format digest text)
                            → Gmail (send leads digest email)
```

| Node | What it does |
|---|---|
| `Manual Trigger` | Runs on demand or scheduled |
| `Split Out` | Unpacks lead arrays into separate items |
| `Filter` | Keeps only leads with budget $\ge$ 1,000 |
| `Google Sheets` | Appends qualified leads to tracking sheet |
| `Aggregate` | Collects processed items into a single array |
| `JavaScript Code (Sort)` | Sorts qualified leads by budget descending |
| `JavaScript Code (Format)` | Generates clean summary message |
| `Gmail (Send Message)` | Sends daily leads report to the sales team |

**Skills demonstrated:** Lead qualification logic, array splitting & re-aggregation, custom JavaScript data sorting & formatting, Google Sheets API, automated Gmail reporting.

---

### 7. Google Maps Lead Scraper & Sheet Sync

**File:** [`Google Maps Cleaner Lead Scraper & Sheet Sync.json`](./Google%20Maps%20Cleaner%20Lead%20Scraper%20%26%20Sheet%20Sync.json)

**What it does in plain English:**
Runs on a schedule to gather high-quality local business leads. It triggers an Apify Google Maps scraper actor (targeting niches such as local cleaners in Toronto), filters for high-reputation businesses (rating $\ge$ 4.8), and iterates through them in batches. For each business, it upserts (appends or updates based on website URL) details into Google Sheets, with built-in pacing pauses to prevent API rate limits.

**Flow Diagram:**
```
Schedule Trigger (interval)
    → Apify Actor (Google Maps Scraper)
        → Filter (totalScore >= 4.8)
            → Loop Over Items (batch iterator)
                → Google Sheets (upsert by Website)
                    → Wait (rate-limiting delay) ──┐
                         └── (loop back to next item)
```

| Node | What it does |
|---|---|
| `Schedule Trigger` | Runs on a preset interval automatically |
| `Apify Actor` | Executes Google Places crawler actor & returns dataset |
| `Filter` | Filters out low-rated businesses (Score $\ge$ 4.8) |
| `Loop Over Items` | Splits items into manageable batches |
| `Google Sheets` | Appends or updates row matched by website domain |
| `Wait` | Introduces paced delay between requests |

**Skills demonstrated:** Apify Actor cloud integration, web scraping & lead extraction, data cleansing & quality thresholds, batch looping with delay pacing, Google Sheets upsert logic.

---

### 8. Telegram Invoice Data Extractor

**File:** [`Invoice Data Extractor_ Telegram to Sheets.json`](./Invoice%20Data%20Extractor_%20Telegram%20to%20Sheets.json)

**What it does in plain English:**
Turns Telegram into an AI receipt & invoice intake system. When an invoice document (PDF) or image (PNG) is sent to your Telegram bot, n8n downloads the file and uses Google Gemini AI to read, parse, and structure the data (merchant name, line items, prices, taxes, total, and currency). It standardizes the values, logs every itemized row to Google Sheets, and sends a confirmation reply right back to the user on Telegram.

**Flow Diagram:**
```
Telegram Trigger (document/image received)
    → Telegram (download file)
        → Switch (by MIME type)
             ├── PDF  → Extract Text → Gemini 3.5 Flash Lite ──┐
             └── PNG  → Gemini Vision (OCR) → Gemini 3.5 ──────┤
                                                               ↓
                                                      JavaScript Normalization
                                                               ↓
                                                      Split Out Line Items
                                                               ↓
                                                      Google Sheets (append rows)
                                                               ↓
                                                      Aggregate & Telegram Reply
```

| Node | What it does |
|---|---|
| `Telegram Trigger` | Fires when user sends a file/message |
| `Telegram (Get a file)` | Downloads binary document or image |
| `Switch` | Routes between PDF documents and PNG images |
| `Extract from File` | Pulls raw text from PDF files |
| `Gemini (Analyze image)` | Vision OCR extraction for receipts/images |
| `Gemini (Message model)` | Extracts strict structured JSON schema |
| `JavaScript Code` | Parses JSON, normalizes currencies & cleans numeric values |
| `Split Out` | Expands line items into individual row entries |
| `Google Sheets` | Appends line items to "Line Items" tab |
| `Telegram (Send message)` | Notifies user that processing completed |

**Skills demonstrated:** Telegram Bot API, Multimodal AI & OCR (Google Gemini Flash Lite), PDF text extraction, structured JSON parsing, currency/number normalization in JavaScript, Google Sheets data logging, bidirectional bot chat.

---

## 🚀 Getting Started

### Prerequisites

| Requirement | Details | Used In |
|---|---|---|
| **n8n** | v1.0+ — [Install guide](https://docs.n8n.io/hosting/) or use [n8n.cloud](https://n8n.cloud) | All workflows |
| **Gmail Account** | OAuth2 credentials configured in n8n | Workflows 1, 2, 3, 4, 6 |
| **Google Sheets** | OAuth2 or service account credentials | Workflows 2, 6, 7, 8 |
| **Google Gemini API Key** | Free tier available at [Google AI Studio](https://aistudio.google.com) | Workflows 1, 8 |
| **Telegram Bot Token** | Created via [@BotFather](https://t.me/botfather) | Workflow 8 |
| **Apify API Token** | Account at [Apify](https://apify.com) | Workflow 7 |
| **UiPath Robot** | Installed locally | Workflow 5 |

---

### How to Import a Workflow

> **New to n8n?** Follow these steps — it takes about 2 minutes.

1. **Open n8n** in your browser (e.g., `http://localhost:5678`).
2. Click **"Add Workflow"** in the top-right corner.
3. Click the **menu (⋮)** → **"Import from File"**.
4. Select any `.json` file from this repository.
5. Open the **Credentials** panel and connect your accounts (Gmail, Sheets, Telegram, Apify, etc.).
6. Click **"Execute Workflow"** to test it.

**Or use the n8n CLI:**
```bash
n8n import:workflow --input="Email inbox.json"
```

> ⚠️ **Important:** The `.json` files do not contain any passwords or API keys. You must add your own credentials inside n8n after importing.

---

## 🗂️ Project Structure

```
n8n-Workflows/
├── Email inbox.json                                      # AI-powered email categorization
├── Form Lead to Sheets + Email Responder.json             # Lead capture form → Sheets + Email
├── gmail with attach read_write.json                     # Gmail attachment extraction
├── gmail with body read_write.json                       # Gmail body processing + triage
├── Google Maps Cleaner Lead Scraper & Sheet Sync.json     # Apify scraper → Sheets sync
├── Invoice Data Extractor_ Telegram to Sheets.json       # Telegram bot + Gemini invoice OCR → Sheets
├── n8n Trigger UIipath.json                              # UiPath RPA trigger
├── Qualified Lead Processor to Google Sheets & Email.json# High-ticket lead filter & digest email
└── README.md
```

---

## 💡 Real-World Use Cases

| Scenario | Workflow to Use |
|---|---|
| Auto-sort your inbox with AI, zero effort | AI Email Inbox Sorter |
| Capture website leads into a spreadsheet automatically | Form Lead → Sheets |
| Auto-reply to leads when they submit a contact form | Form Lead → Sheets |
| Archive email attachments (invoices, reports) to disk | Gmail Attachment Extractor |
| Parse and store data from email attachments on a schedule | Gmail Body Processor |
| Trigger a UiPath bot as part of a larger automation | n8n → UiPath Trigger |
| Filter high-value leads and receive a sorted email digest | Qualified Lead Processor |
| Scrape vetted local business leads from Google Maps automatically | Google Maps Lead Scraper |
| Process PDF and image receipts sent via Telegram into Google Sheets | Telegram Invoice Data Extractor |

---

## 🤝 Contributing

Contributions are welcome! To add a new workflow:

1. Build and test your workflow in n8n.
2. Export it: **☰ Menu → Download** — this saves a `.json` file.
3. **Remove any hardcoded credentials** from the JSON before committing.
4. Add a new section to this README following the same format.
5. Open a pull request with a short description of what the workflow does.

---

## 📄 License

This project is open-source under the [MIT License](LICENSE).

---

*Built with [n8n](https://n8n.io) — the fair-code workflow automation platform.*
