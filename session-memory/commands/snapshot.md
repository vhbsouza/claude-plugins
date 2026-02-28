---
description: Save a snapshot of current outputs before changes
allowed-tools: Read, Write, Edit, Bash(mkdir:*), Bash(cp:*), Bash(ls:*), Bash(date:*), Bash(wc:*)
argument-hint: [output-directory]
---

You are creating a pre-run snapshot — a safety net before any operation that modifies generated outputs.

## Steps

1. **Determine what to snapshot:**
   - If the user provided `$1` (output directory), use that
   - Otherwise, look at `CLAUDE.md` and `.claude/rules/01-project-overview.md` to identify the main output directory
   - If still unclear, ask the user: "Which directory contains the outputs you want to snapshot?"

2. **Create the snapshot:**
   ```bash
   # Get today's date
   DATE=$(date +%Y-%m-%d)

   # Check if snapshot for today already exists
   if [ -d "snapshots/$DATE" ]; then
     # Find next available version
     V=2
     while [ -d "snapshots/$DATE-v$V" ]; do V=$((V+1)); done
     DATE="$DATE-v$V"
   fi

   mkdir -p "snapshots/$DATE"
   ```

3. **Copy outputs:**
   - Copy all files from the output directory to the snapshot
   - Show the user what was copied and the total size

4. **Log the snapshot:**
   - Read `snapshots/SNAPSHOT_HISTORY.md`
   - Add a brief entry noting the pre-run snapshot:
     ```markdown
     ### [DATE] — Pre-run snapshot

     **Context:** Snapshot before [describe what the user is about to do]

     **Files preserved:**
     - [list of files copied]
     ```

5. **Confirm to the user:**
   - "Snapshot saved to `snapshots/[DATE]/` — safe to proceed."
   - Show the rollback command: `cp snapshots/[DATE]/* [output-dir]/`

## Rules

- Always check if a snapshot directory for today already exists (use -v2, -v3 suffixes)
- Never overwrite an existing snapshot
- Write log entries in the user's language
- If the output directory is empty or doesn't exist, warn the user instead of creating an empty snapshot
