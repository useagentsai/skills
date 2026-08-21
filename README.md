# UseAgents Skills

[![Skills](https://skills.sh/useagentsai/skills/badge.svg)](https://skills.sh/useagentsai/skills)

Agent Skills that teach coding agents how to discover real developer tools through [UseAgents](https://useagents.site) — a live registry of CLIs, SDKs, APIs, and libraries — instead of guessing from training data.

These skills are [Agent Skills](https://agentskills.io/) compatible and install via [skills.sh](https://skills.sh).

## Install

```bash
npx skills add useagentsai/skills
```

Or with Bun:

```bash
bunx skills add useagentsai/skills
```

This installs the `useagents` skill into your project (for example under `.agents/skills/`). Compatible agents (Cursor, Codex, Claude Code, and others) load the skill when you need a package, library, CLI, or SDK.

## What the skill does

When an agent needs a developer tool, it should:

1. **Search** the UseAgents registry with the task and stack constraints
2. **Shortlist** candidates from returned metadata
3. **Fetch context** (install steps, examples, docs link) for the chosen tool
4. **Implement** from that context and official docs — never invent package names or setup from training data when context is missing

Connect UseAgents over [MCP](https://docs.useagents.site/mcp/connecting), the [CLI](https://docs.useagents.site/agents/cli), or the [REST API](https://docs.useagents.site/api-reference/introduction). The skill directs the workflow; MCP/CLI/API are how the agent talks to the registry.

## Skills in this repo

| Skill | Description |
| ----- | ----------- |
| [`useagents`](./useagents/) | Discover tools via UseAgents before recommending packages or writing install/usage code |

## Docs

- [UseAgents](https://useagents.site)
- [Agents introduction](https://docs.useagents.site/agents/introduction)
- [Connect](https://docs.useagents.site/agents/connect)
- [Skills (docs)](https://docs.useagents.site/agents/skills)

## License

[MIT](./LICENSE)
