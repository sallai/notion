---
name: notion
description: Use Notion through a bundled single-file Python CLI. Use when Codex needs to authenticate to Notion, search Notion, read/export pages, inspect or modify blocks, create/update/trash/restore pages, manage comments, work with databases or data sources, list users, or call raw Notion API endpoints. Also use when Notion credentials are missing or invalid and the user needs an agent-readable setup walkthrough.
---

# Notion

## Core Rule

Use the bundled CLI for Notion work:

```sh
uv run <skill-dir>/scripts/notion-cli ...
```

Resolve `<skill-dir>` to this skill folder before running commands. Always use `uv` for Python execution. Do not create Python packages for this skill; keep future Python tooling as single-file scripts with PEP 723 metadata.

Prefer documentation, command help, setup text, examples, and reusable assets embedded in `scripts/notion-cli` over separate files. Start with:

```sh
uv run <skill-dir>/scripts/notion-cli setup
uv run <skill-dir>/scripts/notion-cli --help
uv run <skill-dir>/scripts/notion-cli page --help
uv run <skill-dir>/scripts/notion-cli block --help
```

## Credentials

Load credentials from `~/.notion/env`. Accepted variables are `NOTION_TOKEN`, `NOTION_API_KEY`, and `NTN_TOKEN`.

If credentials are missing or invalid, run:

```sh
uv run <skill-dir>/scripts/notion-cli setup
```

Use the command's STDOUT to guide the user. Never ask the user to paste real tokens into chat. Never print or commit real Notion tokens.

## Workflows

Use read commands before mutations:

```sh
uv run <skill-dir>/scripts/notion-cli me
uv run <skill-dir>/scripts/notion-cli search "query" --object page
uv run <skill-dir>/scripts/notion-cli page get "<page-url-or-id>"
uv run <skill-dir>/scripts/notion-cli page export "<page-url-or-id>" "output-basename"
uv run <skill-dir>/scripts/notion-cli block children "<page-or-block-id>" --recursive --markdown
uv run <skill-dir>/scripts/notion-cli comment list "<page-or-block-id>"
```

Use write commands only when the user has requested changes. After any mutation, verify by reading back with the CLI.

Common mutation surfaces:

```sh
uv run <skill-dir>/scripts/notion-cli page create ...
uv run <skill-dir>/scripts/notion-cli page update "<page-id>" ...
uv run <skill-dir>/scripts/notion-cli page delete "<page-id>"
uv run <skill-dir>/scripts/notion-cli page restore "<page-id>"
uv run <skill-dir>/scripts/notion-cli block append "<page-or-block-id>" ...
uv run <skill-dir>/scripts/notion-cli block update "<block-id>" ...
uv run <skill-dir>/scripts/notion-cli block delete "<block-id>"
uv run <skill-dir>/scripts/notion-cli comment create ...
```

Use the raw API escape hatch for endpoints that do not need a new high-level wrapper:

```sh
uv run <skill-dir>/scripts/notion-cli api GET "/users/me"
uv run <skill-dir>/scripts/notion-cli api POST "/search" --data '{"query":"report"}'
```

Notion page delete means trash. Use restore when reversal is needed.

## Self-Maintenance

If a needed Notion capability is missing:

1. First check `setup`, `--help`, subcommand help, and the raw `api` command.
2. If the raw API command is enough, use it without changing the script.
3. If a reusable CLI feature is needed, explain the missing capability and ask the user before editing `scripts/notion-cli`.
4. After explicit approval, update only the bundled single-file CLI unless the user asks otherwise.
5. Keep new docs, setup guidance, examples, and small reusable snippets embedded in the CLI script when practical.
6. Re-run the updated CLI and verify the original Notion task succeeds.

When changing `scripts/notion-cli`, keep it dependency-free unless a dependency is clearly necessary and declared in the PEP 723 block.
