# 🧠 AI Memory Agent

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![Python 3.10+](https://img.shields.io/badge/Python-3.10+-blue.svg)](https://www.python.org/downloads/)
[![FastAPI](https://img.shields.io/badge/FastAPI-0.100+-009688.svg?style=flat\&logo=FastAPI\&logoColor=white)](https://fastapi.tiangolo.com/)
[![Ollama](https://img.shields.io/badge/Ollama-Local%20LLM-orange.svg)](https://ollama.com)
[![Privacy](https://img.shields.io/badge/Privacy-Local-green.svg)]()

> **A persistent AI memory system that preserves project context across conversations.**

AI coding assistants typically treat each conversation as a separate context. When starting a new session, developers often need to re-explain the project's architecture, technical decisions, completed work, conventions, and current tasks.

**AI Memory Agent** addresses this problem by capturing project knowledge from conversations and the codebase, extracting relevant changes with a local LLM, and maintaining a structured persistent memory that can be automatically injected into new Claude conversations.

The system is designed to run locally using **Ollama**, with project memory stored locally rather than relying on a remote memory service.

---

## ⚡ Problem → Solution

| Without AI Memory Agent                               | With AI Memory Agent                               |
| ----------------------------------------------------- | -------------------------------------------------- |
| Re-explain the project at the beginning of every chat | Project context is already available               |
| Repeat architecture and technology decisions          | Architecture and decisions are persisted           |
| Explain what has already been implemented             | Completed work is tracked                          |
| Manually provide project conventions and constraints  | Relevant constraints can be injected automatically |
| Context is lost between conversations                 | Project knowledge persists across sessions         |

---

## ✨ What It Remembers

The memory engine maintains structured project knowledge rather than simply storing raw conversations.

It can track:

* **Project Summary** — High-level project purpose and business context
* **Completed Work** — Implemented features and completed tasks
* **Active Work** — Current objectives and ongoing tasks
* **Architecture & Technical Decisions** — Technologies, patterns, constraints, and design choices
* **Project Conventions** — Important implementation rules and project-specific preferences
* **Repository Structure** — Relevant modules, files, entry points, and codebase information

---

## 🏗️ Core Architecture

The system is organized around a persistent **Memory Engine** that receives information from both conversations and repository analysis.

```text
                         ┌─────────────────────────┐
                         │      Claude Chat        │
                         │        (Web)            │
                         └────────────┬────────────┘
                                      │
                              Conversation Event
                                      │
                                      ▼
                         ┌─────────────────────────┐
                         │    Chrome Extension     │
                         └────────────┬────────────┘
                                      │
                              HTTP / REST API
                                      │
                                      ▼
                         ┌─────────────────────────┐
                         │     FastAPI Backend     │
                         └────────────┬────────────┘
                                      │
                                      ▼
                         ┌─────────────────────────┐
                         │     Memory Service      │
                         └────────────┬────────────┘
                                      │
                                      ▼
                         ┌─────────────────────────┐
                         │      Memory Engine      │
                         └──────┬──────────┬───────┘
                                │          │
                    Conversation │          │ Repository
                       Analysis  │          │ Analysis
                                ▼          ▼
                    ┌──────────────┐  ┌─────────────────┐
                    │    Change    │  │   Repository    │
                    │   Extractor  │  │    Analysis     │
                    │  (Local LLM) │  │  / AST Parser   │
                    └──────┬───────┘  └────────┬────────┘
                           │                   │
                           └─────────┬─────────┘
                                     ▼
                         ┌─────────────────────────┐
                         │      Memory Merger      │
                         │   Incremental Updates   │
                         └────────────┬────────────┘
                                      │
                                      ▼
                         ┌─────────────────────────┐
                         │      Memory Store       │
                         │   project_memory.json   │
                         └────────────┬────────────┘
                                      │
                                      ▼
                         ┌─────────────────────────┐
                         │    Context Generator    │
                         └────────────┬────────────┘
                                      │
                              Context Payload
                                      │
                                      ▼
                         ┌─────────────────────────┐
                         │   New Claude Session    │
                         │  with Project Context   │
                         └─────────────────────────┘
```

---

## 🧩 Memory Engine

The core of the system is the **Memory Engine**, which separates memory extraction, consolidation, and persistence.

### 1. Change Extraction

The **ChangeExtractor** uses a local LLM through Ollama to analyze new conversation content and identify information that should modify the existing project memory.

For example, a conversation may introduce:

* a new technology
* an architectural decision
* a completed task
* a new project constraint
* an updated implementation detail

The extractor converts this information into structured memory updates rather than storing the entire conversation.

### 2. Memory Merging

The **MemoryMerger** applies extracted changes to the existing project memory.

This allows the memory to evolve incrementally instead of creating a separate memory snapshot for every conversation.

Conceptually:

```text
Existing Memory
      +
New Extracted Changes
      │
      ▼
 Memory Merger
      │
      ▼
Updated Project Memory
```

### 3. Persistent Storage

The **MemoryStore** persists the resulting structured memory locally in:

```text
project_memory.json
```

This keeps the project knowledge:

* persistent across conversations
* locally accessible
* easy to inspect
* easy to version-control
* independent from a remote memory database

---

## 🔍 Repository Analysis

AI Memory Agent can also analyze the project codebase to provide technical context independently from conversations.

The repository analysis component can extract information such as:

* project structure
* Python modules
* classes and functions
* entry points
* relevant source files
* code organization

An **AST-based parser** is used to inspect Python source code structurally rather than relying only on raw text.

This provides a second source of project knowledge:

```text
Conversation Context ──────┐
                           │
                           ▼
                      Memory Engine
                           ▲
                           │
Repository Analysis ───────┘
```

Combining conversational knowledge with repository-level information allows the generated context to represent both **what has been discussed** and **what exists in the codebase**.

---

## 🛠️ Tech Stack

| Layer             | Technology                      | Role                                        |
| ----------------- | ------------------------------- | ------------------------------------------- |
| **Backend**       | Python 3.10+, FastAPI, Pydantic | REST API and application services           |
| **Memory Engine** | Python                          | Memory extraction, merging, and persistence |
| **LLM Engine**    | Ollama + `qwen3:8b`             | Local AI-based change extraction            |
| **Code Analysis** | Python AST                      | Repository structure analysis               |
| **Frontend**      | Chrome Extension                | Captures conversations and injects context  |
| **Storage**       | JSON                            | Persistent structured project memory        |
| **Communication** | HTTP / REST                     | Extension ↔ FastAPI communication           |

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

### 4. Configure Ollama

Install [Ollama](https://ollama.com/) and pull the local model:

```bash
ollama pull qwen3:8b
```

### 5. Launch the Backend API

```bash
cd backend
uvicorn main:app --reload
```

The API will be available at:

```text
http://localhost:8000
```

### 6. Install the Chrome Extension

1. Open Chrome and navigate to `chrome://extensions/`
2. Enable **Developer mode**
3. Click **Load unpacked**
4. Select:

```text
extensions/claude-memory
```

---

## 🔌 API Reference

### Process a Conversation Event

```http
POST /memory/event
```

Used by the Chrome extension to send new conversation content to the backend for memory processing.

### Retrieve Project Context

```http
GET /memory/context
```

Returns the generated project context in a format suitable for injection into a new AI conversation.

### Trigger Repository Analysis

```python
from backend.services.repository import RepositoryService

RepositoryService().scan_repository("path/to/your/project")
```

---

## 🔐 Local-First Design

AI Memory Agent is designed around a local-first architecture.

The LLM processing is performed through **Ollama**, allowing the memory extraction process to run locally without requiring a cloud-based LLM API.

Project memory is persisted locally in `project_memory.json`.

```text
Conversation
     │
     ▼
Chrome Extension
     │
     ▼
Local FastAPI Backend
     │
     ├──────► Local Repository Analysis
     │
     └──────► Local Ollama LLM
                    │
                    ▼
              Memory Engine
                    │
                    ▼
          project_memory.json
```

---

## 🗺️ Roadmap

* [x] Local persistent memory engine
* [x] Local LLM integration through Ollama
* [x] AST-based static codebase analysis
* [x] Conversation-driven memory updates
* [x] Automatic context generation
* [ ] Git hooks — automatically update memory on commit/checkout
* [ ] VSCode Extension — inspect project memory from the editor
* [ ] Semantic memory — vector storage with Chroma/Qdrant
* [ ] Multi-project memory profiles
* [ ] Extended client support — Cursor, Windsurf, Claude Desktop

---

## 📄 License

This project is licensed under the **MIT License**.
