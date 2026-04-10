# Setup Guide

> Step-by-step instructions to set up Fusion Memory Wiki.

## Prerequisites

### Python

```bash
# Check version (need 3.12+)
python --version

# If not installed, download from:
# https://www.python.org/downloads/
```

### Git (Optional)

```bash
# Check installation
git --version

# If not installed:
# https://git-scm.com/download
```

## Installation

### 1. Install MemPalace

```powershell
# Windows: Set encoding first
$env:PYTHONIOENCODING = "utf-8"

# Install MemPalace and dependencies
pip install mempalace

# Install ChromaDB (vector database)
pip install chromadb

# Optional: Install chroma-hnswlib for faster vector search
# If installation fails, ChromaDB will use an alternative implementation
pip install chroma-hnswlib
```

### 2. Create Directory Structure

```powershell
# Navigate to your workspace
cd C:\path\to\your\workspace

# Create directories
mkdir wiki\pages\entities
mkdir wiki\pages\concepts
mkdir wiki\pages\decisions
mkdir wiki\pages\synthesis
mkdir wiki\raw\articles
mkdir wiki\raw\exports
mkdir wiki\raw\assets
mkdir memory
```

### 3. Initialize MemPalace

```powershell
# Set encoding
$env:PYTHONIOENCODING = "utf-8"

# Initialize (--yes for non-interactive mode)
mempalace init "C:\path\to\your\workspace" --yes
```

This creates:
- `mempalace.yaml` — Wing/room configuration
- `~/.mempalace/config.json` — Global config
- `~/.mempalace/palace/` — Vector database

### 4. Create Core Files

Create `MEMORY.md` in workspace root:

```markdown
# MEMORY.md

## About
[Your agent identity and purpose]

## Key Knowledge
- Project A: [[wiki/pages/entities/project-a.md]]
- Project B: [[wiki/pages/entities/project-b.md]]

## Current Focus
[What you're working on]

## Preferences
[Your human's preferences]
```

Create `wiki/SCHEMA.md`:

```markdown
# Wiki Schema

[Copy from this project's wiki/SCHEMA.md]
```

## Verification

### Test MemPalace

```powershell
$env:PYTHONIOENCODING = "utf-8"

# Index files
mempalace mine "C:\path\to\your\workspace"

# Search test
mempalace search "test"

# Wake-up test
mempalace wake-up
```

### Test Wiki

```markdown
# Test Entity

> This is a test

## Basic Info
- Type: Test
- Status: Active

## Details
Testing the wiki system.
```

Then re-index:

```powershell
mempalace mine "C:\path\to\your\workspace"
```

## Configuration

### MemPalace Config

Location: `~/.mempalace/config.json`

```json
{
  "palace_path": "C:/Users/{username}/.mempalace/palace",
  "collection_name": "workspace",
  "topic_wings": {
    "memory": ["journal", "notes"],
    "projects": ["code", "docs"],
    "knowledge": ["wiki", "research"]
  }
}
```

### Environment Variables

```powershell
# Required for Windows with Unicode content
$env:PYTHONIOENCODING = "utf-8"

# Optional: Set in $PROFILE for persistence
# Notepad $PROFILE
# Add: $env:PYTHONIOENCODING = "utf-8"
```

## Multi-Agent Setup

For multiple agents to share:

```powershell
# All agents use the same workspace
cd C:\shared\workspace

# Each agent:
# 1. Install MemPalace locally
# 2. Point to same workspace
# 3. Read/write wiki pages

mempalace init "C:\shared\workspace" --yes
mempalace mine "C:\shared\workspace"
```

All agents will:
- Share the same vector database
- Read each other's wiki pages
- Build on collective knowledge

## Troubleshooting

### Unicode Errors on Windows

```powershell
# Always set before running mempalace
$env:PYTHONIOENCODING = "utf-8"
```

### chroma-hnswlib Build Errors

```powershell
# Skip this optional step - ChromaDB will work without it
# Just proceed without chroma-hnswlib installed
```

### Empty Search Results

```powershell
# Re-index
mempalace mine "C:\path\to\your\workspace"

# Check status
mempalace status
```

### Corrupted Vector Index

```powershell
# Repair
mempalace repair
```

## Next Steps

1. Read [ARCHITECTURE.md](ARCHITECTURE.md) for design details
2. Create your first wiki page
3. Set up a regular ingest schedule
4. Invite another agent to share knowledge
