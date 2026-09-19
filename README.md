# @pipeworx/cornell-wex

Cornell LII's Wex legal encyclopedia: plain-English definitions of 5,600+ US
legal terms and doctrines, with each entry's related terms, topic categories
and last-reviewed date, read live from law.cornell.edu.

Part of [Pipeworx](https://pipeworx.io) — an MCP gateway connecting AI agents to 1576+ live data sources.

## Tools

- `wex_define(term)` — the definition of one term ("tort", "adverse
  possession", "res judicata"): definition text, related Wex terms, categories,
  keywords, last-reviewed date and the entry URL. A miss returns
  `found:false, reason:"no_such_term"` with suggestions from the same letter
  of the index.
- `wex_search(query, limit?)` — entries whose titles contain the query words,
  ranked by how many words match. Looks under the first letter of each query
  word (up to three index pages, fetched in parallel).
- `wex_browse(letter, offset?, limit?)` — the entries under one letter of the
  A–Z index, with the per-letter counts for the whole index.

## Auth

Keyless.

## Licence — read this before changing the pack

LII's terms (<https://www.law.cornell.edu/lii/terms/documentation>) place all
content LII creates, which Wex is, under **Creative Commons BY-NC-SA 2.5**:
*"you may copy, distribute, and display this work, or make derivative works,
so long as a) you give the LII credit; b) you do not use this work for
commercial purposes; and c) you distribute any works derived from this one
under the same licensing terms."* So:

- the gateway entry is `zeroRated: true` — 0 credits on every tier, no
  volume-bracket slot — because NC forbids charging for it;
- every response leads with `attribution`, `license` (id `CC-BY-NC-SA-2.5`,
  url, obligations) and `license_note`, via `attachLicense` in
  `@pipeworx/shared`, because BY means the credit must be shown and SA binds
  the caller downstream too;
- each call is one live fetch of the entry or index page; nothing is kept
  beyond the gateway's ordinary result cache (one day for this pack).

Bruce ruled this in decision #1973 (2026-09-14): *"it's not commercial
redistribution, it's just redistribution, for free."* Fleet #1974.

## Data sources

- <https://www.law.cornell.edu/wex/{slug}> — one entry. The slug is the term
  lowercased with spaces as underscores (`adverse_possession`); a few carry
  dots or slashes (`a.k.a.`, `a/r`). The article body sits in
  `#main-content` after the `<h1>`; the trailing `[Last reviewed in … by the
  Wex Definitions Team]` paragraph is split out as `last_reviewed`, and the
  `Keywords` / `Wex` category lists that follow are returned separately.
- <https://www.law.cornell.edu/wex/all/{letter}> — the A–Z index (30 pages:
  a–z plus 1, 3, 4, 7, 9). `/wex/all` redirects to `/wex/all/a`. Counts per
  letter are in the letter nav (5,608 entries on 2026-09-14).
- `/wex/search?keys=…` renders no results server-side, which is why search is
  built over the index pages instead.
- `?_format=json` on an entry answers 200 with HTML, not JSON.

## Quick Start

Add to your MCP client (Claude Desktop, Cursor, Windsurf, etc.):

```json
{
  "mcpServers": {
    "cornell-wex": {
      "url": "https://gateway.pipeworx.io/cornell-wex/mcp"
    }
  }
}
```

### What this endpoint actually serves

`tools/list` at `https://gateway.pipeworx.io/cornell-wex/mcp` returns the tools in the table
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

## No MCP client? Call it over HTTP

```bash
curl -X POST https://gateway.pipeworx.io/v1/tools/wex_define \
  -H 'Content-Type: application/json' \
  -d '{"term":"tort"}'
```

No account needed for the first calls. Inspect any tool: `GET https://gateway.pipeworx.io/v1/tools/wex_define`. Find one: `POST https://gateway.pipeworx.io/v1/tools/search_packs` with `{"query":"..."}`.

## Standalone (no gateway account)

This package also runs as a local stdio MCP server — no Pipeworx account, no
gateway round-trip:

```json
{
  "mcpServers": {
    "cornell-wex": {
      "command": "npx",
      "args": ["-y", "@pipeworx/mcp-cornell-wex"]
    }
  }
}
```

Or run it directly to confirm it starts:

```bash
npx -y @pipeworx/mcp-cornell-wex
```

It speaks MCP over stdin/stdout and answers `initialize`/`tools/list`/`tools/call`
for **only** this pack's tools — none of the shared meta-tools the gateway
connection above adds. Same source, same tools, no ask_pipeworx routing.

## Using with ask_pipeworx

Instead of calling tools directly, you can ask questions in plain English —
this works on the pack endpoint above as well as on the full gateway:

```
ask_pipeworx({ question: "your question about Cornell Wex data" })
```

The gateway picks the right tool and fills the arguments automatically.

## More

- [Docs and guides](https://pipeworx.io/docs)
- [pipeworx.io](https://pipeworx.io)

## License

MIT
