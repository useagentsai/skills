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

## Prompt

`developer_tool_discovery` — reusable workflow: search first, fetch tool context second, implement last.

## Client guides

- Cursor: https://docs.useagents.site/mcp/clients/cursor
- Claude: https://docs.useagents.site/mcp/clients/claude
- Codex: https://docs.useagents.site/mcp/clients/codex
- More: https://docs.useagents.site/mcp/connecting
