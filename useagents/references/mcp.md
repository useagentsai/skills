# UseAgents MCP

Prefer MCP when the client already supports it (Cursor, Claude, Codex, Windsurf, and others).

## Connection

| Setting | Value |
| ------- | ----- |
| URL | `https://mcp.useagents.site/mcp` |
| Auth | Not required during public beta |

Example client config:

```json
{
  "mcpServers": {
    "UseAgents": {
      "url": "https://mcp.useagents.site/mcp"
    }
  }
}
```

Docs: https://docs.useagents.site/mcp/connecting

## Tools

### `search_tools`

Find candidate tools with a natural-language query. Returns shortlisting metadata only — not full install instructions.

Typical inputs: `q` (task description), optional `language`, and other filters supported by the server.

### `get_tool_context`

Fetch install/usage context for a chosen tool **slug** from search results. Call this before recommending setup or writing implementation code.

If there is no result for the slug, say context is unavailable instead of guessing.

### `search_docs`

Search the official documentation for a published tool slug. Use after `search_tools` and `get_tool_context` when you need deeper how-to answers.

| Parameter | Required | Description |
| --------- | -------- | ----------- |
| `slug` | Yes | Exact tool slug from search results |
| `query` | Yes | Natural-language question to search in the tool's docs |
| `format` | No | `toon` (default) or `json` |

Example:

```json
{
  "slug": "resend",
  "query": "how do I send attachments"
}
```

Docs: https://docs.useagents.site/mcp/tools-reference/search-docs

### `test_tool`

Run a sandboxed smoke test of agent-authored code: install named packages, write files, and execute an entry file. Use after `get_tool_context` or `search_docs` when you have written a small program and want to know if it installs and runs. Does **not** execute on the user's machine.

The sandbox allows outbound network so live vendor API calls can run; pass keys via `env`. JavaScript and TypeScript (`runtime: node`) use the Bun runtime to add packages and run files. Pass `sessionId` from a previous result to reuse the same box (packages and files stay installed). The box is paused between runs and has no hard TTL.

| Parameter | Required | Description |
| --------- | -------- | ----------- |
| `language` | Yes | Snippet language, for example `typescript` or `python` |
| `runtime` | Yes | `node`, `python`, `golang`, `ruby`, or `rust` |
| `files` | Yes* | Objects with `path` and `code`. Required unless `sessionId` is set |
| `packages` | No | Package names to install (max 20) |
| `entry` | No | File to run. Defaults to the first file. Required when `files` is empty |
| `sessionId` | No | Previous `test_tool` session to reuse |
| `slug` | No | Registry slug this snippet is testing |
| `env` | No | `{ name, value?, secret? }` pairs. Each item needs a `value` or a `secret`. Omit or pass `[]` for none |
| `timeoutMs` | No | 1000–60000, default 30000 |
| `format` | No | `toon` (default) or `json` |

Example:

```json
{
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
}
```

A successful run returns `ok: true` and `status: ran`, plus `stdout` / `stderr` and a `sessionId` for the next call. A failed snippet still returns a structured result with `ok: false` and `status` of `failed_install`, `failed_run`, `timeout`, `blocked`, or `unavailable` — fix the snippet rather than inventing a different package.

Docs: https://docs.useagents.site/mcp/tools-reference/test-tool

## Recommended workflow

1. Call `search_tools` with a task-focused query.
2. Shortlist candidates from the results.
3. Call `get_tool_context(slug)` for install and quickstart context.
4. Call `search_docs(slug, query)` when you need deeper answers from official docs.
5. Implement from context and docs passages.
6. Call `test_tool` with the files you wrote when you want to confirm the packages install and the code runs. Pass `env` when the snippet needs config or secrets. Pass `sessionId` to iterate in the same sandbox.

## Prompt

`developer_tool_discovery` — reusable workflow: search first, fetch tool context second, search docs when needed, implement, then smoke-test with `test_tool`.

## Client guides

- Cursor: https://docs.useagents.site/mcp/clients/cursor
- Claude: https://docs.useagents.site/mcp/clients/claude
- Codex: https://docs.useagents.site/mcp/clients/codex
- More: https://docs.useagents.site/mcp/connecting
