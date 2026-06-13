# kotvmeshke — Claude Code plugin

Author and manage quiz packs on [kotvmeshke.com](https://kotvmeshke.com) from your
AI assistant. This plugin bundles two things:

- **the `kotvmeshke-quiz-author` skill** — teaches the assistant the pack model,
  question-writing best practices and safe tool workflows;
- **the MCP server connection** — `https://api.kotvmeshke.com/mcp`.

## Install (Claude Code)

1. Create an access token at **https://kotvmeshke.com/settings** → "AI access"
   (any registered account works — the token starts with `qmcp_` and is shown
   once). Make it available to Claude Code as an environment variable, e.g. add
   to your shell profile (`~/.zshrc` / `~/.bashrc`):

   ```bash
   export KOTVMESHKE_MCP_TOKEN=qmcp_your_token_here
   ```

2. Add this marketplace and install the plugin:

   ```
   /plugin marketplace add glotyuids/claude-kotvmeshke-mcp
   /plugin install kotvmeshke-quiz-author@kotvmeshke
   ```

3. Restart Claude Code once. Verify with `/mcp` — `kotvmeshke` and its tools
   should be listed. Then just ask: *"build a quiz about the 90s"*.

The token is a password — keep it out of shared configs and repos. Revoke or
rotate any token at https://kotvmeshke.com/settings.

## Other clients

Cursor, VS Code, Claude Desktop, Codex and any other MCP client connect to the
same server — see **https://kotvmeshke.com/docs/mcp/clients**.

## What it can do

Create whole packs, add rounds / topics / questions, attach media, validate and
publish — all scoped to your own account, all as private drafts until you
explicitly publish. Full docs: https://kotvmeshke.com/docs/mcp
