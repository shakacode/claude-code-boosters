---
description: Comprehensive self-review of uncommitted changes before creating a PR
---

# Self-Review

Review all uncommitted changes on this branch before creating a PR.

## Process

1. Run `git fetch origin` and determine the base branch
2. Run `git diff origin/<base-branch>...HEAD` to get the full diff
3. Review changes for bugs, security issues, performance problems, missing tests, and style violations
4. Check that all files end with newline characters and no debug code is left in

## Output Format

**Summary**: Overall readiness assessment (Ready / Needs Work / Blocked)

**Issues found:**

| Severity | File:Line | Issue | Suggested Fix |
|----------|-----------|-------|---------------|
| Critical | path/file:42 | Description | Concrete fix |
| High | path/file:15 | Description | Concrete fix |
| Medium | path/file:30 | Description | Concrete fix |
| Low | path/file:10 | Description | Concrete fix |

Severity: Critical = security/data loss, High = will cause failures, Medium = quality/performance, Low = style/nitpick

## After Review

- Offer to fix any issues found
- Run relevant tests and linters
- Offer to push and create the PR

## Guidelines

- Focus on issues that matter, not pedantic nitpicks
- Respect existing patterns -- don't suggest changes just to match your preference
- If the PR looks good, say so. Don't manufacture issues.
