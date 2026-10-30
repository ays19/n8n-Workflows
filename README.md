# 🔄 n8n Workflow Collection

A curated collection of production-ready **n8n automation workflows** covering Gmail processing and RPA (Robotic Process Automation) integration with UiPath.

> **n8n** is an open-source, self-hostable workflow automation tool that connects apps, APIs, and services with a visual node-based editor.

---

## 📋 Table of Contents

- [Workflows](#-workflows)
  - [Gmail with Attachment — Read & Write](#1-gmail-with-attachment--read--write)
  - [Gmail with Body — Read & Write](#2-gmail-with-body--read--write)
  - [n8n Trigger for UiPath](#3-n8n-trigger-for-uipath)
- [Getting Started](#-getting-started)
- [Prerequisites](#-prerequisites)
- [How to Import a Workflow](#-how-to-import-a-workflow)
- [Project Structure](#-project-structure)
- [Use Cases](#-use-cases)
- [Contributing](#-contributing)

---

## 📦 Workflows

### 1. Gmail with Attachment — Read & Write

**File:** [`gmail with attach read_write.json`](./gmail%20with%20attach%20read_write.json)

Fetches emails from Gmail, extracts attachment data, transforms it, and saves the files to disk.

**Flow:**

```
Manual Trigger → Get Gmail Messages → Edit Fields → Convert to File → Read/Write Files from Disk
```

| Node | Purpose |
|---|---|
| `Manual Trigger` | Starts the workflow on demand |
| `Get many messages` | Fetches emails from Gmail inbox |
| `Edit Fields` | Maps and transforms email metadata |
| `Convert to File` | Converts attachment data to a binary file |
| `Read/Write Files from Disk` | Saves the file locally |

**Good for:** Archiving email attachments, feeding files into downstream automation pipelines.

---

### 2. Gmail with Body — Read & Write

**File:** [`gmail with body read_write.json`](./gmail%20with%20body%20read_write.json)

A more comprehensive Gmail automation that reads email bodies, extracts structured data from files, applies labels, marks emails as read, and can send reply emails — all on a schedule.

**Flow:**

```
Schedule Trigger ─┐
Manual Trigger  ──┤→ Get Gmail Messages → Extract from File → Read/Write Files
                  │→ Add Label → Mark as Read → Send Message
```

| Node | Purpose |
|---|---|
| `Schedule Trigger` | Runs automatically on a defined interval |
| `Manual Trigger` | Runs on demand for testing |
| `Get many messages` (×3) | Fetches emails with various filters |
| `Extract from File` | Parses structured data from file content |
| `Read/Write Files from Disk` | Persists extracted content locally |
| `Add label to message` (×2) | Organizes emails with Gmail labels |
| `Mark a message as read` | Marks processed emails as read |
| `Send a message` | Sends reply or follow-up emails |

**Good for:** Email triage automation, data extraction pipelines, inbox-zero workflows.

---

### 3. n8n Trigger for UiPath

**File:** [`n8n Trigger UIipath.json`](./n8n%20Trigger%20UIipath.json)

Bridges n8n with **UiPath RPA** by triggering a UiPath robot or process via a shell command — combining low-code automation with enterprise RPA.

**Flow:**

```
Manual Trigger → Execute Command (UiPath)
```

| Node | Purpose |
|---|---|
| `Manual Trigger` | Starts the workflow on demand |
| `Execute Command` | Runs a system command to invoke UiPath |

**Good for:** Triggering UiPath attended/unattended robots from n8n, orchestrating RPA as part of a larger automation chain.

---

## 🚀 Getting Started

### Prerequisites

| Requirement | Details |
|---|---|
| **n8n** | v1.0+ (self-hosted or [n8n.cloud](https://n8n.cloud)) |
| **Gmail Account** | With OAuth2 credentials configured in n8n |
| **UiPath** | UiPath Robot installed (for the UiPath workflow only) |

### How to Import a Workflow

1. Open your n8n instance in the browser.
2. Click **"Add Workflow"** → **"Import from File"**.
3. Select any `.json` file from this repository.
4. Configure credentials (Gmail OAuth2, etc.) in the credential manager.
5. Click **"Execute Workflow"** to test.

> **Tip:** You can also import directly via the n8n CLI:
> ```bash
> n8n import:workflow --input="gmail with attach read_write.json"
> ```

---

## 🗂️ Project Structure

```
n8n-Workflows/
├── gmail with attach read_write.json   # Gmail attachment extraction workflow
├── gmail with body read_write.json     # Gmail body processing workflow
├── n8n Trigger UIipath.json            # UiPath RPA trigger workflow
└── README.md
```

---

## 💡 Use Cases

| Scenario | Workflow |
|---|---|
| Auto-save email attachments to disk | Gmail with Attachment |
| Parse invoice/report emails on a schedule | Gmail with Body |
| Label and archive processed emails | Gmail with Body |
| Trigger a UiPath bot from a web event | n8n Trigger for UiPath |
| Chain RPA + API automation in one pipeline | n8n Trigger for UiPath |

---

## 🤝 Contributing

Contributions are welcome! To add a new workflow:

1. Export your workflow from n8n as JSON (**⋮ Menu → Download**).
2. Remove any sensitive credentials from the file before committing.
3. Add a new section in this README describing the workflow's nodes and purpose.
4. Open a pull request.

---

## 📄 License

This project is open-source and available under the [MIT License](LICENSE).

---

*Built with [n8n](https://n8n.io) — the fair-code workflow automation platform.*
