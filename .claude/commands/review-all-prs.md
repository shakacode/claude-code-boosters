# Review All Open PRs

You are tasked with reviewing all open pull requests in this repository and posting comprehensive code review feedback directly to GitHub.

## Usage Modes

**Review all PRs**: `/review-all-prs`
**Review specific PR**: `/review-all-prs 347` (reviews only PR #347)
**Dry-run mode**: `/review-all-prs --dry-run` (shows reviews without posting to GitHub)

## Your Task

1. **Determine mode**: Check if a PR number was provided as an argument
   - If a number is provided, review only that PR
   - If `--dry-run` is provided, show reviews without posting
   - Otherwise, review all open PRs

2. **List PRs to review**: Use `gh pr list` to get all open pull requests (or just the specified one)

3. **For each PR, perform a comprehensive review**:
   - Get the PR details including title, description, and author
   - Get the full diff using `gh pr diff <pr-number>`
   - Analyze the changes for:
     - Code quality and adherence to best practices
     - Potential bugs or issues
     - Performance considerations
     - Security concerns
     - Test coverage
     - Adherence to repository conventions (check CLAUDE.md if it exists)
     - Breaking changes
     - Documentation needs

4. **Post your review**:
   - If in dry-run mode, display the review you would post
   - Otherwise, use `gh pr comment <pr-number>` to post your comprehensive review directly to the PR

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
- Provide specific line references when possible (e.g., "In file.rs:42...")
- Prioritize issues by severity (critical bugs > security > performance > style)
- Suggest concrete improvements with examples when relevant
- If the PR looks good, say so! Don't manufacture issues.
- Use the repository's coding standards and conventions from CLAUDE.md
- Keep reviews concise but thorough - aim for clarity over length

## Process

For each PR:
1. Announce which PR you're reviewing
2. Fetch and analyze the full diff
3. Provide your comprehensive analysis
4. Post the review comment to GitHub
5. Move to the next PR

Start by listing all open PRs and then review them one by one.
