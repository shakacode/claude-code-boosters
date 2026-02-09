# Using Claude Code with Codex CLI

This guide covers how to use Anthropic's Claude Code and OpenAI's Codex CLI together effectively. Both are terminal-based AI coding agents, and they complement each other well.

## What Is Codex CLI?

[Codex CLI](https://github.com/openai/codex) is OpenAI's open-source (Apache 2.0) coding agent that runs in your terminal. First released April 2025, it's built in Rust and supports GPT models optimized for coding.

**Install:**

```bash
# npm
npm install -g @openai/codex

# Homebrew (macOS)
brew install --cask codex
```

**Authenticate** via ChatGPT sign-in (works with existing Plus/Pro/Team plans) or API key.

## Key Differences

| | Claude Code | Codex CLI |
|---|---|---|
| **Models** | Claude Opus 4.6, Sonnet 4.5 | GPT-5.3-Codex, GPT-5.2-Codex |
| **Instructions file** | `CLAUDE.md` | `AGENTS.md` |
| **Sandbox** | macOS Seatbelt, configurable | macOS Seatbelt + Linux Landlock/seccomp |
| **Approval modes** | Permission rules in settings.json | `--approval-mode auto/read-only/full-access` |
| **Multi-agent** | Subagents, agent teams (experimental) | Single agent (parallelize manually) |
| **Slash commands** | Skills/commands system | Not built-in |
| **License** | Proprietary | Apache 2.0 |
| **Config** | `~/.claude/settings.json` | `~/.codex/config.toml` |

## Strengths-Based Task Delegation

Based on developer benchmarks and real-world usage:

| Task | Better Tool | Why |
|------|------------|-----|
| Rapid prototyping | Claude Code | Faster output, better at large implementations |
| Complex debugging | Claude Code | Stronger at multi-step reasoning chains |
| UI/frontend code | Claude Code | Better at React, CSS, visual components |
| Test-driven development | Claude Code | Subagent support helps run tests in parallel |
| Code review | Codex CLI | Thorough analysis, good at catching subtle issues |
| Large refactors | Codex CLI | Context compression handles sprawling changes |
| ML/data science | Codex CLI | Stronger domain knowledge |
| CI/CD pipelines | Either | Both handle YAML/config well |

These are generalizations -- both tools are highly capable across all tasks. Use whichever you're more fluent with.

## Shared Instructions: AGENTS.md Strategy

The key to using both tools on the same project is a shared instructions file.

### Option 1: AGENTS.md as the Single Source of Truth

Put universal instructions in `AGENTS.md` and configure both tools to read it:

**For Codex CLI:** Reads `AGENTS.md` automatically.

**For Claude Code:** Add a fallback in `CLAUDE.md`:

```markdown
# Project Instructions
See @AGENTS.md for build commands, architecture, and conventions.

# Claude Code-Specific
- Use `/self-review` before creating PRs
- Use subagents for codebase exploration
```

### Option 2: Symlink

```bash
ln -s AGENTS.md CLAUDE.md
```

Simple but you lose the ability to have tool-specific instructions.

### Option 3: Separate Files with Shared Core

```
AGENTS.md          # Universal instructions (both tools read this)
CLAUDE.md          # @AGENTS.md + Claude-specific features
```

This is the recommended approach -- it gives you cross-tool compatibility while letting you use tool-specific features.

### Codex Fallback Configuration

Codex can also be configured to read `CLAUDE.md` as a fallback in `~/.codex/config.toml`:

```toml
project_doc_fallback_filenames = ["CLAUDE.md", "COPILOT.md"]
```

## AGENTS.md Discovery

Codex discovers `AGENTS.md` using a cascading hierarchy:

1. **Home directory** (`~/.codex/AGENTS.md`): Global defaults
2. **Project root** → **current directory**: Walks down, reads one file per directory
3. **Override files**: `AGENTS.override.md` takes priority over `AGENTS.md` in the same directory

Files are concatenated from root down. The closest file to the edited code wins for conflicting instructions.

See [templates/AGENTS.md](../templates/AGENTS.md) for a starter template.

## Practical Workflows

### 1. Implement with Claude, Review with Codex

```bash
# Session 1: Claude Code implements the feature
claude
> "Implement the CSV export feature described in issue #234"

# Session 2: Codex reviews the implementation
codex "Review the changes on this branch for bugs, security issues, and test coverage"
```

### 2. Cross-Validation

Ask both tools to solve the same problem independently, then compare approaches:

```bash
# Git worktrees keep both isolated
git worktree add ../feature-claude feature-branch-claude
git worktree add ../feature-codex feature-branch-codex

# Terminal 1
cd ../feature-claude && claude

# Terminal 2
cd ../feature-codex && codex
```

### 3. Parallel Agents on Different Tasks

Use git worktrees to run both tools simultaneously on different tasks:

```bash
git worktree add ../task-auth auth-refactor
git worktree add ../task-api api-endpoints

# Run Claude Code on auth refactor
cd ../task-auth && claude

# Run Codex on API endpoints
cd ../task-api && codex
```

### 4. Spec-First Development

1. Write the spec/tests with one tool
2. Implement with the other
3. The implementation tool can't "cheat" because it didn't write the tests

## Codex CLI Quick Reference

### Approval Modes

```bash
codex --approval-mode auto        # Default: auto-approve workspace changes
codex --approval-mode read-only   # Browse only, ask before changes
codex --approval-mode full-access # Full machine access (use sparingly)
codex --full-auto                 # No prompts, but sandboxed
```

### Configuration (`~/.codex/config.toml`)

```toml
model = "gpt-5.2-codex"
approval_policy = "untrusted"
sandbox_mode = "workspace-write"

[profiles.fast]
model = "gpt-5.2-codex"
approval_policy = "on-request"
```

## Tips

- **Keep AGENTS.md under 150 lines.** Same principle as CLAUDE.md -- long files bury the signal.
- **Wrap commands in backticks** in both files so agents can copy-paste them.
- **Use git worktrees** when running both tools simultaneously to avoid file conflicts.
- **Don't fight the tools.** If one tool is struggling with a task, try the other instead of correcting it repeatedly.
- **Update instructions when things change.** Both tools rely on accurate build/test commands.

## Cost Considerations

- **Codex CLI**: Included with ChatGPT Plus ($20/month) or Pro ($200/month) plans, or pay-per-token via API
- **Claude Code**: Requires Claude Pro ($20/month, limited), Max ($100/month), or API access

For teams doing heavy AI-assisted development, having access to both gives you flexibility to use whichever is faster, cheaper, or better for the specific task at hand.
