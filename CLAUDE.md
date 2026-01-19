# Claude Code for Business - Starter Kit

Your AI Employee infrastructure. Build AI systems that do real work in your business.

---

## IMPORTANT: Working Directory

**You MUST open THIS folder directly in VS Code.**

If you cloned this repo into a subfolder, make sure VS Code opens the folder containing this `CLAUDE.md` file at the root - NOT a parent folder.

**Correct:** VS Code opens `claude-code-business-starter-kit/`
**Wrong:** VS Code opens a parent folder that contains `claude-code-business-starter-kit/`

If skills, MCPs, or agents aren't working, this is usually why.

---

## Routing

| When you want to... | Load skill |
|---------------------|------------|
| Create a new skill | `create-skill` |
| Create a new command | `create-command` |
| Create a new agent | `create-agent` |
| Create a new hook | `create-hook` |
| Add or build an MCP | `create-mcp` |
| Search online for MCP servers | `mcp-finder` agent (spawned by create-mcp) |

---

## Output Organization

**Principle:** Never dump files in root. Outputs go in logical folders.

| Output Type | Location | Example |
|-------------|----------|---------|
| Content | `content/[type]/[project]/` | `content/youtube/competitor-analysis/` |
| Research | `research/[topic]/` | `research/ai-tools/` |
| Data | `data/[source]/` | `data/youtube-api/` |
| Exports | `exports/[format]/` | `exports/csv/` |

**When creating skills that produce output:**
1. Determine what TYPE of output (content, research, data, export)
2. Create the appropriate folder structure
3. Use descriptive project/topic names
4. Keep related files together

---

## Folder Structure

```
project/
├── CLAUDE.md              # Project memory + rules (this file)
├── .mcp.json              # MCP server connections
│
├── .claude/
│   ├── settings.json      # Permissions and hooks
│   ├── skills/            # Your skills
│   ├── commands/          # Your slash commands
│   └── agents/            # Your subagents
│
├── content/               # Content outputs (videos, posts, etc.)
├── research/              # Research outputs
├── data/                  # Data exports and raw data
└── exports/               # Formatted exports
```

**Rules live in CLAUDE.md** - this file. Add project-wide guidelines here.

---

## MCP Servers

This project is configured to use:
- **YouTube MCP** - For video research and analytics
- **Notion MCP** - For saving insights and data

See `.mcp.json` for configuration.

---

## Getting Started

1. Open this folder in your terminal
2. Run `claude` to start Claude Code
3. Say "create a skill for [something]" to build your first skill

---

**Built for:** Claude Code for Business YouTube Series
