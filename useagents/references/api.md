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

## Test

Run an agent-authored snippet in a UseAgents sandbox: install named packages, write files, and execute an entry file. Does not run on the caller's machine.

```http
POST /tools/test
```

| Field | Required | Description |
| ----- | -------- | ----------- |
| `language` | Yes | Snippet language, for example `typescript` or `python` |
| `runtime` | Yes | `node`, `python`, `golang`, `ruby`, or `rust` |
| `files` | Yes* | 1–20 objects with `path` (relative) and `code`. Required unless `sessionId` is set |
| `packages` | No | Package names to install (`bun add`, `pip install`, `go get`, `gem install`, or `cargo add`) |
| `entry` | No | File to execute. Defaults to the first `files` path. Required when `files` is empty |
| `sessionId` | No | Previous session. Reuses packages and files; the box is paused between runs |
| `slug` | No | Registry slug this snippet is testing (analytics) |
| `env` | No | `{ name, value?, secret? }` pairs. Each item needs a `value` or a `secret` |
| `timeoutMs` | No | 1000–60000. Default 30000 |

Query parameter `format` is `json` (default) or `toon`. Pass secrets through `env[].secret`. JavaScript and TypeScript (`runtime: node`) use the Bun runtime. The sandbox allows outbound network.

Example:

```bash
curl -X POST "https://api.useagents.site/tools/test" \
  -H "Content-Type: application/json" \
  -d '{
    "slug": "resend",
    "language": "typescript",
    "runtime": "node",
    "packages": ["resend"],
    "env": [{ "name": "RESEND_API_KEY", "secret": "re_test" }],
    "files": [
      {
        "path": "src/index.ts",
        "code": "import { Resend } from \"resend\";\nconsole.log(typeof new Resend(\"re_test\").emails.send);\n"
      }
    ]
  }'
```

A successful run returns `ok: true` and `status: ran`. A failed snippet is still HTTP 200 with `ok: false` and `status` of `failed_install`, `failed_run`, `timeout`, `blocked`, or `unavailable`. HTTP 422 is invalid input. HTTP 503 means the sandbox provider is unavailable. HTTP 429 is the dedicated 5 requests/minute limiter. Reuse the returned `sessionId` on the next call.

Test reference: https://docs.useagents.site/api-reference/tools-test

## Recommended workflow

1. `GET /tools/search?q=...` — find candidate tools
2. Shortlist by slug and metadata
3. `GET /tools/context/:slug` — install and quickstart context
4. `GET /tools/docs/:slug?q=...` — deeper docs answers when needed
5. Implement from context and docs passages
6. `POST /tools/test` — sandbox smoke test when you want to confirm the snippet runs

## Notes

- Public beta: no API key required for typical agent queries; subject to rate limits.
- Rate limits: https://docs.useagents.site/api-reference/rate-limit
- If install or usage context is missing, say so — do not invent from training data.
