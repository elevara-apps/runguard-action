# RunGuard Actions Report

RunGuard Actions Report is the free GitHub Marketplace Action for repository-level workflow spend and CI/CD guardrail checks.

Public site: https://runguard-dev.github.io/

It scans `.github/workflows` and writes a GitHub Step Summary with:

- expensive runner labels
- missing concurrency cancellation
- long job timeouts
- unpinned third-party Actions
- broad workflow permissions
- risky `pull_request_target` usage
- untrusted PR, issue, or comment text flowing into shell or agentic steps

## Usage

```yaml
name: RunGuard report

on:
  pull_request:
    paths:
      - ".github/workflows/**"
  workflow_dispatch:

permissions:
  contents: read
  pull-requests: write

jobs:
  runguard:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: elevara-apps/runguard-action@v0.1.1
        with:
          comment: "true"
          fail-on: "critical"
```

## Inputs

| Input | Default | Description |
|---|---|---|
| `workflow-path` | `.github/workflows` | Directory containing workflow files. |
| `comment` | `false` | Post a PR comment when running on pull requests. |
| `fail-on` | `never` | Fail on `critical`, `high`, `medium`, `low`, or `never`. |
| `average-monthly-runs` | `80` | Used for approximate waste estimates. |
| `github-token` | `${{ github.token }}` | Token for optional comments. |

## Outputs

| Output | Description |
|---|---|
| `risk-score` | 0-100 workflow spend/security score. |
| `finding-count` | Number of findings. |
| `estimated-monthly-waste-usd` | Approximate monthly waste estimate. |

## Sample Report

See `examples/sample-report.md` in the public Action repository for a generated report from an intentionally risky workflow.

The Action is the first distribution surface. Install the RunGuard GitHub App for organization-wide scheduled reporting once available.
