# Claude Code Tips and Tricks

Practical tips for getting the most out of Claude Code, collected from real team experience.

## The Single Most Important Thing

**Claude's context window fills up, and performance degrades as it fills.** Everything below flows from this constraint.

## Context Management

### Use `/clear` Between Unrelated Tasks

After finishing a task, hit `/clear` before starting the next one. A clean context beats accumulated corrections every time. If you've been going back and forth with Claude on a problem for several turns without progress, `/clear` and write a better prompt from scratch.

### Delegate Research to Subagents

When you need to investigate something (find usages, understand a pattern, read docs), delegate it instead of doing it in your main session:

> "Use a subagent to find all places where we create database connections and report back the patterns used"

This keeps your main context focused on the actual work.

### Course-Correct Early

- **`Esc`** -- Stop Claude mid-response if it's going the wrong direction
- **`Esc` + `Esc`** or **`/rewind`** -- Undo the last turn entirely
- **`/clear`** -- Nuclear option, fresh start

The cost of letting Claude go down the wrong path for 5 turns is much higher than stopping it after 1.

## Planning Before Coding

### Use Plan Mode for Complex Tasks

Press `Shift+Tab` twice (or use the `/plan` command) to enter plan mode. Claude will research and plan without making changes. This is invaluable for:

- Multi-file refactors
- New feature implementation
- Bug investigation
- Understanding unfamiliar code

The two-phase approach (explore, then implement) produces dramatically better results than diving straight into coding.

### Give Claude a Way to Verify Its Work

This is the highest-leverage habit you can build. Include:

- Test commands to run
- Expected output to check against
- Screenshots for UI work
- URLs to verify against

> "After making the change, run `pnpm test:unit -- --grep 'auth'` and verify all tests pass"

## Parallel Sessions

### Writer/Reviewer Pattern

1. Session 1: Implement the feature
2. Session 2: Review the implementation (use `/self-review`)

Each session has clean context and different "perspective."

### Test-Driven Development

1. Session 1: Write failing tests
2. Session 2: Implement code to make tests pass

### Fan Out with `claude -p`

For batch operations across many files:

```bash
# Add type annotations to all service files
for f in src/services/*.ts; do
  claude -p "Add JSDoc type annotations to all exported functions in $f" &
done
wait
```

## Effective Prompting

### Be Specific About What You Want

Bad: "Fix the login"
Good: "The login form at `src/components/Login.tsx` submits but doesn't redirect to `/dashboard` on success. The API call succeeds (I can see 200 in network tab). Fix the redirect logic."

### Reference Files Directly

Use `@` to reference files in your prompt:

> "Refactor @src/utils/auth.ts to use the new token format described in @docs/auth-spec.md"

### Pipe Context In

```bash
# Give Claude test output to debug
pnpm test 2>&1 | claude -p "These tests are failing. Diagnose and fix."

# Give Claude a specific error
cat /var/log/app.log | tail -50 | claude -p "What's causing these errors?"
```

## Common Failure Patterns

### Kitchen Sink Session

**Problem:** Mixing 5 unrelated tasks in one session.
**Fix:** Use `/clear` between tasks, or open separate sessions.

### Correcting Over and Over

**Problem:** Claude got it wrong, you correct it, it gets it wrong again, you correct again...
**Fix:** After 2 failed corrections, `/clear` and write a better prompt that includes what you want AND what you don't want.

### Over-Specified CLAUDE.md

**Problem:** Your CLAUDE.md is 500 lines and Claude ignores half of it.
**Fix:** Keep it under 100 lines. Move detailed rules to `.claude/rules/` files with path scoping.

### Trust-Then-Verify Gap

**Problem:** Claude says "done!" and you trust it without checking.
**Fix:** Always provide a verification step. "After making changes, run the tests" or "show me the diff."

### Infinite Exploration

**Problem:** You ask Claude to "look into" something and it reads 50 files.
**Fix:** Scope investigations. "Check only the files in `src/auth/`" or use a subagent so it doesn't burn your main context.

## Settings Worth Configuring

### Permission Rules

Pre-approve common operations to avoid constant permission prompts:

```json
{
  "permissions": {
    "allow": [
      "Bash(npm run *)",
      "Bash(pnpm *)",
      "Bash(git status)",
      "Bash(git diff *)",
      "Bash(git log *)"
    ],
    "deny": [
      "Bash(rm -rf *)",
      "Bash(git push --force *)"
    ]
  }
}
```

### Key Environment Variables

| Variable | Purpose |
|----------|---------|
| `ANTHROPIC_MODEL` | Override the default model |
| `MAX_THINKING_TOKENS` | Extended thinking budget |

## Workflow Patterns That Work Well

### PR Workflow

1. Create feature branch
2. Make changes
3. Run `/self-review` to catch issues
4. Fix anything found
5. Run `/merge-commit-msg` to draft commit message
6. Push and create PR

### Code Review Workflow

1. `/review-all-prs` to review open PRs
2. `/review-all-prs 347` for specific PR
3. `/address-review <PR-URL>` to handle review feedback

### Debugging Workflow

1. Paste the error message
2. Ask Claude to diagnose (not fix) first
3. Once root cause is identified, ask for the fix
4. Verify with tests

### Learning a New Codebase

1. Start with plan mode (`Shift+Tab` twice or `/plan`)
2. Ask Claude to explore the architecture
3. Use subagents to investigate specific areas
4. Build a mental model before making changes
