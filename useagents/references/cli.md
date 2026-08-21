# UseAgents CLI

Use the CLI when MCP is not connected, or for terminal/scripts.

## Install

npm:

```bash
npm install --global @useagents/cli
useagents --help
```

Standalone binary (macOS/Linux):

```bash
curl -fsSL https://useagents.site/cli/install.sh | sh
useagents --help
```

Docs: https://docs.useagents.site/agents/cli

## Search

```bash
useagents search "email API" --limit 5
useagents search "MCP server" --language typescript-javascript --transport mcp
```

Useful flags: `--limit`, `--language` / `-l`, `--transport` / `-t`, `--category` / `-c`, `--json`.

## Context

```bash
useagents context resend
useagents context resend -l typescript-javascript -t api
```

Call `context` for the chosen slug after shortlisting search results. Context includes description, install steps, examples, and docs links.

## Environment

- `USEAGENTS_API_URL` — override API base (default production API)
- `USEAGENTS_API_KEY` — optional; not required during public beta
- `--json` — machine-readable output (also used when stdout is piped)
