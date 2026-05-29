# CORNERSTONE PDK Monitor — User Manual

**Version 1.0.0 · 29 May 2026**
Designed by Emre Kaplan · University of Southampton · [pdk.cornerstone.soton.ac.uk](https://pdk.cornerstone.soton.ac.uk)
Licence: TAPR Open Hardware Licence v1.0

---

## Overview

The CORNERSTONE PDK Monitor is a free, single-file, browser-based dashboard for managing and sharing integrated photonics Process Design Kits (PDKs). It acts as a gateway between designers and fabrication foundries — giving the community a single place to discover, use, and contribute to open PDKs. No server, no installation: open `pdk-monitor.html` in any modern browser.

---

## Table of Contents

1. [First-Time Use — Quick Start](#1-first-time-use--quick-start)
2. [Header Controls](#2-header-controls)
3. [Platform Tabs](#3-platform-tabs)
4. [Building Block (BB) Cards](#4-building-block-bb-cards)
5. [BB Detail View](#5-bb-detail-view)
6. [Data & File Management](#6-data--file-management)
7. [Multi-User Pool & GitHub](#7-multi-user-pool--github)
8. [AI Insight Agent](#8-ai-insight-agent)
    - [8.1 Configuration](#81-configuration)
    - [8.2 BB Summariser](#82-bb-summariser)
    - [8.3 Multi-Turn Chat](#83-multi-turn-chat)
    - [8.4 GitHub File Fetcher](#84-github-file-fetcher)
    - [8.5 Analyse Files with AI](#85-analyse-files-with-ai)
    - [8.6 Detachable Chat Window](#86-detachable-chat-window)
9. [Roles & Access Control](#9-roles--access-control)
10. [Import / Export & Backup](#10-import--export--backup)
11. [Keyboard Shortcuts](#11-keyboard-shortcuts)
12. [Troubleshooting](#12-troubleshooting)

---

## 1. First-Time Use — Quick Start

**Step 1 — Open the dashboard**
Double-click `pdk-monitor.html` to open it in Chrome, Firefox, or Safari. No installation required.

**Step 2 — Accept the licence terms**
On first launch a terms gate appears. Read and accept the TAPR Open Hardware Licence v1.0 to proceed.

**Step 3 — Explore the default PDK**
The dashboard loads pre-populated with the CORNERSTONE building blocks across SOI, TFLN, and SiN platforms. Browse the platform tabs to see available components.

**Step 4 — Switch to Admin mode (to edit)**
By default the dashboard opens in Read-only mode. To add or edit BBs, open the HTML file in a text editor and follow the comment at the top of the `<script>` block to enable Admin mode.

**Step 5 — Connect to GitHub (optional)**
Click **🐙** in the header to add your GitHub Personal Access Token. This enables fetching team data, publishing changes, and the AI file fetcher.

**Step 6 — Configure the AI agent (optional)**
Click **🧠 Insight → ⚙ Settings**, enter your Anthropic or OpenAI API key, and click **🔌 Test connection**. The AI agent can then summarise BBs, answer questions about your PDK, and analyse measurement data files.

**Step 7 — Import your own data**
Use **Import** in the header to load a `dashboard.json` exported from another instance, replacing the default data with your own.

---

## 2. Header Controls

| Control | Function |
|---------|----------|
| ↻ (refresh) | Hard reload — reloads the page from disk, bypassing the browser cache |
| Search box | Full-text search across BBs, platforms, contributors, descriptions |
| Status / Category dropdowns | Filter the BB card list |
| Useful Links | Configurable chip links (edit in About modal) |
| 🐙 GitHub | Configure GitHub PAT and user identity |
| 🧠 Insight | Open the AI Agent |
| ⓘ About | Version info, notes, editable links and logo |
| Import | Import a `dashboard.json` or ZIP backup |
| Backup (.json) | Export the full dashboard as a JSON backup |
| ↻ Sync all | Replay all previously recorded GitHub fetch URLs across every BB |

**Storage indicator** (in header): shows localStorage usage. Turns amber at 70%, red at 90%.

---

## 3. Platform Tabs

Each tab represents one fabrication platform (e.g. SOI 220 nm). The tab shows the platform name, material (Si / SiN / LN), BB count, and a completion progress bar.

**Adding a platform:** Click **+ Add tab** (Admin only).
**Reordering:** Drag tabs left or right.
**Platform settings:** Click the ✏ icon on any tab header to edit name, material, waveguide thickness, GitHub folder URL, and default waveguide width.

---

## 4. Building Block (BB) Cards

Each BB card shows:
- **Name** and **type icon** (waveguide, MMI, ring resonator, grating coupler, etc.)
- **Status pill**: Available · In progress · Planned · Discontinued
- **Material badge**: Si · SiN · LN
- **EDA tools** supported (IPKISS, gdsfactory, KLayout, etc.)
- **File count** — attached GDS, S-matrix, scripts, images, documents
- **Contributor** chip
- **CDI axes** — Chip / Wafer / Assembly maturity levels (1–4)

**Selecting BBs for bulk move:** A small faded checkbox sits to the left of each card. Click it to select; use **🔀 Move selected** to move multiple BBs to another platform at once.

Click any card to open the **BB Detail panel** on the right.

---

## 5. BB Detail View

The detail panel has five tabs:

**Details** — Edit name, description, type, category, status, contributor, CDI maturity, EDA tools, and planned release date.

**Files** — Upload or link GDS/OASIS layout files, S-matrix data (`.s2p`, `.s4p`, CSV), images, Python scripts, and documents (PDF, Markdown, YAML).

**Tests** — Log characterisation results with date, contributor, and pass/fail. Attach raw data files to each test entry.

**AI** — AI-generated summary of this BB (requires API key — see §8.2). Shows an executive sentence and a deep-dive analysis citing only measured values. Cached until you click **🔁 Refresh**.

**Parameters** — Structured parameter table (width, gap, coupling length, etc.) editable per sub-entity or platform variant.

The **✦ Ask AI** button pre-fills the chat with this BB's context for focused questions.

---

## 6. Data & File Management

**Uploading files:** Drag onto a BB card or use the upload button in the Files tab. Supported: GDS, OASIS, s2p, s4p, CSV, TSV, PNG, JPEG, PDF, PY, KPY, LYML, YAML, Markdown, and more.

**GDS viewer:** GDS files render inline with layer colours from the GDS layer map. Hover a shape for layer name and description. Edit the layer map under **Manage Lists → GDS Layers**.

**S-matrix viewer:** Upload a Touchstone (`.s2p`, `.s4p`, `.sNp`) or CSV file. The viewer plots |S| vs wavelength/frequency for each port pair.

**Python script runner:** Scripts tagged as Python run in-browser via a Pyodide sandbox. Use **💻 Run locally** to download a self-contained folder for scripts that need native binaries or large datasets.

---

## 7. Multi-User Pool & GitHub

### Setting up GitHub

1. Click **🐙** in the header.
2. Paste a GitHub PAT with `repo` scope.
3. Optionally enter your GitHub username / email.
4. Tick **Remember token** to persist on this device.

### Fetching from the pool (section 2 of the fetch panel)

| Button | What it does |
|--------|-------------|
| 📥 Fetch all enabled sources | Fetches the configured main repo and (optionally) all user slices |
| 🔍 Discover user slices | Scans the `users/` folder for contributor dashboards |
| 🪪 Fetch my slice | Fetches only your own `users/<id>/` slice |
| 📚 Fetch user repos | One-click full pool assembly including all real files |
| Merge into my dashboard | Additively combines fetched data with your local copy |
| 🔁 Replace whole dashboard | Overwrites your local copy with the fetched data |
| ☑ backup | Tick to auto-download a backup JSON before Replace |

**Tip:** Untick **backup** if you run many fetch cycles in a session and don't want a new file each time.

### Publishing (section 3)

Use **📤 Publish** (Admin) to commit `dashboard.json` and all file changes to the configured GitHub repository.

---

## 8. AI Insight Agent

The AI Insight agent works with Claude, OpenAI, or any OpenAI-compatible endpoint (Ollama, LM Studio, etc.).

### 8.1 Configuration

Click **🧠 Insight → ⚙ Settings**.

| Field | Notes |
|-------|-------|
| API endpoint | Default: `https://api.anthropic.com/v1/messages`. OpenAI: `https://api.openai.com/v1/chat/completions`. Ollama: `http://localhost:11434/v1/chat/completions` |
| Model | `claude-sonnet-4-5`, `gpt-4o`, `llama3`, etc. |
| API key | Stored only in this browser's localStorage |
| Monthly token cap | Hard limit on tokens per calendar month |
| Egress mode | metrics-only (safest) · metrics + extracts · full |
| Prompt style | Brief · Balanced · Deep |

Click **🔌 Test connection** before saving.

### 8.2 BB Summariser

Open any BB → **🧠 AI tab** → **🧠 Summarise this BB**. The agent extracts metrics from your uploaded files in-browser, sends only the computed JSON to the LLM, and caches the result. Click **🔁 Refresh** to regenerate.

Trigger an **📋 Executive summary** for an entire platform or the whole dashboard from the main Insight modal.

### 8.3 Multi-Turn Chat

Click **🧠 Insight → 💬 Chat with the agent**. The agent calls tools automatically to answer your questions:

| Tool | What it reads |
|------|--------------|
| `list_platforms` | All platforms and BB counts |
| `list_bbs` | BBs filtered by platform, category, status, contributor |
| `get_bb` | Full detail for a specific BB |
| `list_contributors` | All contributors and their BB counts |
| `get_bb_metrics` | Computed metrics for a BB |
| `search_bbs` | Full-text search |
| `summarise_bb` | Generates and caches an AI summary |

**Example queries:** "List every grating coupler on SOI 220 nm", "Which BBs have no S-matrix data?", "Summarise the MMI 1×2 on SOI 340 nm".

Use **🗑 Clear conversation** to start fresh or **⬇ Export .md** to download the transcript.

### 8.4 GitHub File Fetcher

Inside the chat, click **▶ Data files in context** to expand the file panel.

| URL format | Example |
|------------|---------|
| Single file (blob) | `github.com/org/repo/blob/main/data.csv` |
| Single file (raw) | `raw.githubusercontent.com/org/repo/main/data.csv` |
| Folder | `github.com/org/repo/tree/main/measurements/` |
| Multiple (newline or comma) | Paste several URLs at once |

Click **Fetch** or press **Ctrl+Enter** / **Cmd+Enter**. Files appear as rows — click ✅/⬜ to toggle in/out of context, or ✕ to remove. The file list scrolls when many files are loaded.

**Supported types fetched from folders:** `.csv`, `.tsv`, `.json`, `.yaml`, `.yml`, `.py`, `.md`, `.log`, `.txt`, `.toml`, `.s2p`, `.s4p`, and many more.

**Tip:** If the repo is private or you hit the 60 req/hr unauthenticated limit, add your GitHub PAT in 🐙 settings — the fetcher uses it automatically (5000 req/hr).

### 8.5 Analyse Files with AI

With files loaded (✅), click **🔬 Analyse files with AI**.

The browser first runs a client-side pre-analysis (no tokens used):
- **CSV/TSV** — delimiter detection, column type inference, count/null%/min/max/mean/median/std, IQR outlier detection, Pearson correlations
- **JSON** — recursive schema map (4 levels deep), null field detection
- **YAML** — top-level key extraction, null/empty field list

The pre-computed report is then sent to the LLM, which produces a markdown table of column stats, anomaly flags, and 2–4 insight bullets. Follow up in chat: "What's causing the outliers in the loss column?"

### 8.6 Detachable Chat Window

Click **⧉ Detach** in the chat footer to lift the chat into a free-floating window:
- **Drag** the title bar to reposition anywhere on screen
- **Resize** from the bottom-right corner handle
- **Minimise** to a small pill at the bottom-right
- **⊞ Dock** to return to the modal

Position and size are remembered in localStorage across sessions.

---

## 9. Roles & Access Control

| Role | Permissions |
|------|------------|
| **Admin** | Full access: add/remove/edit BBs, platforms, users, publish, manage lists |
| **User-admin** | Edit BBs in `users/<id>/`; read-only elsewhere |
| **Read-only** | View all data, run scripts, download files; cannot edit or publish |

The default role is Read-only. To switch to Admin, see the comment at the top of the `<script>` block in the HTML file.

---

## 10. Import / Export & Backup

**Backup (.json)** — Downloads the full dashboard as `dashboard.json`. Use to back up data, commit to GitHub, or share.

**Import** — Loads a `dashboard.json` or ZIP backup, replacing the current model. A local backup is saved automatically first.

**Export BB folder (zip)** — From any BB → Files tab → **⬇ Download folder**: downloads a ZIP with all BB files and an auto-generated `README.md`.

**Run locally (zip)** — For Python scripts: downloads a self-contained folder with the script, data files, and `run.sh`, ready to run from a terminal.

---

## 11. Keyboard Shortcuts

| Shortcut | Action |
|----------|--------|
| `/` | Focus search box |
| `Escape` | Close modal / detail panel |
| `Ctrl+Enter` / `Cmd+Enter` | Send AI chat message; fetch files in URL input |
| `Ctrl+S` / `Cmd+S` | (Script editor) Save script |

---

## 12. Troubleshooting

**Dashboard shows nothing / nothing is clickable**
→ Hard reload: `Cmd+Shift+R` (Mac) or `Ctrl+Shift+R` (Windows). If that fails, close the tab and re-open the HTML file directly.

**AI agent returns "No API key"**
→ Open 🧠 Insight → ⚙ Settings. Enter your API key and click 🔌 Test connection to verify.

**API error 429 (rate limit)**
→ Your organisation has hit the per-minute token cap. Collapse the file panel (click ▶) before chatting, or switch to a faster model (Haiku). Raise your limit at console.anthropic.com.

**GitHub fetch returns 403**
→ You've hit the 60 req/hr unauthenticated limit. Add your GitHub PAT in 🐙 settings.

**GitHub fetch returns 404**
→ Check the branch name. GitHub defaults to `main`; some repos still use `master`.

**File fetcher loads fewer files than expected**
→ Files > 2 MB are skipped. Binary files (GDS, images, compiled) are excluded by design.

**Storage indicator is red**
→ Near the localStorage limit (~5–10 MB). Export JSON, then use **🧹 Clear AI cache** in AI Settings and delete old backups.

**Pyodide script runner times out**
→ Pyodide loads from CDN on first use (~10 MB). Check your network connection.

**Dashboard shows stale data after a pool fetch**
→ Click ↻ in the header to hard reload.

---

*For questions, contributions, and issues, see the GitHub repositories linked in the dashboard header.*
*CORNERSTONE PDK Monitor is free and open under the TAPR Open Hardware Licence v1.0.*
*Designed by Emre Kaplan · University of Southampton · CORNERSTONE PDK Team*
