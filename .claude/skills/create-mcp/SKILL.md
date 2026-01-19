---
name: create-mcp
description: Add, configure, or build MCP servers in Claude Code. USE WHEN user says 'add an MCP', 'connect to', 'add integration', 'setup MCP', 'connect MCP server', 'add notion', 'add youtube', 'configure MCP', 'build an MCP', 'create MCP server'.
---

# Create MCP

Teaches Claude how to add existing MCP servers OR build custom ones from scratch.

---

## Two Paths

| Path | When to Use | Complexity |
|------|-------------|------------|
| **Connect Existing** | Using a published MCP (YouTube, Notion, GitHub) | Easy (5 min) |
| **Build Custom** | Creating your own MCP server for a custom API | Medium (1-2 hours) |

Ask the user: **"Are you connecting to an existing MCP or building a custom one?"**

---

# PATH 1: Connect Existing MCP

## The 6-Phase Guided Flow

### Phase 1: DISCOVER

**Your job: Identify what the user needs. The mcp-finder agent searches online.**

Ask: "What do you want Claude to be able to do?"

Examples of user needs:
- "Search YouTube videos and get transcripts"
- "Create and update Notion pages"
- "Manage GitHub issues and PRs"

Once you understand the need, **spawn the `mcp-finder` agent** to search online (NPM, GitHub, MCP directories) for the best MCP servers.

The mcp-finder agent will:
1. Search multiple online sources
2. Evaluate options based on functionality, maintenance, docs
3. Return top 2-3 recommendations with comparison

Present the mcp-finder's recommendations to the user.

---

### Phase 2: VERIFY TOOLS

Before installing, show the user what tools the MCP provides.

Example:
```
The YouTube MCP provides these tools:
- videos_searchVideos - Search for videos
- videos_getVideo - Get video details
- transcripts_getTranscript - Get video transcript
- channels_getChannel - Get channel info
- channels_listVideos - List channel videos

Does this cover what you need?
```

Get user confirmation before proceeding.

---

### Phase 3: GET API KEY

Provide step-by-step instructions for getting the API key.

**Common Services:**

