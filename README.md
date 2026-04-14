# claude-code-cron

A GitHub template to run Claude Code on a cron schedule — no server required.

## Quick start

1. Click **"Use this template"** to create your repo
2. Configure authentication (see [Authentication](#authentication))
3. Set your prompt (see [Configuration](#configuration))

The workflow runs automatically on schedule, or trigger it manually from the **Actions** tab.

## Authentication

Add one of the following as a repository secret in **Settings > Secrets and variables > Actions**.

### Option A — Claude Pro/Max subscription (recommended)

Uses your existing Claude subscription quota. Generates a token valid for 1 year.

```bash
# Run locally (requires Claude Code installed)
claude setup-token
```

Add the output as a secret named `CLAUDE_CODE_OAUTH_TOKEN`.

> Requires Claude Pro, Max, Team, or Enterprise subscription.

### Option B — Anthropic API key

Billed per token. Get your key at [console.anthropic.com](https://console.anthropic.com).

Add it as a secret named `ANTHROPIC_API_KEY`.

> If both secrets are set, the API key takes precedence.

## Configuration

### Prompt

Add `CLAUDE_PROMPT` as a **repository variable** (Settings > Secrets and variables > Actions > Variables):

```
CLAUDE_PROMPT=Review open issues in this repo and suggest which ones to tackle next.
```

You can also override it for a single run via **Actions > Claude Code Cron > Run workflow**.

### Model

Optionally add `CLAUDE_MODEL` as a **repository variable** to choose which model to use. If not set, Claude Code uses its default model.

Check the [Anthropic docs](https://docs.anthropic.com/en/docs/about-claude/models) for the latest available model IDs.

### Cron schedule

Edit the `cron` expression in `.github/workflows/claude-cron.yml`:

```yaml
schedule:
  - cron: '0 6 * * *'  # every day at 6:00 UTC (default)
```

**GitHub Actions always uses UTC.** There is no timezone setting — you need to convert your local time manually:

| Your local time | Offset | UTC value to use |
|---|---|---|
| 6:00 Spain/Paris (winter) | UTC+1 | `0 5 * * *` |
| 6:00 Spain/Paris (summer) | UTC+2 | `0 4 * * *` |
| 6:00 New York (winter) | UTC-5 | `0 11 * * *` |
| 6:00 New York (summer) | UTC-4 | `0 10 * * *` |
| 6:00 London (winter) | UTC+0 | `0 6 * * *` |
| 6:00 London (summer) | UTC+1 | `0 5 * * *` |

Formula: `UTC hour = local hour - offset`. For example, 6:00 at UTC+2 → `6 - 2 = 4` → `0 4 * * *`.

Note: if your region observes daylight saving time, the UTC equivalent shifts by 1 hour twice a year — there is no way to handle this automatically in GitHub Actions.

GitHub Actions cron may also be delayed by a few minutes during periods of high load.

## Syncing with the template

Repos created from a template don't automatically receive updates. To pull in the latest changes:

```bash
# Add the template as a remote (one-time)
git remote add template https://github.com/susomejias/claude-code-cron.git

# Fetch and merge the latest changes
git fetch template
git merge template/main --allow-unrelated-histories
```

Resolve any conflicts, then commit and push. You can repeat the fetch + merge step anytime the template is updated.
