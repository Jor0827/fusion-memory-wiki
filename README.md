# Fusion Memory Wiki for AI Agents

> Build persistent, shareable memory for AI agents by combining vector search (MemPalace) with synthesized knowledge (LLM Wiki).

> A three-layer memory system combining **MemPalace** (full-text vector storage) with **LLM Wiki** (incremental knowledge synthesis). Enable multiple AI agents to share knowledge, context, and memories.

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)
[![Python 3.12+](https://img.shields.io/badge/Python-3.12+-blue.svg)](https://www.python.org/)

## The Problem

AI agents today face a critical challenge: **memory fragmentation**. Each conversation starts fresh, losing context from previous sessions. Even when agents share the same workspace, they can't learn from each other's work.

## The Solution

A fusion memory architecture that combines two complementary approaches:

| Layer | Technology | Purpose |
|-------|------------|---------|
| **L0** | `MEMORY.md` | Core index: key facts, decisions, project status |
| **L1** | `memory/YYYY-MM-DD.md` | Daily logs: raw session records |
| **L2** | `wiki/` | Knowledge base: synthesized entity/concept/decision pages |

**Engine**: [MemPalace](https://github.com/milla-jovovich/mempalace) provides vector search + wake-up context recovery.

## Why This Works

| Feature | Traditional Approach | Fusion Memory |
|---------|---------------------|----------------|
| Context Recovery | Start from scratch | `mempalace wake-up` → ~800 tokens instant context |
| Knowledge Synthesis | Lost between sessions | Wiki pages with cross-references |
| Cross-Agent Sharing | Impossible | Read each other's wiki pages |
| Full-Text Search | Need external tools | Built-in vector search |
| Contradiction Detection | Manual | Lint operation catches conflicts |

## Quick Start

> ⚠️ **Windows Users**: Always set `$env:PYTHONIOENCODING = "utf-8"` before running MemPalace commands to avoid Unicode/emoji display issues.

### Prerequisites

```bash
# Python 3.12+
python --version

# Install MemPalace and dependencies
pip install mempalace
pip install chromadb  # if not already installed
```

### Initialize the System

```powershell
# Set encoding for Unicode support
$env:PYTHONIOENCODING = "utf-8"

# Create workspace structure (PowerShell)
New-Item -ItemType Directory -Path wiki/pages/entities, wiki/pages/concepts, wiki/pages/decisions, wiki/pages/synthesis, wiki/raw/articles, wiki/raw/exports, wiki/raw/assets, memory -Force

# Initialize MemPalace
mempalace init "C:\path\to\your\workspace" --yes
```

### First Ingest

Create your first wiki page following the [schema](wiki/SCHEMA.md):

```markdown
# [Entity Name]

> One-line description

## Basic Info
- Type: Project/Tool/Concept
- Status: Active/Completed/Archived
- Related: [[Other Entity]]

## Key Details
[Your synthesized knowledge]

## Sources
- [[Source 1]]
- [[Source 2]]
```

Then run:

```powershell
mempalace mine "C:\path\to\your\workspace"
```

## Architecture

```
┌─────────────────────────────────────────────────────────────┐
│                     Agent Conversation                       │
└─────────────────────────────────────────────────────────────┘
                              │
                              ▼
┌─────────────────────────────────────────────────────────────┐
│  L0: MEMORY.md                                             │
│  - Core identity                                            │
│  - Key decisions                                            │
│  - Current projects status                                  │
└─────────────────────────────────────────────────────────────┘
                              │
                              ▼
┌─────────────────────────────────────────────────────────────┐
│  L1: memory/YYYY-MM-DD.md                                  │
│  - Daily session logs                                       │
│  - Raw conversation records                                 │
│  - Timestamps and context                                  │
└─────────────────────────────────────────────────────────────┘
                              │
                              ▼
┌─────────────────────────────────────────────────────────────┐
│  L2: wiki/                                                 │
│  ┌─────────────┐ ┌─────────────┐ ┌─────────────┐        │
│  │  entities/  │ │  concepts/  │ │  decisions/ │        │
│  │  (people,   │ │  (methods,  │ │  (why we    │        │
│  │   projects) │ │   theories) │ │   chose X)  │        │
│  └─────────────┘ └─────────────┘ └─────────────┘        │
└─────────────────────────────────────────────────────────────┘
                              │
                              ▼
┌─────────────────────────────────────────────────────────────┐
│  MemPalace (ChromaDB + all-MiniLM-L6-v2)                   │
│  - Vector storage of all files                              │
│  - Semantic search                                          │
│  - Wake-up context recovery                                 │
└─────────────────────────────────────────────────────────────┘
```

## Usage Patterns

| Need | Action |
|------|--------|
| Find raw text/details | `mempalace search "query"` |
| Understand synthesized knowledge | Read `wiki/pages/` |
| Trace history | Check `memory/YYYY-MM-DD.md` |
| Quick context recovery | `mempalace wake-up` |
| Knowledge network | Follow `[[wiki links]]` |
| Check for contradictions | Run lint (see SCHEMA.md) |

## Directory Structure

```
your-workspace/
├── AGENTS.md              # ⚠️ CRITICAL: 操作指令（auto-injected every session）
├── MEMORY.md              # L0: Core index
├── SOUL.md                # Agent identity and values
├── USER.md                # User info and preferences
├── memory/
│   └── YYYY-MM-DD.md      # L1: Daily logs
├── wiki/
│   ├── SCHEMA.md          # Maintenance rules
│   ├── index.md           # Content index
│   ├── log.md             # Operation log
│   ├── pages/             # L2: Wiki pages
│   │   ├── entities/      # People, projects
│   │   ├── concepts/      # Methods, theories
│   │   ├── decisions/     # Decision records
│   │   └── synthesis/    # Comprehensive analysis
│   └── raw/               # Immutable source material
│       ├── articles/
│       ├── exports/
│       └── assets/
├── mempalace.yaml         # MemPalace config
└── ...                    # Your other files
```

> ⚠️ **AGENTS.md is the most important file**. It's auto-injected into every session.
> Without operational instructions there, agents will know the system exists but won't know how to use it.
> See [SETUP.md](SETUP.md) step 4 for the required content.

## Inspiration

- **MemPalace** — [https://github.com/milla-jovovich/mempalace](https://github.com/milla-jovovich/mempalace) (MIT License)
- **LLM Wiki** — [Karpathy's Gist](https://gist.github.com/karpathy/442a6bf555914893e9891c11519de94f)

This is an application/integration built on principles from MemPalace and LLM Wiki. Original concepts are copyrighted by their respective authors.

## License

This project is licensed under MIT License - see [LICENSE](LICENSE) for details.

---

_This system evolves as you use it. Each ingest makes it smarter._
