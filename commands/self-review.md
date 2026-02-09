---
description: Comprehensive self-review of uncommitted changes before creating a PR
---

# Self Review

Perform a comprehensive code review on your uncommitted changes before creating a PR.

## Instructions

You are performing a self-review of code changes to ensure quality and catch issues early.

### Step 1: Fetch Changes

First, understand what has changed:

1. Run `git fetch origin` to get latest remote changes
2. Determine the base branch: `git remote show origin | grep 'HEAD branch' | awk '{print $NF}'`
3. Run `git status` to see current branch state
4. Run `git diff origin/<base-branch>...HEAD --stat` to see changed files
5. Run `git diff origin/<base-branch>...HEAD` to see the actual diff

### Step 2: Context Gathering

Based on the changed files, gather relevant context:

- Check if there are corresponding test files that should be reviewed
- Check for related configuration files that may need updates
- Review CLAUDE.md or AGENTS.md for project-specific conventions

### Step 3: Code Review Focus Areas

When reviewing the diffs, focus on:

1. **Correctness & Logic**
   - Are there any obvious bugs or logical errors?
   - Are edge cases handled properly?
   - Do conditionals cover all scenarios?
   - Are there potential null/undefined reference errors?

2. **Security**
   - Are there XSS vulnerabilities (unescaped user input)?
   - Are there SQL injection risks?
   - Are there command injection risks?
   - Is sensitive data being logged or exposed?
   - Are there CSRF vulnerabilities?

3. **Performance**
   - Are there N+1 queries?
   - Are there unnecessary re-renders in UI components?
   - Are there inefficient algorithms or loops?
   - Are large data structures being copied unnecessarily?

4. **Testing**
   - Are there tests for the changed functionality?
   - For critical logic: Are edge cases tested?
   - Are tests actually testing the right thing?

5. **Code Quality**
   - Is the code readable and maintainable?
   - Are variable names clear and descriptive?
   - Is the code DRY (Don't Repeat Yourself)?
   - Are functions/methods too long or complex?

6. **Formatting & Style**
   - Will linters pass?
   - Do all files end with newlines?
   - Are there any debug statements that should be removed?

### Step 4: Review Checklist

Before creating PR, verify:

- [ ] All files end with newline characters
- [ ] No debug code (console.log, binding.pry, debugger, print statements)
- [ ] Tests exist and pass locally
- [ ] Linting passes
- [ ] Changes are focused (not mixing unrelated changes)
- [ ] Breaking changes are documented

### Step 5: Output Format

Present your review with:

1. **Summary Overview**
   - General code quality assessment
   - Severity level (Critical, High, Medium, Low, None)
   - Overall readiness for PR

2. **Issues Table**

Present issues in this format:

| Severity | File:Line | Issue | Suggested Fix |
|----------|-----------|-------|---------------|
| Critical | path/file:42 | Description | Concrete fix suggestion |
| High | path/file:15 | Description | Concrete fix suggestion |
| Medium | path/file:30 | Description | Concrete fix suggestion |
| Low | path/file:10 | Description | Concrete fix suggestion |

**Severity Definitions:**
- **Critical**: Security vulnerabilities, data loss risks, breaking changes
- **High**: Bugs that will cause failures, major logic errors
- **Medium**: Code quality issues, performance concerns, missing tests
- **Low**: Style issues, minor improvements, nitpicks

3. **Recommendations**
   - Suggest follow-up tasks or improvements
   - Highlight areas that need tests
   - Note any architectural concerns

4. **Positive Feedback**
   - Highlight well-written code
   - Note good practices followed

### Step 6: Pre-PR Actions

After the review, offer to:

1. **Fix Issues**: Offer to fix identified issues directly
2. **Run Tests**: Run relevant tests to verify changes
3. **Run Linting**: Run linters and fix violations
4. **Create PR**: Offer to push and create the PR

## Important Notes

- **Don't be overly pedantic** - focus on issues that matter
- **Be constructive** - always suggest solutions, not just problems
- **Respect existing patterns** - don't suggest changes just to match your preference
- **If the PR looks good, say so!** Don't manufacture issues.
