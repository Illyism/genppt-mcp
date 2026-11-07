# GenPPT MCP Server — Automated Installation Guide

This guide is written for AI coding agents (Cline, Cursor, Claude Code, Codex, Windsurf, etc.) to install and configure the GenPPT MCP server automatically. Follow the steps in order.

## What this server does

GenPPT (`https://genppt.com/mcp`) is a remote Streamable HTTP MCP server that generates AI presentation decks, investor pitch decks, and slide decks from a prompt, outline, notes, or website URL, and exports finished presentations to PowerPoint (`.pptx`), PDF, or slide images.

## Prerequisites

No local packages or runtime dependencies are required. The server runs over Streamable HTTP at `https://genppt.com/mcp`.

To create and export decks in chat, the user can create a `gppt_...` API key at `https://genppt.com/app/account` (under **MCP key → Create key**) and pass it as `Authorization: Bearer gppt_YOUR_KEY`. The server also connects without a key (returning `status: "auth_required"` with a browser `handoffUrl` and `connectUrl`).

## Installation

Add the remote HTTP server to the client's MCP configuration:

### Cline / Cursor / Claude Desktop (`mcpServers` JSON)

```json
{
  "mcpServers": {
    "genppt": {
      "url": "https://genppt.com/mcp",
      "headers": {
        "Authorization": "Bearer gppt_YOUR_KEY"
      }
    }
  }
}
```

*(If the user does not have a `gppt_...` key yet, you may omit `"headers"` initially so the server connects and provides the account connection link when called.)*

### Claude Code CLI

```bash
claude mcp add --transport http genppt https://genppt.com/mcp --header "Authorization: Bearer gppt_YOUR_KEY"
```

### VS Code (`.vscode/mcp.json`)

```json
{
  "servers": {
    "genppt": {
      "type": "http",
      "url": "https://genppt.com/mcp",
      "headers": {
        "Authorization": "Bearer gppt_YOUR_KEY"
      }
    }
  }
}
```

## Verify the installation

Call `genppt_account_status` with `{}`. A working install returns either the connected GenPPT account plan and deck allowance (`status: "connected"`) or the account connection URL (`status: "auth_required"`, `connectUrl: "https://genppt.com/app/account"`).
