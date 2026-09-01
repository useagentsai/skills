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

## Docs

Search a tool's official documentation with a natural-language question:

```bash
useagents docs resend "how do I send attachments"
useagents docs stripe "configure webhook signature verification"
useagents docs resend "attachments" --format toon
```

Call `docs` after `context` when you need deeper how-to answers from official documentation.

## Test

Smoke-test a snippet in a UseAgents sandbox (not on the local machine). The CLI POSTs to `/tools/test` and exits `1` when `ok` is false.

```bash
useagents test \
  --runtime node \
  --language typescript \
  --package resend \
  --file src/index.ts \
  --slug resend
```

`--file` is relative to the current directory and may be repeated. `--package` and `--env NAME=value` are also repeatable. The sandbox allows outbound network so live vendor API calls can run; pass keys with `--env`. JavaScript and TypeScript (`--runtime node`) use the Bun runtime to add packages and run files. Reuse a previous sandbox with the `sessionId` from the last result. The box is paused between runs and has no hard TTL.

Call `test` after `context` (and `docs` when needed) when you have written a small program and want to know if it installs and runs.

## Recommended workflow

1. `useagents search "<task>"` — find candidate tools
2. Shortlist by slug and metadata
3. `useagents context <slug>` — install and quickstart context
4. `useagents docs <slug> "<question>"` — deeper docs answers when needed
5. Implement from context and docs
6. `useagents test` — sandbox smoke test when you want to confirm the snippet runs

## Environment

- `USEAGENTS_API_URL` — override API base (default production API)
- `USEAGENTS_API_KEY` — optional; not required during public beta
- `--json` — machine-readable output (also used when stdout is piped)
