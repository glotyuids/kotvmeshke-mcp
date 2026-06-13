# Per-client connection config

The kotvmeshke MCP server is `https://api.kotvmeshke.com/mcp` (streamable HTTP),
authenticated with `Authorization: Bearer qmcp_…`. Replace `qmcp_YOUR_TOKEN`
with a token from https://kotvmeshke.com/settings → "AI access".

Mirrors https://kotvmeshke.com/docs/mcp/clients (which also has one-click
"Add to …" buttons for Cursor and VS Code with the token pre-filled).

## Claude Code

Prefer the [plugin](../plugins/kotvmeshke-quiz-author) (server + skill). Manual:

```bash
claude mcp add --transport http kotvmeshke https://api.kotvmeshke.com/mcp \
  --header "Authorization: Bearer qmcp_YOUR_TOKEN"
```

## Cursor — `~/.cursor/mcp.json`

```json
{
  "mcpServers": {
    "kotvmeshke": {
      "url": "https://api.kotvmeshke.com/mcp",
      "headers": { "Authorization": "Bearer qmcp_YOUR_TOKEN" }
    }
  }
}
```

## VS Code (Copilot) — `.vscode/mcp.json`

```json
{
  "servers": {
    "kotvmeshke": {
      "type": "http",
      "url": "https://api.kotvmeshke.com/mcp",
      "headers": { "Authorization": "Bearer qmcp_YOUR_TOKEN" }
    }
  }
}
```

## Windsurf (Codeium) — `~/.codeium/windsurf/mcp_config.json`

```json
{
  "mcpServers": {
    "kotvmeshke": {
      "serverUrl": "https://api.kotvmeshke.com/mcp",
      "headers": { "Authorization": "Bearer qmcp_YOUR_TOKEN" }
    }
  }
}
```

## Cline — `cline_mcp_settings.json`

```json
{
  "mcpServers": {
    "kotvmeshke": {
      "type": "streamableHttp",
      "url": "https://api.kotvmeshke.com/mcp",
      "headers": { "Authorization": "Bearer qmcp_YOUR_TOKEN" }
    }
  }
}
```

## Zed — `~/.config/zed/settings.json`

```json
{
  "context_servers": {
    "kotvmeshke": {
      "url": "https://api.kotvmeshke.com/mcp",
      "headers": { "Authorization": "Bearer qmcp_YOUR_TOKEN" }
    }
  }
}
```

## Codex — `~/.codex/config.toml`

```toml
[mcp_servers.kotvmeshke]
type = "http"
url = "https://api.kotvmeshke.com/mcp"
bearer_token_env_var = "KOTVMESHKE_MCP_TOKEN"
```
```bash
export KOTVMESHKE_MCP_TOKEN=qmcp_YOUR_TOKEN
```

## Claude Desktop — `claude_desktop_config.json` (via mcp-remote, needs Node.js)

```json
{
  "mcpServers": {
    "kotvmeshke": {
      "command": "npx",
      "args": ["-y", "mcp-remote", "https://api.kotvmeshke.com/mcp",
               "--header", "Authorization: Bearer qmcp_YOUR_TOKEN"]
    }
  }
}
```

## Gemini CLI — `~/.gemini/settings.json` (via mcp-remote, needs Node.js)

```json
{
  "mcpServers": {
    "kotvmeshke": {
      "command": "npx",
      "args": ["-y", "mcp-remote", "https://api.kotvmeshke.com/mcp",
               "--header", "Authorization: Bearer qmcp_YOUR_TOKEN"]
    }
  }
}
```
