# PR Review Command Usage Guide

## Overview
The `/review-all-prs` command provides automated, comprehensive code reviews that match the quality of GitHub Actions Claude Code reviews, but can be run on-demand from the Claude CLI.

## Command Location
`.claude/commands/review-all-prs.md`

## Usage Modes

### 1. Review All Open PRs
```
/review-all-prs
```
Reviews every open pull request in the repository and posts comments to each.

### 2. Review Specific PR
```
/review-all-prs 347
```
Reviews only PR #347 and posts the review comment.

### 3. Dry-Run Mode (Preview)
```
/review-all-prs --dry-run
```
Generates reviews for all PRs but displays them in the chat instead of posting to GitHub. Useful for:
- Testing the command
- Previewing review quality
- Learning from the analysis

## What Gets Reviewed

Each PR receives analysis on:

1. **Code Quality**
   - Adherence to best practices
   - Code structure and maintainability
   - Readability and clarity

2. **Security**
   - Potential vulnerabilities
   - Security implications of changes
   - Credential/secret exposure

3. **Performance**
   - Performance impact
   - Optimization opportunities
   - Resource usage

4. **Testing**
   - Test coverage assessment
   - Missing test scenarios
   - Testing approach quality

5. **Bugs & Issues**
   - Logic errors
   - Edge cases
   - Potential runtime issues

6. **Documentation**
   - Code comments
   - PR description quality
   - Documentation updates needed

## Review Format

Reviews are posted in this structured format:

```markdown
## Claude Code Review

### Summary
[Brief overview and assessment]

### Key Findings

#### Strengths
- [Positive aspects]

#### Issues
- [Bugs, security, critical concerns]

#### Suggestions
- [Improvements and recommendations]

### Code Quality
[Detailed quality assessment]

### Testing
[Test coverage analysis]

### Security
[Security considerations]

### Performance
[Performance implications]

### Documentation
[Documentation assessment]

---
*Automated review by Claude Code*
```

## Example Output

See `sample_review_output.md` for a complete example review of PR #347, which demonstrates:
- Identification of subtle behavioral changes
- Security-positive observations
- Specific file/line references
- Actionable recommendations
- Severity categorization

## Comparison to GitHub Actions

This command provides the **same comprehensive analysis** as the GitHub Actions workflow (`.github/workflows/claude-code-review.yml`) but offers:

**Advantages:**
- On-demand execution (don't wait for CI)
- Batch review all PRs at once
- Dry-run mode for previewing
- Control over which PRs to review
- No CI resources consumed

**GitHub Actions advantages:**
- Automatic on every PR
- Integrated into CI/CD workflow
- Requires no manual trigger

## Best Practices

1. **Use dry-run first** when testing or learning: `/review-all-prs --dry-run`
2. **Review specific PRs** during active development: `/review-all-prs 347`
3. **Batch review** before releases or sprints: `/review-all-prs`
4. **Check repository conventions** - the command reads CLAUDE.md if present
5. **Combine with manual review** - use Claude's analysis to augment human review

## Technical Details

The command uses:
- `gh pr list` - List open PRs
- `gh pr view` - Get PR details
- `gh pr diff` - Analyze changes
- `gh pr comment` - Post reviews

Requires:
- GitHub CLI (`gh`) installed and authenticated
- Appropriate repository permissions
- Claude Code with command support

## Limitations

- Reviews are based on diff analysis (no runtime testing)
- Cannot access external documentation or APIs
- Quality depends on code context available in the diff
- May not catch issues that require broader codebase understanding

## Future Enhancements

Potential improvements:
- Filter by PR labels or authors
- Custom review templates
- Integration with review comment threads
- Support for review suggestions/inline comments
- Summary reports across all PRs
