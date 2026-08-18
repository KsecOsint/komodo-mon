```markdown
# 🦎 Komodo-mon

**Automated Alternate Alias Discovery and Monitoring**

Komodo-mon is a CLI-driven Open Source Intelligence (OSINT) framework designed for continuous, automated username monitoring and discovery. By combining local Large Language Models via **Ollama**, target enumeration through **Sherlock**, local **SQLite** state tracking, and **Discord Webhook** integrations, Komodo-mon provides passive target surveillance with real-time change detection.

---

## 🔑 Key Features

* **AI-Driven Handle Generation:** Leverages local LLMs (`llama3` via Ollama) to generate target handle permutations, leetspeak variations, prefix/suffix mutations, and email cross-mixes.
* **Automated Social Enumeration:** Interfaces with `sherlock` to scan across hundreds of online platforms for target presence.
* **State Management & Change Tracking:** Uses SQLite databases per profile to track username status transitions (`UNCHECKED` → `FOUND`, `FOUND` → `DISAPPEARED`).
* **Discord Alerting System:** Automatically pushes alerts via Discord webhooks when target handles are newly discovered or removed.
* **Tor & Proxy Anonymization:** Includes native Tor SOCKS proxy support (`--tor`, `--unique-tor`) with built-in socket health checks before scanning.
* **Flexible Modes:** Supports an interactive Rich CLI menu, single-pass executions (`--scan-once` for cron jobs), or continuous background monitoring (`--daemon`).

---

## 🛠️ Architecture Overview

```text
┌─────────────────┐       ┌──────────────────┐       ┌──────────────────┐
│ Target Details  │ ───►  │ Ollama (llama3)  │ ───►  │ Target Handles   │
│ (Name, Emails)  │       │ Handle Generator │       │ Database (SQLite)│
└─────────────────┘       └──────────────────┘       └──────────────────┘
                                                              │
                                                              ▼
┌─────────────────┐       ┌──────────────────┐       ┌──────────────────┐
│  Discord Alert  │ ◄───  │ Delta Engine     │ ◄───  │ Sherlock Engine  │
│    Webhook      │       │ (Status Changes) │       │ (Tor/Proxy Opt.) │
└─────────────────┘       └──────────────────┘       └──────────────────┘
```

---

## 📋 Prerequisites

Before installing, ensure the following services are installed and available on your environment:

1. **Python 3.10+**
2. **Ollama**: Required for AI handle generation. Install from [ollama.ai](https://ollama.ai/).
   * Pull the default model:
     ```bash
     ollama pull llama3
     ```
3. **Tor Service** *(Optional, required only for `--tor` flag usage)*:
   * **Linux/Debian:** `sudo apt install tor && sudo systemctl start tor`
   * **macOS:** `brew install tor && brew services start tor`

---

## 🚀 Installation & Setup

### 1. Clone the Repository

```bash
git clone https://github.com/your-username/komodo-mon.git
cd komodo-mon
```

### 2. Set Up a Virtual Environment

```bash
python3 -m venv venv
source venv/bin/activate  # On Windows: venv\Scripts\activate
```

### 3. Install Dependencies

```bash
pip install -r requirements.txt
```

Ensure Sherlock is accessible in your environment PATH or virtual environment:

```bash
pip install sherlock-project
```

---

## 💻 Usage & CLI Guide

Komodo-mon can be operated interactively via its console interface or non-interactively via CLI flags.

### 1. Interactive Console Mode

To launch the main TUI menu:

```bash
python3 komodo.py
```

From the interactive menu, you can:
* **Add / Edit Target Profiles:** Define target names, email addresses, custom attributes (e.g., gamer tags), and known handles.
* **Generate Handles via Ollama:** Prompt local AI models to extrapolate potential unknown handles from target metadata.
* **Run Scan Cycles:** Execute enumeration passes manually (with optional Tor routing prompt).
* **Application Settings:** Set your global Discord Webhook URL for real-time notifications.
* **View/Delete Profiles:** Manage target profiles stored inside `./data/`.

---

### 2. Non-Interactive CLI Modes

#### **Single Pass Mode (`--scan-once`)**
Executes a single scanning cycle across all stored target profiles and exits immediately. Ideal for scheduled `cron` jobs.

```bash
python3 komodo.py --scan-once
```

#### **Daemon Mode (`--daemon`)**
Runs continuously in the background, executing scan cycles at defined intervals.

```bash
python3 komodo.py --daemon --interval 3600
```

#### **Tor Anonymized Scans (`--tor`)**
Routes all outgoing Sherlock enumerations through the local Tor SOCKS proxy (`127.0.0.1:9050`) and requests a new IP circuit per query to prevent rate limiting.

```bash
python3 komodo.py --scan-once --tor
```

#### **Custom Proxy Support (`--proxy`)**
Routes scans through a custom HTTP or SOCKS proxy:

```bash
python3 komodo.py --scan-once --proxy "socks5://127.0.0.1:1080"
```

---

## ⚙️ Configuration File Structure

The system automatically manages profile metadata and execution states within the `./data` folder structure:

```text
komodo-mon/
├── data/
│   └── [target_person_id]/
│       ├── config.json       # Target metadata, emails, and custom attributes
│       ├── database.db       # SQLite DB storing handles, statuses, & timestamps
│       └── [username].txt    # Raw output logs from Sherlock scans
├── global_config.json        # Global application settings (Discord Webhook URL)
├── komodo.py                  # Main CLI entry point & TUI interface
├── scanner.py                # Ollama handle generator & Sherlock execution engine
├── database.py               # SQLite database interface layer
├── notifier.py               # Discord webhook dispatch module
└── requirements.txt          # Python package requirements
```

---

## 🔔 Discord Webhook Integration

To receive alerts when target handle states change:

1. Open Discord and go to **Server Settings** → **Integrations** → **Webhooks**.
2. Click **New Webhook**, select a target channel, and copy the **Webhook URL**.
3. Launch Komodo-mon (`python3 komodo.py`), select **Option 4 (Application Settings)**, and paste your URL.

Alerts trigger automatically for events such as `NEW DISCOVERY` or `DISAPPEARED`.

---

## 📄 License

This project is licensed under the MIT License. See `LICENSE` for details.
```
