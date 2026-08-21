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

## Context after search

After choosing a slug from search:

1. Prefer the UseAgents MCP `get_tool_context` or CLI `useagents context <slug>` when those surfaces are available.
2. Otherwise use public listing / docs links and bootstrap fields returned with search results, and follow the tool's official docs.
3. If install or usage context is missing, say so — do not invent from training data.

## Notes

- Public beta: no API key required for typical agent queries; subject to rate limits.
- Rate limits: https://docs.useagents.site/api-reference/rate-limit
