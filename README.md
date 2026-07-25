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

`server.py` — a **zero-dependency stdio bridge** (Python 3.9+, stdlib only) for
clients that speak MCP over stdio and can't send HTTP headers. It forwards every
JSON-RPC message to the hosted endpoint over TLS and streams responses back:

```bash
export GETAPPNICHE_API_KEY="getappniche_..."
python3 server.py
```

Claude Desktop (`claude_desktop_config.json`):

```json
{
  "mcpServers": {
    "getappniche": {
      "command": "python3",
      "args": ["/absolute/path/to/server.py"],
      "env": { "GETAPPNICHE_API_KEY": "getappniche_..." }
    }
  }
}
```

Being ~150 readable lines, it doubles as a **reference for what travels over the
wire**: your API key goes only into the `Authorization` header of TLS requests
to `api.getappniche.com` — nothing else, nowhere else.

## Tools

| Tool | Purpose | Cost |
|---|---|---|
| `search_apps` | Search & filter apps by store, category, text, ratings, growth | 1 credit |
| `get_app_detail` | One app by `apple:284882215` / `google:com.duolingo` | 1 credit |
| `get_app_historicals` | Metric time-series: reviews, ratings, downloads, revenue | 1 credit |
| `get_keyword_difficulty` | Popularity, difficulty, traffic & opportunity for a keyword | 10 credits |
| `batch_keyword_difficulty` | Up to 10 keywords, auto-sorted by opportunity | 10 credits / kw |
| `get_app_reviews` | Enriched review feed for apps monitored in your workspace | 1 credit |
| `get_supported_countries` | Valid store country codes | Free |

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
