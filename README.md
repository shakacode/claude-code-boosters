# claude-code-boosters

ShakaCode Team Shared Claude Code Commands, Agents, Skills, Tips

## About

This repository contains shared resources for the ShakaCode team to enhance their Claude Code workflows:

- **Commands**: Custom slash commands for common development tasks
- **Agents**: Specialized agents for specific workflows
- **Skills**: Reusable skill definitions
- **Tips**: Best practices and workflow optimizations

## Commands

### `/address-review`

Fetch GitHub PR review comments and create todos to address them.

**Usage:**
- `/address-review 12345` - Fetch all comments from PR #12345
- `/address-review https://github.com/org/repo/pull/12345` - Use full PR URL
- `/address-review https://github.com/org/repo/pull/12345#pullrequestreview-123456789` - Fetch specific review

**Installation:**

Copy `commands/address-review.md` to your `~/.claude/commands/` directory:

```bash
cp commands/address-review.md ~/.claude/commands/
```

## Contributing

Add new commands, agents, or skills by creating appropriately named markdown files in their respective directories.
