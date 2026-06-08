# RunGuard Actions Report

RunGuard Actions Report scans GitHub Actions workflow files for spend waste, risky permissions, unpinned Actions, expensive runner usage, missing concurrency, long timeouts, `pull_request_target` risk, and agentic workflow injection paths.

This is the Marketplace Action distribution repository for RunGuard for GitHub by Elevara.

## Usage

```yaml
name: RunGuard

on:
  pull_request:
  workflow_dispatch:

jobs:
  runguard:
    runs-on: ubuntu-latest
    permissions:
      contents: read
      pull-requests: write
    steps:
      - uses: actions/checkout@v4
      - uses: elevara-apps/runguard-action@v0.1.0
        with:
          comment: "true"
          fail-on: "critical"
```

## Inputs

| Input | Default | Description |
|---|---:|---|
| `workflow-path` | `.github/workflows` | Directory containing workflow YAML files. |
| `comment` | `false` | Post a pull request comment when running on a PR. |
| `fail-on` | `never` | Fail when findings meet `critical`, `high`, `medium`, or `low`. |
| `average-monthly-runs` | `80` | Run count used for monthly waste estimates. |
| `github-token` | `${{ github.token }}` | Token used for optional PR comments. |

## Outputs

- `risk-score`
- `finding-count`
- `estimated-monthly-waste-usd`

## Product Path

The free Action is the top-of-funnel distribution wedge. The GitHub App adds org-wide scheduled reports, install-level history, budget alerts, and paid Marketplace plans after install traction.

Product workspace and marketing site:

- https://github.com/elevara-apps/runguard-for-github
- https://elevara-apps.github.io/runguard-for-github/
