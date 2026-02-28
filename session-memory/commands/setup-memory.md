---
description: Scaffold a structured memory system for the current project
allowed-tools: Read, Write, Edit, Bash(mkdir:*), Bash(ls:*), Glob, Grep
argument-hint: [project-name]
---

You are setting up a structured memory and context management system for a Claude Code / Cowork project. This system uses five layers — think of it like a brain: CLAUDE.md is the "index" (quick lookup), MEMORY.md is "working memory" (current state), .claude/rules/ is "long-term memory" (detailed knowledge), snapshots/ is "photo album" (versioning), and docs/ is "reference library" (domain documentation).

## What you will create

### 1. CLAUDE.md (Project Index)

Create `CLAUDE.md` at the workspace root. This is the entry point Claude reads on every session. It should be SHORT (an index, not a dump) and use `@` imports to reference rule files.

```markdown
# [Project Name]

[One-line description of what this project does.]

## Instructions by topic

@.claude/rules/01-project-overview.md
@.claude/rules/02-[domain-specific].md

## Reference documentation

@docs/context/[relevant-docs].md

## Signaling rules (always follow)

- When modifying files: report which files changed
- Memory: update `MEMORY.md` at the end of sessions with significant changes
```

Ask the user:
- What is the project name? (use `$1` if provided as argument)
- One-line description of the project
- What are the main topics/domains this project covers? (these become the numbered rule files)

### 2. MEMORY.md (Working Memory)

Create `MEMORY.md` at the workspace root. This is the living document that tracks current state. Structure:

```markdown
# MEMORY.md — [Project Name]

> **Protocol:** Update this file at the end of each session with significant changes.
> Keep this file under 200 lines — move details to `.claude/rules/`.

---

## Current Status

**Last updated:** [today's date]

[Brief description of current state]

---

## Knowledge Map

| File | Contains |
|------|----------|
| `.claude/rules/01-project-overview.md` | [description] |
| `.claude/rules/02-...` | [description] |

---

## Important Decisions Log

| Date | Decision | Reason |
|------|----------|--------|
| [today] | Initial project setup with memory system | Structured context management |

---

## Priority Pending Items

- [ ] [First pending item — ask the user]
```

### 3. .claude/rules/ (Modular Context)

Create the `.claude/rules/` directory with numbered files. Always start with:

- `01-project-overview.md` — folder architecture, key files, how to run things

Then create additional files based on the topics the user identified. Each file should follow this pattern:

```markdown
# [Topic Title]

> [One-line description of what this file covers]

## [Subsection]

[Content organized with tables, code blocks, and clear structure]
```

### 4. snapshots/ (Versioning)

Create `snapshots/SNAPSHOT_HISTORY.md`:

```markdown
# SNAPSHOT_HISTORY.md — Generation History

> **Protocol:** Record every successful generation/milestone here.
> Format: `[Date] - [What was generated] | [Observations]`

---

## How to use the `/snapshots` folder

To preserve a successful state before regenerating:

\`\`\`bash
mkdir -p snapshots/YYYY-MM-DD
cp [relevant-output-files] snapshots/YYYY-MM-DD/
\`\`\`

This allows reverting to a known-good state if a new run breaks something.

---

## Run Log

(No runs recorded yet — this file will be updated as the project progresses.)
```

### 5. docs/ (Reference Documentation)

Create the directory structure:

```
docs/
  context/    ← domain-specific reference material
  projects/   ← detailed project documentation
```

Create placeholder files if the user describes specific documentation needs.

## Execution steps

1. Ask the user the clarifying questions above (project name, description, topics)
2. Create all directories: `.claude/rules/`, `snapshots/`, `docs/context/`, `docs/projects/`
3. Create `CLAUDE.md` with `@` imports pointing to the rule files
4. Create `MEMORY.md` with initial structure
5. Create `01-project-overview.md` with the folder architecture
6. Create additional rule files based on user's topics (just structure/headers — the user will fill details over time)
7. Create `snapshots/SNAPSHOT_HISTORY.md`
8. Show the user a summary of everything created

## Important rules

- All files should be in the SAME LANGUAGE the user is using in the conversation
- Keep CLAUDE.md under 30 lines — it's an index, not documentation
- Keep MEMORY.md under 200 lines — overflow goes to `.claude/rules/`
- Number rule files with zero-padded prefixes (01-, 02-, etc.) for ordering
- Use tables for structured data (status, mappings, etc.)
- Include the "Protocol" reminder at the top of MEMORY.md and SNAPSHOT_HISTORY.md
