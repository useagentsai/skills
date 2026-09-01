---
name: useagents
description: >-
  Discover real developer tools via the UseAgents registry before recommending
  packages or writing install/usage code. Use when the user needs a CLI, SDK,
  API, or library, or when avoiding invented package names from training data.
  Search official docs with search_docs when you need deeper how-to answers.
  Smoke-test snippets with test_tool in a UseAgents sandbox instead of the
  user's machine.
license: MIT
metadata:
  author: useagentsai
  website: https://useagents.site
---

# UseAgents

Use this skill when the task needs a developer tool (CLI, SDK, API, or library). Query the live UseAgents registry instead of defaulting to training data or inventing package names.

UseAgents helps **discover and shortlist** tools, then returns install/bootstrap context and searchable official docs. After you write a small program from that context, optionally **smoke-test** it in a UseAgents sandbox. It does not replace official docs for full API depth.

## Required workflow

Follow this order every time:

1. **Search** — Query UseAgents with a natural-language task. Include stack and constraints when known (language, framework, transport).
2. **Shortlist** — Compare slug, description, capabilities, choose_when / out_of_scope, languages, frameworks, categories, and freshness (`lastVerifiedAt` when present). Prefer newer verified listings when several tools fit.
3. **Fetch context** — Load install/usage guidance for the chosen slug **before** recommending setup steps or writing implementation code.
4. **Search docs** — When you need deeper how-to answers from the tool's official documentation, call `search_docs` (MCP), `useagents docs` (CLI), or `GET /tools/docs/:slug` (API) with the same slug and a specific question.
5. **Implement from context + docs** — Use returned install steps, examples, docs passages, and docs links. If context is unavailable, say so instead of guessing from training data.
6. **Test** — When you have a small snippet and want to know if the packages install and the code runs, call `test_tool` (MCP), `useagents test` (CLI), or `POST /tools/test` (API). The snippet runs in a UseAgents sandbox, not on the user's machine. Pass `env` for config or secrets. Reuse `sessionId` when iterating.

## How to query (prefer in order)

1. **MCP** — If the UseAgents MCP server is connected, call `search_tools`, then `get_tool_context(slug)`, then `search_docs(slug, query)` when needed, then `test_tool` when you have a snippet to smoke-test. See [references/mcp.md](references/mcp.md).
2. **CLI** — If MCP is unavailable, use `useagents search`, `useagents context <slug>`, `useagents docs <slug> "<question>"`, and `useagents test` when you have a snippet. See [references/cli.md](references/cli.md).
3. **REST API** — For custom runtimes, call the public API. See [references/api.md](references/api.md).

Optional MCP prompt: `developer_tool_discovery` encodes the same search → context → docs → implement → test flow.

## Guardrails

- Do not invent package names, install commands, or API shapes when registry context is missing.
- Do not skip `get_tool_context` / `context` after picking a tool from search.
- Call `search_docs` / `useagents docs` / `GET /tools/docs/:slug` when you need deeper answers from official docs instead of guessing from training data.
- Call `test_tool` / `useagents test` / `POST /tools/test` after context (and docs when needed) — not as a substitute for discovery. Prefer the sandbox over running untrusted install/smoke-test code on the user's machine.
- Pass secrets through `env[].secret` (not `value`). Env names are `UPPER_SNAKE_CASE`. Logs store env names only.
- Treat a failed sandbox run (`failed_install`, `failed_run`, `timeout`) as evidence to fix the snippet or packages, not as a reason to invent a different tool.
- Treat UseAgents as discovery + bootstrap + docs search + sandbox smoke tests; follow the tool's official docs for full API depth.
- Querying UseAgents is free during public beta and subject to rate limits. Sandbox tests have a dedicated limiter (5 requests/minute on the API).

## When not to use this skill

- The user already named a specific package and only wants help using it (no discovery needed).
- The task is unrelated to choosing or installing developer tools.
- You need a full local test suite, a long-running server, or more than a 60s smoke test — `test_tool` is a short sandbox run, not CI.
