# Claude Code for Business - Starter Kit

Your AI Employee infrastructure. Build AI systems that do real work in your business.

## What Is This?

A ready-to-use template for [Claude Code](https://claude.ai/code) that includes:

- **5 Meta-Skills** - Claude knows how to create skills, commands, agents, hooks, and MCP connections
- **Smart Defaults** - Pre-configured settings and folder structure
- **Auto-Update Hook** - CLAUDE.md routing table updates automatically when you create new skills

## Quick Start

### 1. Use This Template

Click the green **"Use this template"** button above to create your own copy.

### 2. Install VS Code + Claude Code Extension

1. Install [VS Code](https://code.visualstudio.com/) if you don't have it
2. Install the **Claude Code extension**:
   - Open VS Code
   - Press `Ctrl+Shift+X` (Windows) or `Cmd+Shift+X` (Mac)
   - Search for **"Claude Code"**
   - Click **Install** on the official Anthropic extension
   - Reload VS Code when prompted

### 3. Clone Your New Repo

- Press `Ctrl+Shift+P` (Windows) or `Cmd+Shift+P` (Mac)
- Type **"Git: Clone"**
- Paste your new repo URL
- Choose a folder to save it
- Click **"Open"** when prompted

> **Important:** When VS Code asks "Would you like to open the cloned repository?" - click **Yes**. You must open the cloned folder directly, not a parent folder. If Claude can't find your skills or MCPs, this is usually why.

### 4. Start Building

Open the Claude Code panel in VS Code and try:

```
"Create a skill for researching YouTube competitors"
"Create a command to analyze my content performance"
"Create an agent that reviews my writing"
```

## What's Included

```
.
├── CLAUDE.md                    # Project memory (Claude reads this first)
├── .mcp.json.example            # Template for MCP connections
├── .gitignore                   # Protects your API keys
│
└── .claude/
    ├── settings.json            # Permissions + auto-update hook
    └── skills/
        ├── create-skill/        # Teaches Claude to create skills
        ├── create-command/      # Teaches Claude to create commands
        ├── create-agent/        # Teaches Claude to create agents
        ├── create-hook/         # Teaches Claude to create hooks
        └── create-mcp/          # Teaches Claude to connect external tools
```

## Connecting External Tools (MCP)

Want to connect YouTube, Notion, or other services? Just ask Claude:

```
"Connect YouTube MCP"
"Add Notion integration"
```

Claude will guide you through the setup, including installing any dependencies (like Node.js) if needed.

## Requirements

- [VS Code](https://code.visualstudio.com/)
- [Claude Code Extension](https://marketplace.visualstudio.com/items?itemName=anthropic.claude-code) (requires Claude Pro, Max, or Teams subscription)

## Learn More

This starter kit was built for the **Claude Code for Business** YouTube series.

- [Watch the Tutorial](https://youtube.com/@chiefleverageofficer)
- [Claude Code VS Code Docs](https://code.claude.com/docs/en/vs-code)

## License

MIT - Use it however you want.

---

Built by [Rashid Khamis](https://the2hourclo.com) | Chief Leverage Officer
