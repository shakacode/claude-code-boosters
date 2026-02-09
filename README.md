# claude-code-boosters

ShakaCode Team Shared Claude Code Commands, Agents, Skills, and Tips.

## Quick Install

Copy what you need to your `~/.claude/` directory:

```bash
# Clone the repo
git clone https://github.com/shakacode/claude-code-boosters.git
cd claude-code-boosters

# Install all commands
cp commands/*.md ~/.claude/commands/

# Install all agents
mkdir -p ~/.claude/agents
cp agents/*.md ~/.claude/agents/

# Or install specific ones
cp commands/self-review.md ~/.claude/commands/
cp commands/merge-commit-msg.md ~/.claude/commands/
```

For project-level sharing, copy to your project's `.claude/commands/` or `.claude/agents/` directory instead.

## Commands

| Command | Description |
|---------|-------------|
| [`/address-review`](commands/address-review.md) | Fetch GitHub PR review comments and create todos to address them |
| [`/review-all-prs`](commands/review-all-prs.md) | Review all open PRs or a specific PR, post reviews to GitHub |
| [`/self-review`](commands/self-review.md) | Comprehensive self-review of uncommitted changes before creating a PR |
| [`/merge-commit-msg`](commands/merge-commit-msg.md) | Generate a structured merge commit message from PR changes |
| [`/optimize`](commands/optimize.md) | Analyze code for performance issues with structured recommendations |
| [`/security-review`](commands/security-review.md) | Review code for security vulnerabilities (OWASP Top 10 checklist) |

## Agents

| Agent | Description |
|-------|-------------|
| [`pr-testing-agent`](agents/pr-testing-agent.md) | Identify and run only the tests affected by PR changes |

Install to `~/.claude/agents/` (personal) or `.claude/agents/` (project).

## Templates

Starter templates for new projects:

| Template | Description |
|----------|-------------|
| [`CLAUDE.md`](templates/CLAUDE.md) | Project-level Claude Code instructions template |
| [`AGENTS.md`](templates/AGENTS.md) | Cross-tool AI agent instructions template (works with Codex CLI, Cursor, etc.) |

## Documentation

| Guide | Description |
|-------|-------------|
| [Writing Effective CLAUDE.md](docs/claude-md-guide.md) | Memory hierarchy, what to include/exclude, path-specific rules, imports |
| [Custom Agents and Skills](docs/custom-agents-and-skills.md) | Creating custom agents, skills, slash commands, and agent teams |
| [Hooks Guide](docs/hooks-guide.md) | Lifecycle hooks for automation, quality gates, and safety |
| [Claude Code + Codex CLI](docs/claude-code-with-codex.md) | Using both tools together, shared instructions, workflows |
| [Tips and Tricks](docs/tips-and-tricks.md) | Context management, parallel sessions, effective prompting, common pitfalls |

## Scripts

| Script | Description |
|--------|-------------|
| [`bin/chrome-mcp`](bin/chrome-mcp) | Launch Chrome with a separate profile for MCP browser debugging |

## GitHub Actions

| Workflow | Description |
|----------|-------------|
| [Claude Code Review](.github/workflows/claude-code-review.yml) | Automatic PR review on open/sync |
| [Claude Code](.github/workflows/claude.yml) | Respond to `@claude` mentions in issues/PRs |

## Contributing

1. Add new commands to `commands/`
2. Add new agents to `agents/`
3. Add documentation to `docs/`
4. Keep everything generic -- project-specific instructions belong in project CLAUDE.md files
5. All files must end with a newline character
