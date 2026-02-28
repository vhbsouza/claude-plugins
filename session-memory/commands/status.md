---
description: Show project status dashboard from memory
allowed-tools: Read, Bash(ls:*), Bash(wc:*), Bash(date:*)
---

You are generating a quick project status dashboard from the memory system. Think of this as `git status` but for project knowledge.

## Steps

1. **Read the sources:**
   - Read `MEMORY.md` (current status, pending items, decision count)
   - Read `CLAUDE.md` (file map)
   - List `snapshots/` to find the latest snapshot
   - List `.claude/rules/` to count knowledge files

2. **Present the dashboard:**

   Format the output as a concise status report:

   ```
   📋 [Project Name] — Status

   Last updated: [date from MEMORY.md]
   Knowledge files: [count] files in .claude/rules/
   MEMORY.md: [line count]/200 lines used
   Last snapshot: [most recent snapshot date, or "none"]

   Current State:
   [1-2 line summary from MEMORY.md "Current Status" section]

   Pending Items:
   🔴 Alta: [count] items
   🟡 Media: [count] items
   🟢 Baixa: [count] items
   [list the Alta items]

   Recent Decisions:
   [last 3 entries from the decision log]
   ```

3. **Health check:**
   - If MEMORY.md > 180 lines: "⚠️ MEMORY.md is near the 200-line limit — consider running /update-memory to trigger overflow."
   - If last update was > 7 days ago: "⚠️ Memory hasn't been updated in over a week."
   - If no snapshots exist: "⚠️ No snapshots found — consider running /snapshot before your next run."

## Rules

- Keep the output compact — this is a quick glance, not a report
- Use the user's language for all text
- If MEMORY.md or CLAUDE.md don't exist, suggest running `/setup-memory` first
- Don't modify any files — this is read-only
