# Project: [PROJECT_NAME]

## Build & Run

```bash
# Install dependencies
[INSTALL_COMMAND]

# Run development server
[DEV_COMMAND]

# Build for production
[BUILD_COMMAND]
```

## Test

```bash
# Run all tests
[TEST_COMMAND]

# Run specific test file
[TEST_SINGLE_COMMAND]

# Run tests matching a pattern
[TEST_PATTERN_COMMAND]
```

## Lint & Format

```bash
# Lint and autofix
[LINT_COMMAND]

# Format
[FORMAT_COMMAND]
```

Always lint changed files before committing.

## Git Workflow

- Branch naming: `<initials>/<issue-number>-<short-description>` (e.g., `jg/123-fix-login-redirect`)
- Always create feature branches, never push directly to main
- Include `Fixes #NNN` in PR descriptions to auto-close issues
- Keep PRs small and focused -- suggest splitting if scope grows
- Always `git pull --rebase` before making changes on a branch

## Code Style

- [Add project-specific style rules that differ from language defaults]
- [e.g., "Use named exports, not default exports"]
- [e.g., "Prefer composition over inheritance"]
- All files must end with a newline character

## Architecture

- [Brief description of project structure]
- [e.g., "src/api/ -- Express route handlers"]
- [e.g., "src/services/ -- Business logic, no HTTP concerns"]
- [e.g., "src/models/ -- Database models (Prisma)"]

## Testing

- [Test runner and conventions]
- [e.g., "Use factories from test/factories/, not inline data"]
- [e.g., "When changing source files, run corresponding test files"]

## Common Gotchas

- [Things that trip up new developers or AI assistants]
- [e.g., "Node 20 required -- Node 22 breaks native dependencies"]
- [e.g., "Must run `pnpm db:migrate` after pulling new migrations"]
