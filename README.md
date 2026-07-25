# GetAppNiche MCP — App Store & Google Play intelligence for AI agents

The [Model Context Protocol](https://modelcontextprotocol.io) surface of
[GetAppNiche](https://getappniche.com): live App Store & Google Play data —
app discovery with revenue & download data, ASO keyword scoring, historical
metrics and review feeds — for Claude, Cursor, Codex, Gemini CLI and any other
MCP-capable agent.

**You probably don't need to run anything.** The server is hosted:

```
https://api.getappniche.com/mcp        Streamable HTTP · JSON-RPC 2.0
Authorization: Bearer YOUR_API_KEY     app.getappniche.com → Settings → API Keys
```

One-line connect for Claude Code:

```bash
claude mcp add --transport http getappniche https://api.getappniche.com/mcp \
  --header "Authorization: Bearer YOUR_API_KEY"
```

Copy-paste setup for **Cursor, Claude Desktop, Windsurf, VS Code, Codex CLI,
Gemini CLI, Zed, Cline, Continue, JetBrains AI** and more:
[getappniche.com/mcp](https://getappniche.com/mcp). Companion agent skills:
[`getappniche/aso-skills`](https://github.com/getappniche/aso-skills)
(`npx skills add getappniche/aso-skills`).

## What's in this repo

A **zero-dependency stdio bridge** for clients that speak MCP over stdio and
can't send HTTP headers — in two flavours: `bin/cli.mjs` (Node 18+, published as
[`@getappniche/mcp`](https://www.npmjs.com/package/@getappniche/mcp)) and
`server.py` (Python 3.9+, stdlib only). Both forward every JSON-RPC message to
the hosted endpoint over TLS and stream responses back.

```bash
# Node — one-liner, no install
GETAPPNICHE_API_KEY="getappniche_..." npx -y @getappniche/mcp

# Python — no dependencies
GETAPPNICHE_API_KEY="getappniche_..." python3 server.py
```

Claude Desktop (`claude_desktop_config.json`):

```json
{
  "mcpServers": {
    "getappniche": {
      "command": "npx",
      "args": ["-y", "@getappniche/mcp"],
      "env": { "GETAPPNICHE_API_KEY": "getappniche_..." }
    }
  }
}
```

`server.json` is the manifest published to the
[official MCP registry](https://registry.modelcontextprotocol.io) as
`com.getappniche/mcp`.

Being ~150 readable lines, it doubles as a **reference for what travels over the
wire**: your API key goes only into the `Authorization` header of TLS requests
to `api.getappniche.com` — nothing else, nowhere else.

## Tools

- `search_apps` - Search and filter apps by store, category, text, ratings and growth (1 credit)
- `get_app_detail` - Get one app by id, e.g. `apple:284882215` or `google:com.duolingo` (1 credit)
- `get_app_historicals` - Metric time series: reviews, ratings, downloads, revenue (1 credit)
- `get_keyword_difficulty` - Popularity, difficulty, traffic and opportunity for a keyword (10 credits)
- `batch_keyword_difficulty` - Score up to 10 keywords, sorted by opportunity (10 credits per keyword)
- `get_app_reviews` - Enriched review feed for apps monitored in your workspace (1 credit)
- `get_supported_countries` - List valid store country codes (free)

Credits ship with a GetAppNiche plan (Pro: 5,000/month) and every result reports
`credits_charged`. Rate limit: 60 requests/minute per key.

## Protocol notes

- Methods: `initialize` → `notifications/initialized` → `tools/list` /
  `tools/call`. Protocol errors use standard JSON-RPC codes; tool-level errors
  (bad id, out of credits) come back as tool results your agent can read.
- The bridge honours `Mcp-Session-Id` and accepts both JSON and SSE-framed
  responses.
- OAuth for clients whose connector UI can't set headers (ChatGPT, claude.ai
  web) is in progress.

## License

MIT. Built by [GetAppNiche](https://getappniche.com) ·
X [@getappniche](https://x.com/getappniche) ·
[LinkedIn](https://www.linkedin.com/company/getappniche)
