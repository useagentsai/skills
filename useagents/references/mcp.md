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

## Recommended workflow

1. Call `search_tools` with a task-focused query.
2. Shortlist candidates from the results.
3. Call `get_tool_context(slug)` for install and quickstart context.
4. Call `search_docs(slug, query)` when you need deeper answers from official docs.
5. Implement from context and docs passages.

## Prompt

`developer_tool_discovery` — reusable workflow: search first, fetch tool context second, search docs when needed, implement last.

## Client guides

- Cursor: https://docs.useagents.site/mcp/clients/cursor
- Claude: https://docs.useagents.site/mcp/clients/claude
- Codex: https://docs.useagents.site/mcp/clients/codex
- More: https://docs.useagents.site/mcp/connecting
