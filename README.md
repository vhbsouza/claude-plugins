# Claude Plugins

> A Plugin marketplace for Claude Code and Cowork — a curated collection of tools covering productivity, engineering, memory, and more.

## Usage

**Add this marketplace to Claude Code:**

```
/plugin marketplace add vhbsouza/claude-plugins
```

**Install a plugin:**

```
/plugin install <plugin-name>@claude-plugins
```

---

## Plugins

### 🧠 session-memory

Structured memory system for Claude Code and Cowork projects. Scaffolds a five-layer memory architecture to maintain context across sessions.

**Layers:**
- `CLAUDE.md` — index and high-level project context
- `MEMORY.md` — working memory for the current session
- `.claude/rules/` — long-term knowledge and project rules
- `snapshots/` — versioned memory checkpoints
- `docs/` — reference documentation

**Includes:** commands (`/setup-memory`, `/update-memory`, `/snapshot`, `/status`), skills, and lifecycle hooks.

```
/plugin install session-memory@claude-plugins
```

---

## Author

**Victor Souza** — [github.com/vhbsouza](https://github.com/vhbsouza)

## License

MIT
