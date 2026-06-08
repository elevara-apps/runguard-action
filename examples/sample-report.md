# RunGuard GitHub Actions Report

Generated: 2026-06-08T14:15:11.956Z

- Workflows scanned: **1**
- Findings: **12**
- Risk score: **100/100**
- Estimated monthly waste: **$146.28**
- Severity mix: critical 4, high 2, medium 4, low 2

## Workflow Summary

| Workflow | Triggers | Runners | Findings | Est. monthly waste |
|---|---|---|---:|---:|
| .github/workflows/risky-review.yml | pull_request_target, schedule | macos-latest | 12 | $146.28 |

## Top Findings

| Severity | Rule | Category | Finding | Location | Fix |
|---|---|---|---|---|---|
| critical | permissions-write-all | security | Workflow grants write-all token permissions | .github/workflows/risky-review.yml:12 | Replace `write-all` with the smallest set of required permissions. |
| critical | pull-request-target | security | Workflow uses pull_request_target | .github/workflows/risky-review.yml:4 | Use `pull_request` unless this workflow is carefully isolated and does not run untrusted code. |
| critical | pull-request-target-checkout-head | security | pull_request_target workflow appears to checkout PR-controlled code | .github/workflows/risky-review.yml:17 | Do not checkout PR head code in `pull_request_target`. Split label/comment automation from untrusted code execution. |
| critical | agentic-workflow-injection | security | Possible agentic workflow injection path | .github/workflows/risky-review.yml:18 | Treat PR, issue, and comment text as untrusted. Add prompt boundaries, allowlists, and restricted tokens before invoking AI or agentic tools. |
| high | action-not-sha-pinned | security | Action is not pinned to a full commit SHA | .github/workflows/risky-review.yml:18 | Pin high-risk third-party Actions to a full commit SHA and review update cadence. |
| high | untrusted-context | security | Workflow consumes untrusted GitHub event context | .github/workflows/risky-review.yml:20 | Do not pass untrusted event text directly to shell, deployment, release, or agentic steps. |
| medium | permissions-missing | security | Workflow has no top-level permissions block | .github/workflows/risky-review.yml | Add top-level `permissions: read-all` or the minimum specific scopes needed by each job. |
| medium | action-not-sha-pinned | security | Action is not pinned to a full commit SHA | .github/workflows/risky-review.yml:15 | Pin high-risk third-party Actions to a full commit SHA and review update cadence. |
| medium | expensive-runner | spend | macOS runner may be driving avoidable Actions spend | .github/workflows/risky-review.yml:11 | Confirm this runner is required. Move portable jobs to Linux and reserve expensive runners for platform-specific work. |
| medium | frequent-schedule | spend | Workflow schedule runs very frequently | .github/workflows/risky-review.yml:7 | Use a less frequent schedule unless the workflow is genuinely time-sensitive. |
| low | missing-concurrency | reliability | Workflow has no concurrency cancellation | .github/workflows/risky-review.yml | Add a `concurrency` group with `cancel-in-progress: true` for PR and branch workflows. |
| low | long-timeout | spend | Workflow job has a long timeout | .github/workflows/risky-review.yml:13 | Set job timeouts close to expected runtime and alert separately on genuine long-running work. |

## Fix Snippets

### permissions-write-all: Workflow grants write-all token permissions

A compromised step or dependency can write code, tags, releases, issues, or other repository resources.

```text
permissions:
  contents: read
  pull-requests: write # only when PR comments are required
```

### pull-request-target: Workflow uses pull_request_target

If the workflow checks out or executes attacker-controlled code, a forked PR can become a write-token or secret-exposure path.

```text
on:
  pull_request:
    types: [opened, synchronize, reopened]
```

### pull-request-target-checkout-head: pull_request_target workflow appears to checkout PR-controlled code

This is one of the highest-risk GitHub Actions patterns because privileged workflow context can execute attacker-controlled fork code.

```text
on:
  pull_request:

permissions:
  contents: read
```

### agentic-workflow-injection: Possible agentic workflow injection path

A PR, issue, or comment can become a prompt-injection path into tools that can read code, call CLIs, or write comments.

```text
permissions:
  contents: read
  pull-requests: read

# Pass only reviewed, bounded context to AI/agent tooling.
```

### action-not-sha-pinned: Action is not pinned to a full commit SHA

Mutable tags and branches can silently change the code that runs inside CI/CD.

```text
uses: vendor/review-agent@<40-character-reviewed-commit-sha>
```

### untrusted-context: Workflow consumes untrusted GitHub event context

Untrusted text can alter shell commands, release notes, prompts, or agent instructions if it is interpolated without boundaries.

```text
- Store untrusted text in a file or escaped environment value.
- Avoid direct shell interpolation.
- Add allowlists before release, deploy, or agent steps.
```

## Next Actions

- Add top-level least-privilege `permissions` to workflow files.
- Pin third-party Actions to full commit SHAs for high-risk or secret-bearing jobs.
- Add `concurrency` cancellation to PR workflows that can supersede older runs.
- Review macOS, Windows, and larger-runner usage for avoidable spend.
- Install the RunGuard GitHub App for org-wide scheduled reports and budget alerts.
