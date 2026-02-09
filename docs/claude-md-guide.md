# Writing Effective CLAUDE.md Files

CLAUDE.md is the primary way to give Claude Code persistent instructions for your project. Think of it as a concise briefing document -- not a manual, but the things Claude can't figure out by reading your code.

## Memory Hierarchy

Claude Code loads instructions from multiple sources, in priority order:

| Priority | Location | Scope | Shared via |
|----------|----------|-------|------------|
| 1 (highest) | Managed policy (`/Library/Application Support/ClaudeCode/CLAUDE.md` on macOS) | All org users | IT/admin |
| 2 | Project (`./CLAUDE.md` or `./.claude/CLAUDE.md`) | Everyone on the project | Git |
| 3 | Project rules (`./.claude/rules/*.md`) | Everyone on the project | Git |
| 4 | User (`~/.claude/CLAUDE.md`) | All your projects | Not shared |
| 5 | Project local (`./CLAUDE.local.md`) | You, this project only | Auto-gitignored |
| 6 (lowest) | Auto memory (`~/.claude/projects/<project>/memory/`) | You, this project only | Not shared |

**Practical split:**
- **Project CLAUDE.md**: Build commands, test runners, lint commands, branch naming, PR conventions, architectural decisions
- **User CLAUDE.md**: Personal git preferences, editor habits, coding style preferences
- **CLAUDE.local.md**: Machine-specific paths, local API keys references, personal workarounds
- **`.claude/rules/`**: Topic-specific rules that you don't want cluttering the main file

## What to Include

Ask yourself: **"Would removing this line cause Claude to make a mistake?"** If not, cut it.

**Good candidates:**
- Build/test/lint commands Claude can't guess (`bundle exec rspec`, `pnpm test:e2e`)
- Code style rules that differ from language defaults
- Branch naming conventions (`jg/1234-description`)
- PR conventions (small PRs, issue linking with `Fixes #NNN`)
- Architectural decisions ("we use server components for X, client for Y")
- Common gotchas ("never import from `internal/` directly")
- Environment quirks ("use Node 20, not 22")

**Don't include:**
- Standard language conventions (Claude already knows them)
- Things obvious from package.json, Gemfile, etc.
- Detailed API documentation (link to it instead)
- Long tutorials or explanations
- Information that changes frequently

## File Imports

CLAUDE.md files can reference other files using `@path/to/file`:

```markdown
# Project Overview
See @README.md for architecture and @docs/api.md for endpoint docs.

# Workflow
Follow the git process in @docs/git-workflow.md
```

- Relative and absolute paths supported
- Max depth of 5 hops for recursive imports
- Great for keeping CLAUDE.md concise while linking to detailed docs

## Path-Specific Rules

Create `.claude/rules/*.md` files with YAML frontmatter to scope rules to specific files:

```yaml
# .claude/rules/api-standards.md
---
paths:
  - "src/api/**/*.ts"
---
# API Development Rules
- All endpoints must validate input with zod schemas
- Return consistent error shapes: { error: string, code: number }
- Log all mutations with structured logging
```

```yaml
# .claude/rules/test-conventions.md
---
paths:
  - "**/*_spec.rb"
  - "**/*.test.ts"
---
# Test Conventions
- Use factories, not fixtures
- One assertion per test when possible
- Name tests: "it does X when Y"
```

Rules without `paths` frontmatter apply globally (same as putting them in CLAUDE.md).

## Cross-Tool Compatibility: AGENTS.md

If your team uses multiple AI coding tools (Codex CLI, Cursor, etc.), consider maintaining an `AGENTS.md` alongside `CLAUDE.md`:

- **CLAUDE.md** -- Claude Code-specific instructions (tool names, slash commands, Claude-specific features)
- **AGENTS.md** -- Universal instructions any AI tool should follow (architecture, conventions, testing)

Codex CLI reads `AGENTS.md` by default. Cursor reads `.cursorrules`. Having a shared `AGENTS.md` means your core project rules work across all tools.

See [claude-code-with-codex.md](claude-code-with-codex.md) for more on multi-tool workflows.

## Example CLAUDE.md Template

See [../templates/CLAUDE.md](../templates/CLAUDE.md) for a starter template you can adapt for new projects.

## Tips

- **Keep it under 100 lines.** If it's longer, split into `.claude/rules/` files.
- **Be specific.** "Use vitest, not jest" beats "use the right test framework."
- **Use imperative mood.** "Run `pnpm lint` before committing" not "You should consider running lint."
- **Update it when you learn.** If Claude keeps making the same mistake, add a rule.
- **Review it quarterly.** Remove rules that are no longer relevant.
