---
description: Update MEMORY.md with current session changes
allowed-tools: Read, Write, Edit, Glob, Grep, Bash(ls:*), Bash(wc:*)
---

You are performing an end-of-session memory update. This is the most important ritual of the memory system — it ensures nothing is lost between sessions.

## Steps

1. **Read current state:**
   - Read `MEMORY.md`
   - Read `CLAUDE.md` to understand the file map
   - Use `ls` and recent conversation context to identify what changed this session

2. **Update MEMORY.md sections:**

   **Current Status:**
   - Set "Last updated" to today's date
   - Write a brief summary of what the project looks like RIGHT NOW (not what changed — what IS)

   **Important Decisions Log:**
   - Append any decisions made during this session (never modify old entries)
   - Format: `| date | decision | reason |`
   - Only log real decisions — not routine operations

   **Priority Pending Items:**
   - Check off items that were completed: `- [ ]` → `- [x]`
   - Add new items discovered during the session
   - Use priority tags: **Alta/High**, **Media/Medium**, **Baixa/Low**

   **Knowledge Map:**
   - If new `.claude/rules/` files were created, add them to the table
   - If file contents changed significantly, update the description

3. **Check overflow:**
   - Count lines in MEMORY.md: `wc -l MEMORY.md`
   - If over 180 lines, identify detailed sections that can move to `.claude/rules/`
   - Move the content, replace with a one-line summary + reference
   - Update CLAUDE.md with new `@import` if a new rule file was created

4. **Update .claude/rules/ if needed:**
   - If new knowledge was established this session (new formats, new rules, new patterns), add it to the relevant rule file
   - If no appropriate file exists, create a new numbered one

5. **Report to the user:**
   - List what was updated in MEMORY.md
   - List any new or modified `.claude/rules/` files
   - Show the current line count of MEMORY.md

## Rules

- Write in the same language the user has been using in the conversation
- Be concise — MEMORY.md is working memory, not a novel
- Decision log is append-only
- Never remove pending items that weren't completed — they carry forward
- If nothing significant changed, say so and skip the update
