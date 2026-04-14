# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

A GitHub template that runs Claude Code on a cron schedule via GitHub Actions. No server or Docker required.

## Architecture

The entire project is a single workflow file:

- **`.github/workflows/claude-cron.yml`** — installs Claude Code CLI on `ubuntu-latest`, authenticates via `ANTHROPIC_API_KEY` or `CLAUDE_CODE_OAUTH_TOKEN`, and runs `claude -p "$CLAUDE_PROMPT"`. Triggered by `schedule` or `workflow_dispatch`.

## Configuration (GitHub repo settings)

| Variable | Type | Description |
|---|---|---|
| `CLAUDE_PROMPT` | Repository variable | Task/message to send to Claude Code |
| `CLAUDE_CODE_OAUTH_TOKEN` | Secret | Token from `claude setup-token` (Pro/Max quota, 1 year) |
| `ANTHROPIC_API_KEY` | Secret | Anthropic API key (per-token billing) |

## Key Implementation Details

- Auth precedence: `ANTHROPIC_API_KEY` takes over `CLAUDE_CODE_OAUTH_TOKEN` if both are set (CLI behavior).
- `timeout-minutes: 5` caps each run — the prompt + response should complete well within this.
- The CLI installation is cached weekly via `actions/cache` (key: `claude-code-{os}-{year}-{week}`) so most runs skip the download entirely.
- `workflow_dispatch` allows a one-off prompt override without changing the repo variable.
- GitHub Actions cron runs on UTC and may be delayed during high load.
