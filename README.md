# GenPPT MCP Server: AI Slide Decks, Pitch Decks & PowerPoint (.pptx) Export

Generate presentation-ready slide decks, investor pitch decks, consulting slides, and executive briefings from a prompt, outline, notes, or website URL—and export finished presentations to native PowerPoint (`.pptx`), PDF, or high-resolution slide images directly from Claude Code, Cursor, Claude Desktop, VS Code, and Codex.

```bash
claude mcp add --transport http genppt https://genppt.com/mcp --header "Authorization: Bearer gppt_YOUR_KEY"
```

This repository contains the configuration manifests, plugin definitions, and agent skills for the hosted GenPPT Streamable HTTP MCP server at `https://genppt.com/mcp`.

## Get an MCP Key

1. Sign in or create a free account at [genppt.com/app/account](https://genppt.com/app/account).
2. In the **MCP key** section, click **Create key** to generate a `gppt_...` Bearer token.
3. Copy the token and pass it in the `Authorization: Bearer gppt_YOUR_KEY` header.

You can also connect to `https://genppt.com/mcp` without a key to inspect tool definitions or receive a direct browser handoff link (`handoffUrl`) for any deck prompt.

## Setup Instructions

### Claude Code

```bash
claude mcp add --transport http genppt https://genppt.com/mcp --header "Authorization: Bearer gppt_YOUR_KEY"
```

Run `/mcp` inside Claude Code to verify that `genppt` is connected and its 8 tools are available.

### Cursor

Add this to `~/.cursor/mcp.json` (global) or `.cursor/mcp.json` in your project:

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

### Claude Desktop and claude.ai

In Claude Desktop or claude.ai, open **Customize → Connectors → + Add → Add custom connector**:

- **Name:** `GenPPT`
- **URL:** `https://genppt.com/mcp`

Or in `claude_desktop_config.json` / `.mcp.json`:

```json
{
  "mcpServers": {
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

### VS Code

Add this to `.vscode/mcp.json` (VS Code uses `servers`):

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

### OpenAI Codex CLI

Add this to `~/.codex/config.toml`:

```toml
[mcp_servers.genppt]
url = "https://genppt.com/mcp"
http_headers = { "Authorization" = "Bearer gppt_YOUR_KEY" }
```

### Install the Agent Skill

Install the GenPPT agent workflow skill for Claude Code, Cursor, or any skills-compatible coding agent:

```bash
npx skills add Illyism/genppt-mcp
```

Or install via the Claude Code plugin marketplace:

```bash
claude plugin marketplace add Illyism/genppt-mcp
claude plugin install genppt@genppt-mcp
```

## How Deck Generation Works

1. **Create a deck (`genppt_create_deck`):** Pass a `prompt` (topic, outline, rough notes, or pasted document), optional `aspectRatio` (`16:9`, `9:16`, `4:5`, `1:1`), optional `websiteUrl` to ground the deck in a live site, and optional `attachmentUrls` (images, PDFs, or YouTube URLs).
2. **Review the concept preview (`genppt_get_deck`):** Poll every 30–60 seconds until `phase` is `concepts_ready`. The server returns `conceptPreviewUrl` so your chat client can render the visual concept sheet inline.
3. **Render full slides (`genppt_generate_slides`):** Once the concept is approved, trigger full slide rendering (1–3 minutes) and poll `genppt_get_deck` until `phase` is `ready`.
4. **Export (`genppt_export_deck`):** Export the finished presentation to `pptx` (PowerPoint), `pdf` (PDF), or `images` (ZIP of slide images) and receive a direct `downloadUrl`.

## Tools

| Tool | Read-Only | Description |
| --- | --- | --- |
| `genppt_account_status` | Yes | Show the connected GenPPT account: current plan tier, decks used this period, saved payment method, and upgrade options. |
| `genppt_list_decks` | Yes | List the user's GenPPT decks (newest edits first), with optional `limit` (1–100) and title `search`. |
| `genppt_get_deck` | Yes | Get a deck's current `phase` (`drafting_concepts`, `concepts_ready`, `rendering_slides`, `ready`, `error`), concept preview image, slide list, inline slide image URLs, and `studioUrl`. |
| `genppt_create_deck` | No | Start a new AI presentation deck from a `prompt`, optional `aspectRatio` (`16:9`, `9:16`, `4:5`, `1:1`), `websiteUrl`, `attachmentUrls`, and `webSearchEnabled`. |
| `genppt_generate_slides` | No | Render all slides once `genppt_get_deck` reaches phase `concepts_ready` (equivalent to clicking Generate in the GenPPT Studio). |
| `genppt_export_deck` | No | Export a ready deck to `pptx` (PowerPoint), `pdf` (PDF), or `images` (ZIP of PNG slides) and return the `downloadUrl`. |
| `genppt_upgrade_plan` | Yes | Preview the price of a GenPPT plan (`growth_monthly`, `growth_yearly`, `max_yearly`). Never charges a card; returns a browser `checkoutUrl` when `confirmPaymentSpend: true`. |
| `genppt_submit_feedback` | No | Send a bug report, feature request, or question (`bug`, `missing_feature`, `question`, `other`) to the GenPPT team. |

## Links

- **Website:** [https://genppt.com](https://genppt.com)
- **MCP Endpoint:** `https://genppt.com/mcp`
- **MCP Discovery Manifest:** [https://genppt.com/.well-known/mcp](https://genppt.com/.well-known/mcp)
- **MCP Server Card:** [https://genppt.com/.well-known/mcp/server-card.json](https://genppt.com/.well-known/mcp/server-card.json)
- **Skill Guide:** [https://genppt.com/SKILL.md](https://genppt.com/SKILL.md)
- **Account & MCP Keys:** [https://genppt.com/app/account](https://genppt.com/app/account)
- **Support:** [https://genppt.com/contact](https://genppt.com/contact)
- **Privacy Policy:** [https://genppt.com/blog/privacy-policy](https://genppt.com/blog/privacy-policy)
- **Terms of Service:** [https://genppt.com/blog/terms-of-service](https://genppt.com/blog/terms-of-service)
