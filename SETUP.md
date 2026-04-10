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

# Create all directories at once (recommended for Windows)
New-Item -ItemType Directory -Path wiki/pages/entities, wiki/pages/concepts, wiki/pages/decisions, wiki/pages/synthesis, wiki/raw/articles, wiki/raw/exports, wiki/raw/assets, memory -Force

# Alternative: separate mkdir commands (Linux/macOS preferred)
# mkdir -p wiki/pages/entities wiki/pages/concepts wiki/pages/decisions wiki/pages/synthesis
# mkdir -p wiki/raw/articles wiki/raw/exports wiki/raw/assets
# mkdir memory
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

Create `AGENTS.md` in workspace root (⚠️ **most critical file** — this is auto-injected into every session):

```markdown
# AGENTS.md - Your Workspace

## Session Startup

Before doing anything else:

1. Read `SOUL.md` — this is who you are
2. Read `USER.md` — this is who you're helping
3. Read `memory/YYYY-MM-DD.md` (today + yesterday) for recent context
4. Also read `MEMORY.md`

## Memory — 三层融合架构

You wake up fresh each session. These files are your continuity:

```
MEMORY.md      ← L0: 核心索引（session 启动必读）
memory/        ← L1: 每日日志（YYYY-MM-DD.md）
wiki/          ← L2: 结构化知识库
```

### 🔧 操作速查（必读）

**写文件必须用你的平台对应的编码安全工具**（不要直接用 write 工具）：

<details>
<summary>OpenClaw + qclaw-text-file skill 的写文件流程</summary>

```
1. 先读取 skill: read("path/to/qclaw-text-file/SKILL.md")
2. 内容写到临时文件: write(path="/tmp/_tw_xxx.md", content=...)
3. 用脚本写入目标文件: exec("python path/to/write_file.py --path <目标路径> --content-file <临时文件>")
4. 删除临时文件
```

</details>

<details>
<summary>通用建议（非 OpenClaw 平台）</summary>

- 确保文件编码为 UTF-8（Windows 上建议 UTF-8 with BOM 用于 CSV）
- 使用平台推荐的换行符（Windows: CRLF, macOS/Linux: LF）
- 写入后验证文件可正常读取

</details>

**记录日志到 memory/YYYY-MM-DD.md**：
- 格式：`## [HH:MM] 事件标题` + 正文
- 追加模式：先 read 现有内容，拼接后再写回

**创建/更新 Wiki 页面**：
- 先读 `wiki/SCHEMA.md` 了解页面格式规范
- 页面放入 `wiki/pages/{entities|concepts|decisions|synthesis}/`
- 写完后更新 `wiki/index.md` 索引
- 在 `wiki/log.md` 记录操作

**更新 MemPalace 索引**：
```powershell
$env:PYTHONIOENCODING = "utf-8"
mempalace mine "path/to/workspace"
```

**搜索记忆**：
```powershell
$env:PYTHONIOENCODING = "utf-8"
mempalace search "关键词"
```

## Memory Maintenance Flow

1. **During sessions**: Write to `memory/YYYY-MM-DD.md` for logs
2. **When decisions happen**: Update `MEMORY.md` + create decision page in wiki
3. **After writing files**: Run `mempalace mine` to update vector index

## Write It Down - No "Mental Notes"!

- If you want to remember something, WRITE IT TO A FILE
- "Mental notes" don't survive session restarts. Files do.
- **Text > Brain** 📝
```

> ⚠️ **Why AGENTS.md is critical**: This file is auto-injected into every session's system prompt.
> Without it, the agent knows nothing about the memory system even if all wiki/ and memory/ files exist.
> The "操作速查" section provides concrete steps — architecture descriptions alone are NOT enough.

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
