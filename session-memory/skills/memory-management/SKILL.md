---
name: memory-management
description: >
  Structured five-layer memory system for Claude Code and Cowork projects. Manages CLAUDE.md (index),
  MEMORY.md (working memory), .claude/rules/ (modular context), snapshots/ (versioning), and docs/ (reference).
  Use when the user says "update memory", "save context", "what do we know about",
  "add this to the rules", "update the project status", or at the end of any session
  with significant changes.
version: 0.1.0
---

# Memory Management System

This skill manages a structured context and memory system with five layers. Think of it like a brain: CLAUDE.md is the table of contents, MEMORY.md is working memory, .claude/rules/ is long-term knowledge, snapshots/ is a photo album for versioning, and docs/ is a reference library.

## Architecture

```
CLAUDE.md                    ← Index (< 30 lines). Uses @imports.
MEMORY.md                    ← Working memory (< 200 lines). Status, decisions, pending items.
.claude/rules/
  01-project-overview.md     ← Folder structure, scripts, how to run
  02-[topic].md              ← Domain-specific knowledge
  ...                        ← One file per topic, numbered for ordering
snapshots/
  SNAPSHOT_HISTORY.md         ← Log of all successful runs/milestones
  YYYY-MM-DD/                 ← Timestamped copies of outputs for rollback
docs/
  context/                    ← Domain reference material
  projects/                   ← Detailed project documentation
```

## Layer Responsibilities

### CLAUDE.md — The Index

Purpose: Entry point that Claude reads every session. Must be SHORT.

Rules:
- Maximum 30 lines
- Use `@.claude/rules/XX-topic.md` imports to reference detailed content
- Use `@docs/context/file.md` and `@docs/projects/file.md` for reference docs
- Include a "Signaling rules" section with protocols the user has established
- Never put detailed knowledge here — only pointers

### MEMORY.md — Working Memory

Purpose: Tracks current project state, decisions, and what needs to happen next.

Sections (maintain this structure):
1. **Current Status** — Last update date + brief state summary
2. **Knowledge Map** — Table mapping each `.claude/rules/` file to its contents
3. **Important Decisions Log** — Table with Date, Decision, Reason columns
4. **Priority Pending Items** — Checklist of what still needs to be done

Rules:
- Maximum 200 lines — when approaching the limit, move details to `.claude/rules/`
- Update at the end of every session with significant changes
- Decision log is append-only (never delete old decisions)
- Pending items use `- [ ]` with priority tags: **Alta**, **Media**, **Baixa** (or High, Medium, Low)

### .claude/rules/ — Long-Term Knowledge

Purpose: Detailed, modular context organized by topic.

Rules:
- One file per topic, numbered with zero-padded prefixes: `01-`, `02-`, etc.
- First file is always `01-project-overview.md` (architecture, scripts, setup)
- Use tables for structured data (mappings, statuses, schemas)
- Use code blocks for commands, file formats, examples
- Each file should have a one-line description at the top as a blockquote

### snapshots/ — Versioning

Purpose: Preserve known-good states and track generation history.

Rules:
- `SNAPSHOT_HISTORY.md` logs every successful run with date, files, counts, and status
- Before regenerating outputs, snapshot the current state: `mkdir -p snapshots/YYYY-MM-DD && cp [outputs] snapshots/YYYY-MM-DD/`
- Each snapshot entry includes a context section explaining what changed

### docs/ — Reference Documentation

Purpose: Detailed technical documentation that doesn't change frequently.

Structure:
- `docs/context/` — domain-specific formats, quirks, external system documentation
- `docs/projects/` — detailed business rules, pipeline documentation
- Referenced from CLAUDE.md via `@docs/...` imports

## Operations

### Updating Memory (end of session)

When a session had significant changes, perform these steps:

1. Read current `MEMORY.md`
2. Update the "Current Status" section with today's date and new state
3. If new decisions were made, append to the "Important Decisions Log"
4. Update "Priority Pending Items" (check off completed, add new ones)
5. If MEMORY.md is approaching 200 lines, extract detailed content to a new or existing `.claude/rules/` file
6. If a new rule file was created, update the "Knowledge Map" table in MEMORY.md and add the `@import` in CLAUDE.md

### Adding New Knowledge

When the user shares new information that should be persisted:

1. Determine which `.claude/rules/` file it belongs to
2. If no appropriate file exists, create a new numbered file
3. Add the information with clear structure (tables, code blocks)
4. Update the Knowledge Map in MEMORY.md
5. Add the `@import` to CLAUDE.md if it's a new file

### Retrieving Context

When asked about project state or history:

1. Check MEMORY.md first for current status and recent decisions
2. Check the relevant `.claude/rules/` file for detailed knowledge
3. Check `snapshots/SNAPSHOT_HISTORY.md` for historical context
4. Check `docs/` for reference documentation

### Creating a Snapshot

Before any operation that might change outputs:

1. Create a timestamped directory: `mkdir -p snapshots/YYYY-MM-DD`
2. Copy current outputs: `cp [output-files] snapshots/YYYY-MM-DD/`
3. After the operation succeeds, log the result in `snapshots/SNAPSHOT_HISTORY.md`

## Language Rule

All memory files must be written in the same language the user uses in conversation. If the user writes in Portuguese, all files are in Portuguese. If in English, all in English.

## Overflow Protocol

When MEMORY.md exceeds 180 lines:

1. Identify sections with detailed content that could move to `.claude/rules/`
2. Create or update the appropriate rule file
3. Replace the detailed content in MEMORY.md with a one-line summary and reference
4. Update the Knowledge Map
