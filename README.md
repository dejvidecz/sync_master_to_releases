# Sync Master to Releases

Reusable GitHub workflow that automatically merges `master` into all active release branches. Ensures hotfixes and production changes propagate to all ongoing releases.

## How it works

When triggered by a push to `master`, this workflow:

1. Finds all remote branches matching the release pattern (e.g. `release_0220`, `release_20260220`)
2. Ignores branches with suffixes (e.g. `release_0220_hotfix`)
3. Merges `master` into each release branch
4. If no release branch exists — falls back to merging into `develop` (configurable)
5. On failure — sends a notification to Microsoft Teams (optional)

## Usage

```yaml
name: "Synchronize master into all release branches"

on:
  push:
    branches:
      - master

jobs:
  sync:
    uses: dejvidecz/sync_master_to_releases/.github/workflows/sync.yml@v1
    secrets: inherit
```

## Inputs

| Input | Required | Default | Description |
|-------|----------|---------|-------------|
| `release_branch_pattern` | No | `^release[-_]([0-9]{4}\|[0-9]{8})$` | Regex pattern for valid release branch names |
| `fallback_to_develop` | No | `true` | Merge into `develop` if no release branch exists |

## Secrets (via `secrets: inherit`)

| Secret | Required | Description |
|--------|----------|-------------|
| `SYNC_TEAMS_WEBHOOK_URL` | No | Teams Incoming Webhook URL for failure notifications |
| `SYNC_TEAMS_MENTION_EMAILS` | No | Comma-separated Teams emails to @mention, e.g. `novak@firma.cz,svoboda@firma.cz` |
