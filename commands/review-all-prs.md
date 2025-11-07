---
description: Review all open PRs or a specific PR with comprehensive code analysis
---

You are tasked with reviewing pull requests in this repository and posting comprehensive code review feedback directly to GitHub.

## Usage Modes

**Review all PRs**: `/review-all-prs`
**Review specific PR**: `/review-all-prs 347` (reviews only PR #347)
**Dry-run mode**: `/review-all-prs --dry-run` (shows reviews without posting to GitHub)
**Dry-run specific PR**: `/review-all-prs 347 --dry-run`

# Instructions

1. **Parse arguments from the user's message:**
   - Extract any numeric argument as the PR number (e.g., "347" or "12345")
   - Check if `--dry-run` flag is present (can be before or after PR number)
   - If no PR number is provided and no `--dry-run` flag, review all open PRs
   - Examples of valid inputs:
     - "347" → Review PR #347 and post comment
     - "--dry-run" → Review all PRs but don't post
     - "347 --dry-run" → Review PR #347 but don't post
     - "" (empty) → Review all PRs and post comments

2. **Validate prerequisites:**
   - Verify `gh` CLI is installed and authenticated: `gh auth status`
   - If authentication fails, instruct user to run: `gh auth login`
   - Get current repository: `REPO=$(gh repo view --json nameWithOwner -q .nameWithOwner)`

3. **Check permissions before proceeding:**
   - Test if user can comment: `gh api repos/${REPO}/issues/comments --method POST --silent --dry-run 2>&1` or check: `gh api user -q .login`
   - If permission check fails, warn the user they may not be able to post comments

4. **List PRs to review:**
   - If specific PR number provided: Verify it exists with `gh pr view <pr-number> --json number,title`
   - If PR doesn't exist, show error and stop
   - If reviewing all PRs: `gh pr list --json number,title --limit 100`
   - If more than 10 PRs found, warn about performance and API rate limits, ask user to confirm
   - Estimate: ~30-60 seconds per PR, with substantial token usage

5. **For each PR, perform a comprehensive review:**
   - Get the PR details: `gh pr view <pr-number> --json number,title,body,author`
   - Get the full diff: `gh pr diff <pr-number>`
   - If diff is empty, skip the PR and note it
   - Check if CLAUDE.md exists: `test -f CLAUDE.md && cat CLAUDE.md` (if exists, use conventions; if not, use general best practices)
   - Analyze the changes for:
     - Code quality and adherence to best practices
     - Potential bugs or issues
     - Performance considerations
     - Security concerns
     - Test coverage
     - Adherence to repository conventions (from CLAUDE.md if available)
     - Breaking changes
     - Documentation needs

6. **Post your review:**
   - If in dry-run mode, display the review with clear heading: "=== DRY RUN: Would post to PR #<number> ==="
   - Otherwise, use `gh pr comment <pr-number> --body "<review>"` to post
   - If posting fails (permissions, rate limit, etc.), display the error and show the review that would have been posted
   - After successful post, confirm with: "✓ Posted review to PR #<number>"

## Review Format

Structure your review comment as follows:

```
## Claude Code Review

### Summary
[Brief summary of the PR's purpose and overall assessment]

### Key Findings

#### Strengths
- [List positive aspects]

#### Issues
- [List any bugs, security concerns, or critical issues]

#### Suggestions
- [List improvements and best practice recommendations]

### Code Quality
[Assessment of code quality, maintainability, readability]

### Testing
[Assessment of test coverage and testing approach]

### Security
[Any security considerations or concerns]

### Performance
[Any performance implications or optimizations]

### Documentation
[Documentation completeness and clarity]

---
*Automated review by Claude Code*
```

## Important Guidelines

- Be constructive and helpful, not just critical
- Provide specific line references using format: `(file.rs:42)` or `(file.rs:47-52)` for ranges
- Prioritize issues by severity (critical bugs > security > performance > style)
- Suggest concrete improvements with examples when relevant
- If the PR looks good, say so! Don't manufacture issues.
- Use the repository's coding standards and conventions from CLAUDE.md if it exists
- Keep reviews concise but thorough - aim for clarity over length

## Error Handling

Handle these scenarios gracefully:

- **PR doesn't exist**: Show error message with PR number and stop
- **Empty diff**: Note the PR has no changes and skip review
- **gh CLI not authenticated**: Show `gh auth login` instructions
- **Insufficient permissions**: Warn user and offer to show review in dry-run mode
- **API rate limit**: Catch error, show how many PRs were reviewed, and instruct user to wait and retry
- **Network errors**: Show error and suggest retry

## Testing Recommendations

Before using this command on important PRs:

1. Test with dry-run mode first: `/review-all-prs --dry-run`
2. Test on a draft PR to verify comment posting works
3. Review a single PR before batch-reviewing all PRs: `/review-all-prs <number>`

## Performance Notes

- **Single PR**: ~30-60 seconds, moderate token usage
- **Multiple PRs**: ~30-60 seconds per PR, high token usage
- **10+ PRs**: Several minutes, very high token usage - confirm with user before proceeding
- **Rate limits**: GitHub API allows ~5000 requests/hour; each PR uses ~5-10 requests

## Process

For each PR:
1. Announce which PR you're reviewing (e.g., "Reviewing PR #347: Add feature X")
2. Fetch and analyze the full diff
3. Provide your comprehensive analysis
4. Post the review comment to GitHub (or show in dry-run mode)
5. Move to the next PR
6. After all reviews, summarize: "✓ Reviewed N PRs" (or "✓ Dry-run complete: Would review N PRs")

Start by validating prerequisites, then list PRs to review, then review them one by one.
