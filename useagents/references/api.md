# UseAgents REST API

Use the HTTP API from custom agent runtimes, backends, or automation.

## Base URL

```text
https://api.useagents.site
```

Docs: https://docs.useagents.site/api-reference/introduction

## Search

```http
GET /tools/search?q=<natural-language-query>
```

Include language, transport, category, or other documented filters when available. Use results to shortlist by slug and metadata.

Search reference: https://docs.useagents.site/api-reference/tools-search

## Context

After choosing a slug from search:

```http
GET /tools/context/:slug
```

Prefer MCP `get_tool_context` or CLI `useagents context <slug>` when those surfaces are available. Otherwise use this endpoint or bootstrap fields from search results.

## Docs search

Search a tool's official documentation with a natural-language question:

```http
GET /tools/docs/:slug?q=<question>
```

| Parameter | Location | Required | Description |
| --------- | -------- | -------- | ----------- |
| `slug` | path | Yes | Exact tool slug from search results |
| `q` | query | Yes | Natural-language docs question |
| `format` | query | No | `json` (default) or `toon` |

Example:

```bash
curl "https://api.useagents.site/tools/docs/resend?q=how%20do%20I%20send%20attachments"
```

Docs reference: https://docs.useagents.site/api-reference/tools-docs

## Recommended workflow

1. `GET /tools/search?q=...` — find candidate tools
2. Shortlist by slug and metadata
3. `GET /tools/context/:slug` — install and quickstart context
4. `GET /tools/docs/:slug?q=...` — deeper docs answers when needed
5. Implement from context and docs passages

## Notes

- Public beta: no API key required for typical agent queries; subject to rate limits.
- Rate limits: https://docs.useagents.site/api-reference/rate-limit
- If install or usage context is missing, say so — do not invent from training data.
