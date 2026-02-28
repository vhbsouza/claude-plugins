---
name: snapshot-versioning
description: >
  Snapshot and versioning system for project outputs. Creates timestamped copies
  of generated files and maintains a run history log.
  Use when the user says "take a snapshot", "save current state", "backup before running",
  "log this run", "what was the last snapshot", or before regenerating any outputs.
version: 0.1.0
---

# Snapshot & Versioning System

Manages timestamped snapshots of project outputs and maintains a historical log of all successful runs. Think of it as "git for project outputs" — before you regenerate, you save; after you succeed, you log.

## Directory Structure

```
snapshots/
  SNAPSHOT_HISTORY.md          ← Master log of all runs (append-only)
  2026-02-27/                  ← Timestamped copy of outputs from that date
    file1.csv
    file2.csv
  2026-03-15/
    ...
```

## Operations

### Pre-Run Snapshot

Before any operation that modifies generated outputs:

1. Get today's date in YYYY-MM-DD format
2. Create the snapshot directory:
   ```bash
   mkdir -p snapshots/YYYY-MM-DD
   ```
3. Copy current outputs to the snapshot:
   ```bash
   cp [output-directory]/* snapshots/YYYY-MM-DD/
   ```
4. Inform the user: "Snapshot saved to `snapshots/YYYY-MM-DD/` — you can rollback if needed."

### Post-Run Logging

After a successful generation/build/run:

1. Read current `snapshots/SNAPSHOT_HISTORY.md`
2. Append a new entry with this structure:

```markdown
### YYYY-MM-DD — [Brief context description]

**Context:** [What changed or triggered this run]

**Files generated:**

| Source | -> | Output | Records | Status |
|--------|---|--------|---------|--------|
| [input files] | -> | [output file] | [count] | [status emoji + text] |

**Commands run:**
\`\`\`
[commands that were executed]
\`\`\`
```

3. Always include record counts when applicable
4. Use status indicators: `VALIDATION OK`, `WARNING: [issue]`, `FAILED: [reason]`

### Rollback

If a run produces bad output and the user wants to revert:

1. Identify the most recent good snapshot: `ls snapshots/`
2. Confirm with the user which snapshot to restore from
3. Copy files back:
   ```bash
   cp snapshots/YYYY-MM-DD/* [output-directory]/
   ```
4. Log the rollback in SNAPSHOT_HISTORY.md:
   ```markdown
   ### YYYY-MM-DD — ROLLBACK to snapshot YYYY-MM-DD
   **Reason:** [why the rollback was needed]
   ```

### Querying History

When asked about past runs:

1. Read `snapshots/SNAPSHOT_HISTORY.md`
2. Present a summary table of recent runs with dates, file counts, and status
3. If asked for details on a specific run, show the full entry

## Rules

- SNAPSHOT_HISTORY.md is append-only — never delete or modify past entries
- Always snapshot BEFORE running, never after (you can't snapshot what you've already overwritten)
- Use the same language as the user for all log entries
- Include enough context in each entry that someone reading it months later can understand what happened
- When a snapshot directory already exists for today, append a suffix: `YYYY-MM-DD-v2`, `YYYY-MM-DD-v3`

## Integration with Memory System

After a successful run that is logged in SNAPSHOT_HISTORY.md:

1. Update `MEMORY.md` "Current Status" section with the new state
2. If the run involved new decisions, add them to the "Important Decisions Log"
3. Update any relevant `.claude/rules/` files if the run revealed new knowledge
