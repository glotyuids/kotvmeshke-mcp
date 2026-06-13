# kotvmeshke MCP

Everything you need to author quiz packs on [kotvmeshke.com](https://kotvmeshke.com)
from an AI assistant, in one place:

- **MCP server** — `https://api.kotvmeshke.com/mcp` (streamable HTTP). Works with
  any MCP client.
- **[Claude Code plugin](plugins/kotvmeshke-quiz-author)** — installs the server
  **and** an authoring skill in one command (auto-updating).
- **[configs/](configs)** — ready-to-paste connection config for every popular
  client (Cursor, VS Code, Windsurf, Cline, Zed, Codex, Claude Desktop, Gemini).
- **[prompts/](prompts)** — example prompts to drive the assistant.

Full docs: **https://kotvmeshke.com/docs/mcp**

## 1. Get a token

Any registered kotvmeshke account can author packs. Create a token at
**https://kotvmeshke.com/settings** → "AI access" (starts with `qmcp_`, shown
once). Treat it like a password.

## 2a. Claude Code — plugin (easiest)

```bash
export KOTVMESHKE_MCP_TOKEN=qmcp_your_token   # add to ~/.zshrc or ~/.bashrc
```
```
/plugin marketplace add glotyuids/kotvmeshke-mcp
/plugin install kotvmeshke-quiz-author@kotvmeshke
```
Restart Claude Code once, verify with `/mcp`, then: *"build a quiz about the 90s"*.

## 2b. Any other client

See **[configs/](configs)** for a ready config per tool, or the settings page on
the site for one-click "Add to …" buttons (Cursor / VS Code) with your token
pre-filled. The server endpoint is `https://api.kotvmeshke.com/mcp` with header
`Authorization: Bearer qmcp_…`.

## What the assistant can do

Create whole packs, add rounds / topics / questions, attach media, validate and
publish — scoped to your own account, all private drafts until you explicitly
publish. The bundled skill (see [plugins/kotvmeshke-quiz-author](plugins/kotvmeshke-quiz-author))
teaches the pack model and safe workflows.

---

*Mirrors the docs at kotvmeshke.com/docs/mcp. The skill is the canonical source
here; the website and the published `.skill` are generated from it.*
