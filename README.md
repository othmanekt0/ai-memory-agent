# 🧠 AI Memory Agent

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![Python 3.10+](https://img.shields.io/badge/Python-3.10+-blue.svg)](https://www.python.org/downloads/)
[![FastAPI](https://img.shields.io/badge/FastAPI-0.100+-009688.svg?style=flat&logo=FastAPI&logoColor=white)](https://fastapi.tiangolo.com/)
[![Ollama](https://img.shields.io/badge/Ollama-Local%20LLM-orange.svg)](https://ollama.com)
[![Privacy](https://img.shields.io/badge/Privacy-100%25%20Local-green.svg)]()

> **Your AI coding assistant remembers everything — across every chat, forever.**

Every time you open a new conversation with Claude, you re-explain your stack, your decisions, what's already built. AI Memory Agent solves this permanently: it captures your project knowledge, stores it locally, and auto-injects it into every new session. Zero cloud. Zero setup per chat. Zero re-explaining.

---

## ⚡ Before vs. After

| Without AI Memory Agent | With AI Memory Agent |
|---|---|
| "We're using FastAPI with JWT auth..." | Agent already knows your auth setup |
| "Don't touch the legacy module..." | Agent knows what to avoid |
| "We use Pydantic v2 with strict mode..." | Agent knows your conventions |
| Re-explaining every new chat | Context injected automatically |

---

## ✨ What It Tracks

Instead of starting every conversation from scratch, the agent continuously tracks and injects:

- **Project Summary** — High-level overview and core business logic
- **Completed Work** — What's already built so the AI doesn't duplicate code
- **Active Sprint/Task** — The immediate objective you're working on
- **Architecture & Tech Decisions** — Design patterns, chosen libraries, constraints
- **Repository Analysis** — Key modules, file structures, and entry points

---

## 🛠️ Tech Stack

| Layer | Technology | Details |
|---|---|---|
| **Backend** | Python 3.10+, FastAPI, Pydantic | Includes an AST Parser for code structure extraction |
| **LLM Engine** | Ollama | Runs 100% locally with `qwen3:8b` or other open weights |
| **Frontend** | Chrome Extension | Injects parsed memory directly into Claude's UI |
| **Storage** | Local JSON | Simple, git-trackable, zero cloud dependencies |

---

## 📐 System Architecture

```text
   ┌────────────────────────┐
   │    Claude Chat (Web)   │
   └───────────┬────────────┘
               │  (1) Capture conversation
               ▼
   ┌────────────────────────┐
   │    Chrome Extension    │
   └───────────┬────────────┘
               │  (2) HTTP POST /memory/event
               ▼
   ┌────────────────────────┐
   │    FastAPI Backend     │
   └─────┬───────────┬──────┘
         │           │
┌────────┘           └────────┐
│ (Internal Scan)             │ (Analyze via Local LLM)
▼                             ▼
┌──────────────────────┐   ┌──────────────────────┐
│  Repository Scanner  │   │     Local Ollama     │
│  (Code AST Parser)   │   │     (qwen3:8b)       │
└─────────┬────────────┘   └──────────┬───────────┘
          │                           │
          ▼                           ▼
┌──────────────────────┐   ┌──────────────────────┐
│ Repository Analysis  │   │   Change Extractor   │
└─────────┬────────────┘   └──────────┬───────────┘
          │                           │
          └────────────┬──────────────┘
                       │ (Memory Merger)
                       ▼
          ┌───────────────────────────┐
          │    project_memory.json    │
          └─────────────┬─────────────┘
                        │
                        ▼
          ┌───────────────────────────┐
          │      Context Generator    │
          └─────────────┬─────────────┘
                        │  (3) Auto-injects payload
                        ▼
          ┌────────────────────────┐
          │  New Claude Chat Window│
          └────────────────────────┘
```

---

## 🚀 Quick Start

### 1. Clone the Repository
```bash
git clone https://github.com/othmanekt0/ai-memory-agent.git
cd ai-memory-agent
```

### 2. Set Up Virtual Environment

**Windows (PowerShell):**
```powershell
python -m venv .venv
.venv\Scripts\activate
```

**Linux/macOS:**
```bash
python3 -m venv .venv
source .venv/bin/activate
```

### 3. Install Backend Dependencies
```bash
pip install -r backend/requirements.txt
```

### 4. Configure & Run Ollama
Download and install [Ollama](https://ollama.com/). Pull your local LLM:
```bash
ollama pull qwen3:8b
```

### 5. Launch the Backend API
```bash
cd backend
uvicorn main:app --reload
```
*Server boots at `http://localhost:8000`.*

### 6. Install the Chrome Extension
1. Open Chrome → navigate to `chrome://extensions/`
2. Toggle **Developer mode** ON (top-right)
3. Click **Load unpacked** (top-left)
4. Select the `extensions/claude-memory` directory

---

## 🔌 API Reference

### Trigger a Repository Scan
```python
from backend.services.repository import RepositoryService

RepositoryService().scan_repository("path/to/your/project")
```

### Key Endpoints
- `POST /memory/event` — Emitted by the Chrome extension to process a new message block
- `GET /memory/context` — Returns the Markdown context payload ready for injection

---

## 🗺️ Roadmap

- [x] Local persistent memory engine
- [x] 100% private local LLM via Ollama
- [x] AST-based static codebase analysis
- [ ] Git hooks — auto-update memory on commit/checkout
- [ ] VSCode Extension — native sidebar to inspect memories
- [ ] Semantic memory — vector storage (Chroma/Qdrant)
- [ ] Multi-project profile management
- [ ] Extended client support (Cursor, Windsurf, Claude Desktop)
