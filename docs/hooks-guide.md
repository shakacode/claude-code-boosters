# Claude Code Hooks

Hooks are user-defined shell commands or LLM prompts that execute automatically at specific lifecycle points in Claude Code. They're configured in `settings.json` and are powerful for enforcing team standards, automating quality checks, and integrating with external tools.

## Hook Events

| Event | When It Fires | Use Cases |
|-------|--------------|-----------|
| `SessionStart` | Session begins or resumes | Load context, set up environment |
| `UserPromptSubmit` | User submits prompt, before processing | Validate input, inject context |
| `PreToolUse` | Before a tool call executes | Block dangerous commands, validate args |
| `PermissionRequest` | Permission dialog appears | Auto-approve known-safe operations |
| `PostToolUse` | After a tool call succeeds | Lint changed files, run tests |
| `PostToolUseFailure` | After a tool call fails | Log failures, suggest fixes |
| `Notification` | When notification is sent | Custom notification routing |
| `SubagentStart` | Subagent is spawned | Track agent activity |
| `SubagentStop` | Subagent finishes | Collect results |
| `Stop` | Claude finishes responding | Quality checks, follow-up prompts |
| `TeammateIdle` | Agent team teammate going idle | Assign new work |
| `TaskCompleted` | Task marked complete | Validation gates |
| `PreCompact` | Before context compaction | Save important context |
| `SessionEnd` | Session terminates | Cleanup, reporting |

## Three Hook Types

### 1. Command Hooks

Run shell commands. Most common type.

```json
{
  "type": "command",
  "command": "/path/to/script.sh",
  "timeout": 30,
  "async": false
}
```

### 2. Prompt Hooks

Single-turn LLM evaluation. Good for judgment-based checks.

```json
{
  "type": "prompt",
  "prompt": "Evaluate whether this change follows our API conventions: $ARGUMENTS"
}
```

### 3. Agent Hooks

Multi-turn subagent with tool access. Most powerful but most expensive.

```json
{
  "type": "agent",
  "prompt": "Review the changes made and verify test coverage is adequate."
}
```

## Configuration

Hooks are configured in `settings.json` files:

| Location | Scope |
|----------|-------|
| `~/.claude/settings.json` | All projects (personal) |
| `.claude/settings.json` | This project (shared via git) |
| `.claude/settings.local.json` | This project (gitignored, personal) |
| Managed policy settings | All org users |

### Basic Structure

```json
{
  "hooks": {
    "EventName": [
      {
        "matcher": "ToolName",
        "hooks": [
          {
            "type": "command",
            "command": "/path/to/script.sh"
          }
        ]
      }
    ]
  }
}
```

The `matcher` field filters which tool triggers the hook. Use `|` for multiple tools: `"Edit|Write"`.

## Practical Examples

### Block Dangerous Commands

`.claude/hooks/block-dangerous.sh`:
```bash
#!/bin/bash
# Block rm -rf, force push, and reset --hard
INPUT=$(cat)
CMD=$(echo "$INPUT" | jq -r '.tool_input.command // empty')

if echo "$CMD" | grep -qE '(rm\s+-rf|push\s+--force|push\s+-f|reset\s+--hard)'; then
  echo "BLOCKED: Dangerous command detected: $CMD" >&2
  exit 2
fi
```

```json
{
  "hooks": {
    "PreToolUse": [
      {
        "matcher": "Bash",
        "hooks": [
          {
            "type": "command",
            "command": ".claude/hooks/block-dangerous.sh"
          }
        ]
      }
    ]
  }
}
```

### Auto-Lint on File Changes

```json
{
  "hooks": {
    "PostToolUse": [
      {
        "matcher": "Edit|Write",
        "hooks": [
          {
            "type": "command",
            "command": ".claude/hooks/lint-changed.sh",
            "async": true,
            "timeout": 120
          }
        ]
      }
    ]
  }
}
```

### Quality Gate on Stop

Use a prompt hook to check if Claude actually finished the task:

```json
{
  "hooks": {
    "Stop": [
      {
        "hooks": [
          {
            "type": "prompt",
            "prompt": "Check if all requested tasks are complete. If not, list what remains."
          }
        ]
      }
    ]
  }
}
```

## Exit Codes

For command hooks:

| Exit Code | Meaning |
|-----------|---------|
| **0** | Success. stdout is parsed for JSON feedback. |
| **2** | Blocking error. stderr is fed back to Claude as an error to fix. |
| **Other** | Non-blocking error. Logged but execution continues. |

## Async Hooks

Set `"async": true` to run hooks in the background without blocking Claude. The output is delivered on the next turn. Good for:

- Long-running lint checks
- Test execution
- External service notifications

## Tips

- **Start with `PreToolUse`** on `Bash` to block dangerous commands. This is the highest-value hook.
- **Use `async: true`** for anything that takes more than a few seconds.
- **Exit code 2** is your friend -- it feeds the error back to Claude so it can self-correct.
- **Keep hooks fast.** Slow hooks degrade the interactive experience.
- **Test hooks manually** before adding them: `echo '{"tool": "Bash", "tool_input": {"command": "rm -rf /"}}' | .claude/hooks/block-dangerous.sh`
- **Share project hooks via git** in `.claude/settings.json` so the whole team gets them.
