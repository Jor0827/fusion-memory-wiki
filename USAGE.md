# Usage Guide

> How to use Fusion Memory Wiki in daily workflows.

## Daily Workflow

### Session Start

```
1. Read MEMORY.md          # Core identity and key facts
2. Read today's log        # memory/YYYY-MM-DD.md
3. Read wiki index         # wiki/index.md
4. Check recent pages     # Recent updates in wiki/
5. Run mempalace wake-up  # Get ~800 tokens context
```

### During Work

**When you learn something new:**
- Add to today's log: `memory/YYYY-MM-DD.md`
- If significant, create/update wiki page

**When you need information:**
- Quick answer: `mempalace search "query"`
- Deep understanding: Read wiki pages
- Historical context: Check memory logs

**When you complete a task:**
- Log in `memory/YYYY-MM-DD.md`
- Update relevant wiki page
- Add cross-references

### Session End

```
1. Summarize session in today's log
2. Note any new entities/concepts discovered
3. Decide: Create wiki page? Update existing?
4. Commit: mempalace mine "workspace"
```

## MemPalace Commands

### Search

```powershell
$env:PYTHONIOENCODING = "utf-8"

mempalace search "精益生产"
# Returns semantic matches with relevance scores
```

### Wake-up

```powershell
mempalace wake-up
# Outputs ~800 tokens:
# - L0: Identity + key facts
# - L1: Recent session summaries
```

### Status

```powershell
mempalace status
# Shows: wings, rooms, file count, drawer count
```

### Re-index

```powershell
# After adding new files
mempalace mine "C:\path\to\workspace"

# Specific mode for conversations
mempalace mine "C:\path\to\workspace" --mode convos
```

## Wiki Operations

### Creating a New Entity

```markdown
# Project Alpha

> Next-gen AI assistant for productivity.

## Basic Info
- Type: Project
- Status: Active
- Lead: @username
- Related: [[Project Beta]]

## Timeline
- 2026-01: Project started
- 2026-03: V1.0 released

## Key Information
[What this project does, key features, etc.]

## Sources
- [[Meeting Notes 2026-01]]
- [[Design Doc]]
```

### Creating a Decision Record

```markdown
# Choose Vector Database

> Decided to use ChromaDB for vector storage.

## Background
Needed a local vector DB with zero API dependency.

## Options Considered
1. **ChromaDB**: Local, Python-native, active development
2. **Qdrant**: Requires Docker, more complex
3. **Pinecone**: Cloud-only, API costs

## Decision
- **Choice**: ChromaDB
- **Rationale**: Zero API, Python-native, MemPalace support
- **Date**: 2026-04-10

## Consequences
- Can run offline
- No API costs
- Dependent on MemPalace ecosystem

## Review Date
2026-07-10
```

### Adding Cross-References

Always link both ways:

```markdown
# In [[Project Alpha]]
Related: [[Project Beta]]

# In [[Project Beta]]
Related: [[Project Alpha]]
```

### Lint Operations

Check for issues:

```markdown
## Manual Lint Checklist

### Contradictions
[Check: Do any pages have conflicting info?]

### Orphans
[Pages with no inbound links: list them]

### Gaps
[Concepts mentioned but no page: list them]

### Stale
[Info superseded: mark or remove]
```

## Multi-Agent Collaboration

### Agent A: Creates Wiki Page

```powershell
# Agent A explores a project
mempalace search "Lean Simulator"
# ... reads code ...
# ... creates wiki page ...
```

### Agent B: Reads and Builds

```powershell
# Agent B starts session
# Reads wiki/index.md
# Sees new page: [[Lean Simulator]]
# Reads it → understands without exploring code
# Creates related page: [[Lean Manufacturing]]
```

### Agent C: Benefits from Both

```
Agent A knows: Project X details
Agent B knows: Project Y details
Agent C reads both → knows X + Y → can connect them
```

## Best Practices

### Do

- ✅ Log everything in daily memory
- ✅ Create wiki pages for significant knowledge
- ✅ Cross-reference liberally
- ✅ Re-index after changes
- ✅ Run lint periodically
- ✅ Share wiki pages with other agents

