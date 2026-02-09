# Custom Agents and Skills

Claude Code supports custom agents (autonomous workers) and skills (slash commands with enhanced capabilities). Both extend Claude Code's functionality for your team's specific workflows.

## Custom Agents

Agents are autonomous AI assistants that run in their own context with custom system prompts, specific tool access, and independent permissions.

### Built-in Agent Types

| Agent | Model | Purpose |
|-------|-------|---------|
| **Explore** | Haiku (fast) | Read-only codebase search and exploration |
| **Plan** | Inherits | Research and planning in plan mode |
| **General-purpose** | Inherits | All tools, complex multi-step tasks |

### Creating Custom Agents

Store agent files at:
- `~/.claude/agents/` -- Personal agents (all projects)
- `.claude/agents/` -- Project agents (shared via git)

**Example: PR Testing Agent** (`.claude/agents/pr-testing-agent.md`):

```markdown
---
name: pr-testing-agent
description: Run tests for PR changes and report results
tools: Read, Grep, Glob, Bash
model: sonnet
maxTurns: 30
---
You test pull request changes. Given a PR number:

1. Get changed files: `gh pr diff <number> --name-only`
2. Identify corresponding test files
3. Run only the relevant tests
4. Report results with pass/fail summary
```

### Agent Frontmatter Reference

```yaml
---
name: my-agent              # Identifier (required)
description: What it does    # Shown in agent selection
tools: Read, Grep, Bash     # Allowed tools (comma-separated)
model: sonnet                # Model override (sonnet, haiku, opus)
permissionMode: default      # Permission handling
maxTurns: 50                 # Max conversation turns
memory: user                 # Persistent memory (see below)
skills:                      # Skills this agent can use
  - api-conventions
---
```

### Persistent Agent Memory

Agents can maintain memory across sessions:

```yaml
memory: user     # Stored at ~/.claude/agent-memory/<name>/
memory: project  # Stored at .claude/agent-memory/<name>/
memory: local    # Stored at .claude/agent-memory-local/<name>/
```

This is useful for agents that learn project patterns over time, like a code reviewer that remembers past feedback.

## Skills (Slash Commands)

Skills are the evolution of custom commands. Any file at `.claude/commands/foo.md` or `.claude/skills/foo/SKILL.md` creates the `/foo` slash command.

### Simple Command (Single File)

Place a markdown file in `.claude/commands/` or `~/.claude/commands/`:

```markdown
---
description: Generate a migration for a new table
---
Generate a database migration for the table described by the user.
Use the project's migration tool and naming conventions.
```

### Skill Directory (Multi-File)

For more complex skills, use a directory structure:

```
.claude/skills/
  generate-api/
    SKILL.md           # Main instructions (required)
    template.md        # Template for Claude to fill in
    examples/
      sample.md        # Example output
    scripts/
      validate.sh      # Script Claude can execute
```

### Skill Locations

| Location | Path | Scope |
|----------|------|-------|
| Personal | `~/.claude/skills/<name>/SKILL.md` | All your projects |
| Project | `.claude/skills/<name>/SKILL.md` | This project |
| Plugin | `<plugin>/skills/<name>/SKILL.md` | Where plugin enabled |

### Skill Frontmatter Reference

```yaml
---
name: my-skill
description: What this skill does
disable-model-invocation: true    # Only user can invoke (not Claude)
user-invocable: false             # Only Claude can invoke (not user)
allowed-tools: Read, Grep, Glob   # Restrict available tools
model: sonnet                     # Override model
context: fork                     # Run in isolated subagent
agent: Explore                    # Which subagent type to use
---
```

### String Substitutions

Use these variables in skill content:

| Variable | Description |
|----------|-------------|
| `$ARGUMENTS` | All arguments the user passed |
| `$ARGUMENTS[0]`, `$1` | First argument |
| `$ARGUMENTS[1]`, `$2` | Second argument |
| `${CLAUDE_SESSION_ID}` | Current session ID |

### Dynamic Context Injection

Run shell commands before skill content is sent to Claude using `` !`command` `` syntax:

```markdown
---
description: Review current PR
---

## PR Context
- PR diff: !`gh pr diff`
- Changed files: !`gh pr diff --name-only`
- PR info: !`gh pr view --json title,body,author`

## Instructions
Review the PR changes above for quality, security, and correctness.
```

### Running Skills in Subagents

Add `context: fork` to run a skill in an isolated subagent context. This protects your main conversation from large output:

```yaml
---
name: deep-research
description: Thorough codebase research
context: fork
agent: Explore
---
Research $ARGUMENTS thoroughly across the entire codebase.
Report findings organized by relevance.
```

## Agent Teams (Experimental)

Agent teams coordinate multiple Claude Code instances working together. Enable with:

```bash
export CLAUDE_CODE_EXPERIMENTAL_AGENT_TEAMS=1
```

Key concepts:
- **Team lead** coordinates and delegates work
- **Teammates** work independently on assigned tasks
- **Shared task list** for self-coordination
- **Mailbox** for inter-agent messaging

Display modes:
- `in-process` -- All in main terminal (Shift+Up/Down to navigate)
- `tmux` -- Split panes (requires tmux or iTerm2)

### When to Use What

| Approach | Best for |
|----------|----------|
| **Subagents** | Focused tasks where you need the result back (research, file search) |
| **Skills** | Repeatable workflows triggered by slash commands |
| **Agent Teams** | Complex work requiring multiple agents to collaborate |
| **Hooks** | Automated quality gates and lifecycle events |

## Tips

- **Start simple.** A single-file command in `.claude/commands/` is often enough.
- **Use `context: fork`** for skills that generate lots of output to keep your main context clean.
- **Choose the right model.** Use `haiku` for fast read-only tasks, `sonnet` for balanced work, keep `opus` for the hardest problems.
- **Share project agents via git** in `.claude/agents/` so the whole team benefits.
