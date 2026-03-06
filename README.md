# Sync Master to Releases

GitHub Action that automatically merges `master` into all active release branches. Ensures hotfixes and production changes propagate to all ongoing releases.

## How it works

When triggered by a push to `master`, this action:

1. Finds all remote branches matching the release pattern (e.g. `release_0220`, `release_0315`)
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
  sync-master-to-release:
    runs-on: ubuntu-latest
    steps:
      - name: Sync master to releases
        uses: dejvidecz/sync_master_to_releases@v1
        with:
          token: ${{ secrets.GITHUB_TOKEN }}
          teams_webhook_url: ${{ secrets.TEAMS_WEBHOOK_URL }}
          teams_mention_emails: ${{ secrets.TEAMS_MENTION_EMAILS }}
```

## Inputs

| Input | Required | Default | Description |
|-------|----------|---------|-------------|
| `token` | Yes | — | GitHub token with push permissions |
| `fallback_to_develop` | No | `"true"` | Merge into `develop` if no release branch exists |
| `teams_webhook_url` | No | `""` | Microsoft Teams webhook URL for failure notifications |
| `teams_mention_emails` | No | `""` | Comma-separated emails to @mention in Teams on failure |
| `release_branch_pattern` | No | `^release_[0-9]{4}$` | Regex pattern for valid release branch names |

## Secrets setup

| Secret | Required | Description |
|--------|----------|-------------|
| `TEAMS_WEBHOOK_URL` | No | Teams Incoming Webhook URL (channel → Workflows → "Send webhook alerts to channel") |
| `TEAMS_MENTION_EMAILS` | No | Comma-separated Teams emails, e.g. `novak@firma.cz,svoboda@firma.cz` |