### Don't

- ❌ Modify raw/ files in place
- ❌ Skip daily logging
- ❌ Create orphan pages without links
- ❌ Ignore contradictions
- ❌ Keep stale information

### Tips

- **Start small**: Begin with just MEMORY.md + daily logs
- **Add wiki gradually**: Only when knowledge compounds
- **Be consistent**: Use same templates
- **Review weekly**: Lint catches drift
- **Share early**: Other agents benefit from your work

## Integration with Workflows

### Research Task

```
1. mempalace search "topic"
2. Read wiki for existing knowledge
3. Add new findings to wiki
4. Cross-reference with related concepts
5. Log in daily memory
```

### Code Exploration

```
1. Explore codebase
2. mempalace mine "project" --mode projects
3. Create entity page for project
4. Document key modules in wiki
5. Link to architecture decisions
```

### Decision Making

```
1. Research options (search + wiki)
2. Document in decision page
3. Include pros/cons, rationale
4. Set review date
5. Share with other agents
```

## Common Patterns

### Pattern: Project Entity

```markdown
# [Project Name]

> One-line pitch

## Status
- Phase: [Discovery/Alpha/Beta/Live]
- Team: [Who works on it]

## Goals
- Primary: [Goal 1]
- Secondary: [Goal 2]

## Progress
[What's done, what's next]

## Dependencies
- [[Related Project]]
- External: [APIs, services]
```

### Pattern: Technology Concept

```markdown
# [Technology]

> What it is

## Key Concepts
- [Concept 1]: Explanation
- [Concept 2]: Explanation

## Use Cases
- When to use: [Scenario]
- When not to: [Scenario]

## Resources
- Official: [Link]
- Tutorial: [Link]
```

## ⚠️ Known Pitfalls & Troubleshooting

### "Agent doesn't use the memory system"

This is the **#1 reported issue**. Symptoms:
- Agent knows about the system (you told it, or it read the docs) but doesn't write files
- Agent writes files in wrong format or wrong location
- Old sessions ignore the memory system entirely

**Root cause**: Architecture descriptions in AGENTS.md are NOT enough. The agent also needs **concrete operational instructions** — exact steps for writing files, which tools to use, and what format to follow.

**Fix**: Ensure `AGENTS.md` includes ALL of these:

| Instruction | Example |
|-------------|---------|
| How to write files safely | "Use qclaw-text-file skill's write_file.py" or equivalent |
| Where to write | "Log to `memory/YYYY-MM-DD.md`" |
| What format | "## [HH:MM] Event title" |
| Post-write action | "Run `mempalace mine` after writing" |
| Wiki operation flow | "Read SCHEMA.md → write page → update index → log" |

**For old sessions**: Sessions that started before AGENTS.md was updated will have the old version in context. Even if you ask the agent to re-read AGENTS.md, it may not internalize the new instructions if the conversation history is long (context window pressure). **Recommendation: start a new session** after updating AGENTS.md.

### "Unicode errors on Windows"

```powershell
# Always set before running mempalace
$env:PYTHONIOENCODING = "utf-8"
```

### "Empty search results"

```powershell
# Re-index
mempalace mine "path/to/workspace"

# Check status
mempalace status
```

### "chroma-hnswlib build fails"

This is optional. ChromaDB works without it. If you need it:
- Try `pip install chroma-hnswlib==0.7.6a9` (has precompiled wheels for Windows)
- Or `pip install mempalace --no-deps` then install dependencies separately

### "Old session doesn't follow updated instructions"

Workspace files (AGENTS.md, SOUL.md, etc.) are injected once at session start. They don't refresh mid-session. Even if the agent re-reads the file, it has a full conversation history competing for context window space.

**Solutions**:
1. **Best**: Start a new session after updating workspace files
2. **Workaround**: Ask the agent to re-read the specific file, then give a short prompt that triggers the behavior (don't assume it will "just know")
3. **Prevention**: Put the most critical instructions at the TOP of AGENTS.md, not buried in sections

---

_This system grows more valuable with consistent use. Make logging a habit._
