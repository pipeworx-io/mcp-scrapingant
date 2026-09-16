# mcp-scrapingant

ScrapingAnt MCP — wraps the ScrapingAnt web scraping API (scrapingant.com)

Part of [Pipeworx](https://pipeworx.io) — an MCP gateway connecting AI agents to 1576+ live data sources.

## Tools

| Tool | Description |
|------|-------------|
| `scrapingant_scrape` | Scrape a web page and return its raw HTML via ScrapingAnt (headless-browser rendering + rotating proxies). Handles JS-heavy sites and anti-bot pages. Example: scrapingant_scrape({ url: "https://example.com", browser: true, _apiKey: "your-key" }) |
| `scrapingant_markdown` | Fetch a web page and return clean, LLM-ready Markdown (boilerplate stripped) via ScrapingAnt. Ideal for feeding page content to a model. Example: scrapingant_markdown({ url: "https://example.com/article", _apiKey: "your-key" }) |
| `scrapingant_extract` | Extract structured data from a web page using ScrapingAnt AI extraction. Describe the fields you want in `extract_properties` (comma-separated) and get back a JSON object with matching camelCase keys. Example: scrapingant_extract({ url: "https://example.com/product", extract_properties: "product title, price(number), full description", _apiKey: "your-key" }) |

## Quick Start

Add to your MCP client (Claude Desktop, Cursor, Windsurf, etc.):

```json
{
  "mcpServers": {
    "scrapingant": {
      "url": "https://gateway.pipeworx.io/scrapingant/mcp"
    }
  }
}
```

### What this endpoint actually serves

`tools/list` at `https://gateway.pipeworx.io/scrapingant/mcp` returns the tools in the table
above **plus the shared Pipeworx meta-tools** — `ask_pipeworx`,
`discover_tools`, `search_within`, `remember`/`recall` and the rest of the
gateway-wide set. So the tool count you see is larger than this table: a
single-pack endpoint currently lists roughly 30 shared tools alongside the
pack's own. The connection's `initialize` response states its exact scope, and
is the authoritative answer for a given day.

This is deliberate, not multiplexing by accident. The meta-tools are what let a
scoped connection answer a question this pack does not cover — via
`ask_pipeworx`, which routes across the whole catalog — without you adding a
second MCP server. There is currently no way to mount a pack endpoint without
them; if the extra schemas cost you more context than the routing is worth,
connect to the full gateway once rather than to several pack endpoints.

Or connect to the full Pipeworx gateway to get every pack's tools listed
directly, instead of just this one's:

```json
{
  "mcpServers": {
    "pipeworx": {
      "url": "https://gateway.pipeworx.io/mcp"
    }
  }
}
```

Both URLs reach the same gateway and the same 1576+ data sources. The
only difference is which pack's tools are listed **directly**; `ask_pipeworx`
reaches all of them from either one.

## Standalone (no gateway account)

This package also runs as a local stdio MCP server — no Pipeworx account, no
gateway round-trip:

```json
{
  "mcpServers": {
    "scrapingant": {
      "command": "npx",
      "args": ["-y", "@pipeworx/mcp-scrapingant"]
    }
  }
}
```

Or run it directly to confirm it starts:

```bash
npx -y @pipeworx/mcp-scrapingant
```

It speaks MCP over stdin/stdout and answers `initialize`/`tools/list`/`tools/call`
for **only** this pack's tools — none of the shared meta-tools the gateway
connection above adds. Same source, same tools, no ask_pipeworx routing.

## Using with ask_pipeworx

Instead of calling tools directly, you can ask questions in plain English —
this works on the pack endpoint above as well as on the full gateway:

```
ask_pipeworx({ question: "your question about Scrapingant data" })
```

The gateway picks the right tool and fills the arguments automatically.

## More

- [Docs and guides](https://pipeworx.io/docs)
- [pipeworx.io](https://pipeworx.io)

## License

MIT
