---
name: genppt
description: Generate AI slide decks, pitch decks, and presentations with the GenPPT MCP server and export finished decks to PowerPoint (.pptx), PDF, or slide images. Use when the user asks to create a presentation, pitch deck, or slide deck, turn notes or a website into slides, inspect generated slide previews, or export a deck to PowerPoint or PDF.
---

# Generate presentations with the GenPPT MCP server

GenPPT is a remote Streamable HTTP MCP server at `https://genppt.com/mcp`. It turns a prompt, outline, notes, or source URLs into a slide concept preview, renders every slide as a designed visual, and exports the finished deck to PowerPoint (`.pptx`), PDF, or a ZIP of slide images.

## Step 1. Check that the server is connected

Look for these tools (your client may add a namespace prefix such as `mcp__genppt__genppt_create_deck`):

- `genppt_account_status`
- `genppt_list_decks`
- `genppt_get_deck`
- `genppt_create_deck`
- `genppt_generate_slides`
- `genppt_export_deck`
- `genppt_upgrade_plan`
- `genppt_submit_feedback`

If the server is not connected, tell the user how to add it:

1. Sign in at `https://genppt.com/app/account` and click **Create key** under **MCP key** to get a `gppt_...` Bearer token.
2. Add the remote server:
   - Claude Code: `claude mcp add --transport http genppt https://genppt.com/mcp --header "Authorization: Bearer gppt_YOUR_KEY"`
   - Cursor / Claude Desktop (`mcp.json`):
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

Without a Bearer key, tools return `status: "auth_required"` with a `connectUrl` (`https://genppt.com/app/account`) or a `handoffUrl` that opens the prompt directly in the browser studio.

## Step 2. Follow the deck workflow in order

1. **Check account or existing decks (optional):** Call `genppt_account_status` to check the connected plan and remaining deck allowance, or `genppt_list_decks` to find an existing deck.
2. **Start a new deck:** Call `genppt_create_deck` with:
   - `prompt`: topic, audience, outline, notes, or pasted content.
   - `aspectRatio`: `'16:9'` (default), `'9:16'`, `'4:5'`, or `'1:1'`.
   - `websiteUrl`: optional company or product URL to ground the deck in.
   - `attachmentUrls`: optional public URLs (up to 10) of files, images, or YouTube videos.
   - `webSearchEnabled`: `true` by default so the agent can research live facts.
3. **Poll until concepts are ready:** Call `genppt_get_deck` with `deckId` every 30–60 seconds while `phase` is `drafting_concepts`.
4. **Show the concept preview and render slides:** When `genppt_get_deck` returns `phase: "concepts_ready"`, show the `conceptPreviewUrl` image inline in chat (`![Concept preview](...)`). Once the user approves (or immediately if they asked for a full hands-off deck), call `genppt_generate_slides` with `deckId`.
5. **Poll until slides are ready:** Call `genppt_get_deck` every 30–60 seconds while `phase` is `rendering_slides`. When `phase` reaches `ready`, render the slide images (`slides[].imageUrl`) inline in chat and share the `studioUrl`.
6. **Export to PowerPoint, PDF, or images:** Call `genppt_export_deck` with `deckId` and `format` (`"pptx"`, `"pdf"`, or `"images"`). It waits up to 2 minutes for the export file to build and returns `downloadUrl` and `filename`.

## Step 3. Guardrails for billing and upgrades

- Exporting (`genppt_export_deck`) and higher monthly deck allowances require a paid plan.
- If a tool returns `limit_reached` or `upgrade_required`, call `genppt_upgrade_plan` with `confirmPaymentSpend: false` (default) to preview the price.
- **Never** pass `confirmPaymentSpend: true` to `genppt_upgrade_plan` before showing the price to the user and getting explicit confirmation. Even with `confirmPaymentSpend: true`, the tool never charges a card directly—it returns a `checkoutUrl` for the user to complete in their browser.

## Step 4. Reporting issues

If a deck hits `phase: "error"` or a user wants to report a bug or feature request, call `genppt_submit_feedback` with `category` (`"bug"`, `"missing_feature"`, `"question"`, or `"other"`), `message`, and optional `deckId`.