| Service | Where to Get Key |
|---------|------------------|
| YouTube | [Google Cloud Console](https://console.cloud.google.com/apis/credentials) → Create credentials → API Key → Enable YouTube Data API v3 |
| Notion | [Notion Integrations](https://www.notion.so/my-integrations) → New integration → Copy Internal Integration Token |
| GitHub | [GitHub Settings](https://github.com/settings/tokens) → Personal access tokens → Generate new token |

Walk the user through the specific steps for their service.

---

### Phase 4: CONFIGURE

**API keys go directly in `.mcp.json`** - no .env file needed since .mcp.json is already gitignored.

Create or update `.mcp.json` in the project root:

```json
{
  "mcpServers": {
    "youtube": {
      "type": "stdio",
      "command": "npx",
      "args": ["-y", "@kirbah/mcp-youtube"],
      "env": {
        "YOUTUBE_API_KEY": "AIzaSy...actual-key-here"
      }
    }
  }
}
```

**Windows Note:** NPX may require cmd wrapper:
```json
{
  "youtube": {
    "type": "stdio",
    "command": "cmd",
    "args": ["/c", "npx", "-y", "@kirbah/mcp-youtube"],
    "env": {
      "YOUTUBE_API_KEY": "AIzaSy...actual-key-here"
    }
  }
}
```

**Tell user:** "Reload VS Code (Ctrl+Shift+P → 'Reload Window') to connect the MCP."

---

### Phase 5: PERMISSIONS

Ask: "Do you want Claude to use this MCP's tools without asking permission each time?"

**If yes**, update `.claude/settings.json`:

```json
{
  "permissions": {
    "allow": [
      "mcp__youtube__*"
    ]
  }
}
```

This auto-approves all tools from that MCP.

---

### Phase 6: TEST

1. **Verify connection:**
   ```bash
   claude mcp list
   ```
   Should show the MCP as connected.

2. **Test a tool:**
   Ask Claude to use one of the MCP's tools:
   - "Search YouTube for videos about Claude Code"
   - "Get my Notion databases"

3. **Troubleshoot if needed:**
   - If not connected, check `.mcp.json` syntax
   - If tools fail, verify API key is correct
   - Check logs: `~/Library/Logs/Claude/mcp*.log` (Mac)

---

## .mcp.json Structure

```json
{
  "mcpServers": {
    "server-name": {
      "type": "http|sse|stdio",
      "url": "https://...",
      "command": "node|npx|python|uv",
      "args": ["arg1", "arg2"],
      "env": {
        "VAR_NAME": "value"
      },
      "headers": {
        "Authorization": "Bearer token"
      }
    }
  }
}
```

### Supported Fields

| Field | Type | Required | Transport | Purpose |
|-------|------|----------|-----------|---------|
| `type` | string | Yes | All | `http`, `sse`, or `stdio` |
| `url` | string | Yes* | HTTP/SSE | Remote server endpoint |
| `command` | string | Yes* | Stdio | Executable to run |
| `args` | array | No | Stdio | Command-line arguments |
| `env` | object | No | Stdio | Environment variables |
| `headers` | object | No | HTTP/SSE | HTTP headers (auth, etc.) |

---

## Transport Types

### HTTP (Remote Servers)
For cloud-hosted MCP servers:

```json
{
  "notion": {
    "type": "http",
    "url": "https://mcp.notion.com/mcp",
    "headers": {
      "Authorization": "Bearer ntn_...your-token"
    }
  }
}
```

### Stdio (Local/NPM Packages)
For npm packages or local scripts:

```json
{
  "youtube": {
    "type": "stdio",
    "command": "npx",
    "args": ["-y", "@kirbah/mcp-youtube"],
    "env": {
      "YOUTUBE_API_KEY": "AIzaSy...your-key"
    }
  }
}
```

---

## Common MCP Servers

### YouTube
```json
{
  "youtube": {
    "type": "stdio",
    "command": "npx",
    "args": ["-y", "@kirbah/mcp-youtube"],
    "env": {
      "YOUTUBE_API_KEY": "your-api-key"
    }
  }
}
```

### Notion
```json
{
  "notion": {
    "type": "stdio",
    "command": "npx",
    "args": ["-y", "@notionhq/notion-mcp-server"],
    "env": {
      "NOTION_TOKEN": "your-integration-token"
    }
  }
}
```

### GitHub
```bash
claude mcp add --transport http github https://api.githubcopilot.com/mcp/
```

### Filesystem
```bash
claude mcp add --transport stdio files -- npx -y @modelcontextprotocol/server-filesystem /path/to/dir
```

---

## Scopes

| Scope | Storage | Visibility | Use Case |
|-------|---------|------------|----------|
| **user** | `~/.claude.json` | All your projects | Personal tools |
| **project** | `.mcp.json` (git) | Team via version control | Shared team tools |
| **local** | `~/.claude.json` (project path) | Only you, this project | Private/sensitive |

**Precedence:** Local > Project > User

---

# PATH 2: Build Custom MCP Server

## When to Build Custom

- API doesn't have an existing MCP server
- You need specific tools for your business
- You want to wrap internal services

---

## The 4-Phase Process

### Phase 1: DEFINE
Ask the user:
- "What API or service do you want to connect?"
- "What tools should Claude have access to?" (e.g., search, create, update)

### Phase 2: SETUP
Choose language and create project structure

### Phase 3: BUILD
Implement the MCP server with tools

### Phase 4: CONNECT
Add to .mcp.json and test

---

## Quick Start: Python (Recommended)

### Setup

```bash
# Install uv (fast Python package manager)
curl -LsSf https://astral.sh/uv/install.sh | sh

# Create project
uv init my-mcp-server
cd my-mcp-server
uv venv
source .venv/bin/activate  # Windows: .venv\Scripts\activate

# Install MCP SDK
uv add "mcp[cli]" httpx
```

### Minimal Server (server.py)

```python
from mcp.server.fastmcp import FastMCP

# Create server
mcp = FastMCP("my-server")

@mcp.tool()
async def my_tool(param: str) -> str:
    """Description of what this tool does.

    Args:
        param: Description of this parameter
    """
    # Your implementation here
    return f"Result for {param}"

def main():
    mcp.run(transport="stdio")

if __name__ == "__main__":
    main()
```

### Run

```bash
uv run server.py
```

---

## Quick Start: TypeScript

### Setup

```bash
mkdir my-mcp-server
cd my-mcp-server
npm init -y
npm install @modelcontextprotocol/sdk zod
npm install -D typescript @types/node

mkdir src
```

### package.json

```json
{
  "type": "module",
  "scripts": {
    "build": "tsc",
    "start": "node dist/index.js"
  }
}
```

### Minimal Server (src/index.ts)

```typescript
import { McpServer } from "@modelcontextprotocol/sdk/server/mcp.js";
import { StdioServerTransport } from "@modelcontextprotocol/sdk/server/stdio.js";
import { z } from "zod";

const server = new McpServer({
  name: "my-server",
  version: "1.0.0",
});

// Register a tool
server.registerTool(
  "my_tool",
  {
    description: "Description of what this tool does",
    inputSchema: {
      type: "object",
      properties: {
        param: {
          type: "string",
          description: "Description of this parameter"
        }
      },
      required: ["param"]
    }
  },
  async ({ param }) => {
    // Your implementation here
    return {
      content: [{
        type: "text",
        text: `Result for ${param}`
      }]
    };
  }
);

async function main() {
  const transport = new StdioServerTransport();
  await server.connect(transport);
  console.error("Server running on stdio");
}

main().catch(console.error);
```

### Build and Run

```bash
npm run build
npm start
```

---

## Adding Tools

Tools are functions Claude can call. Each tool needs:
- **Name**: What Claude uses to call it
- **Description**: Helps Claude know when to use it
- **Parameters**: What inputs it accepts
- **Implementation**: What it actually does

### Python Example

```python
@mcp.tool()
async def search_products(query: str, limit: int = 10) -> str:
    """Search for products in the catalog.

    Args:
        query: Search terms
        limit: Maximum results to return (default 10)
    """
    # Call your API
    results = await api.search(query, limit)
    return json.dumps(results)

@mcp.tool()
async def get_order(order_id: str) -> str:
    """Get details for a specific order.

    Args:
        order_id: The order ID to look up
    """
    order = await api.get_order(order_id)
    return json.dumps(order)
```

### TypeScript Example

```typescript
server.registerTool(
  "search_products",
  {
    description: "Search for products in the catalog",
    inputSchema: {
      type: "object",
      properties: {
        query: { type: "string", description: "Search terms" },
        limit: { type: "number", description: "Max results", default: 10 }
      },
      required: ["query"]
    }
  },
  async ({ query, limit = 10 }) => {
    const results = await api.search(query, limit);
    return {
      content: [{ type: "text", text: JSON.stringify(results) }]
    };
  }
);
```

---

## Connect Custom MCP to Claude Code

### Add to .mcp.json

**Python:**
```json
{
  "mcpServers": {
    "my-server": {
      "type": "stdio",
      "command": "uv",
      "args": ["--directory", "/absolute/path/to/my-mcp-server", "run", "server.py"],
      "env": {
        "API_KEY": "your-api-key"
      }
    }
  }
}
```

**TypeScript:**
```json
{
  "mcpServers": {
    "my-server": {
      "type": "stdio",
      "command": "node",
      "args": ["/absolute/path/to/my-mcp-server/dist/index.js"],
      "env": {
        "API_KEY": "your-api-key"
      }
    }
  }
}
```

**Important:** Use ABSOLUTE paths, not relative.

---

## Critical: Logging Rules

**For stdio servers, NEVER write to stdout!**

```python
# BAD - breaks JSON-RPC
print("Processing...")

# GOOD - use stderr or logging
import logging
logging.info("Processing...")
```

```typescript
// BAD
console.log("Started");

// GOOD
console.error("Started");  // stderr is safe
```

---

## Testing Your MCP

1. Run `claude mcp list` - verify server shows as connected
2. Ask Claude to use a tool: "Search for products about X"
3. Check logs if issues:
   ```bash
   # Mac
   tail -f ~/Library/Logs/Claude/mcp*.log
   ```

---

## MCP Server Structure Summary

```
my-mcp-server/
├── server.py          # Main server (Python)
├── src/
│   └── index.ts       # Main server (TypeScript)
├── package.json       # (TypeScript)
├── pyproject.toml     # (Python)
└── README.md          # How to use
```

---

## Troubleshooting

| Problem | Cause | Solution |
|---------|-------|----------|
| Server not connected | Invalid path | Use absolute paths |
| No tools available | Server crashed | Check stderr/logs |
| Tool returns error | Implementation bug | Debug your code |
| MCP not loading | Wrong working directory | Open project folder directly in VS Code |

---

## Creation Checklist

### Connecting Existing MCP:
- [ ] Spawned mcp-finder to find best option
- [ ] Verified tools match user needs
- [ ] Got API key with step-by-step guidance
- [ ] Added to .mcp.json with key directly in config
- [ ] Windows: npx uses `cmd /c` wrapper if needed
- [ ] User reloaded VS Code
- [ ] Set up permissions in settings.json (optional)
- [ ] Tested with `claude mcp list`
- [ ] Tested a tool to confirm working

### Building Custom MCP:
- [ ] Tools have clear descriptions
- [ ] Parameters documented
- [ ] No stdout writes (stdio transport)
- [ ] Absolute paths in .mcp.json
- [ ] Error handling implemented
- [ ] Tested each tool

---

**Connecting:** `.mcp.json` + 6-phase guided flow
**Building:** Python (`mcp[cli]`) or TypeScript (`@modelcontextprotocol/sdk`)
**Testing:** `claude mcp list` + try tools in chat
