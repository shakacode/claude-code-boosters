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

### `/review-all-prs`

Review all open PRs or a specific PR with comprehensive code analysis and post reviews directly to GitHub.

**Usage:**
- `/review-all-prs` - Review all open PRs and post comments
- `/review-all-prs 347` - Review only PR #347 and post comment
- `/review-all-prs --dry-run` - Review all PRs without posting (preview mode)
- `/review-all-prs 347 --dry-run` - Review PR #347 without posting

**Features:**
- Comprehensive analysis: code quality, security, performance, testing, documentation
- Structured review format matching GitHub Actions Claude Code reviews
- Dry-run mode for testing before posting publicly
- Rate limiting warnings for batch reviews
- Error handling for common issues

**Installation:**

Copy `commands/review-all-prs.md` to your `~/.claude/commands/` directory:

```bash
cp commands/review-all-prs.md ~/.claude/commands/
```

**Documentation:**
- Full usage guide: [docs/review-all-prs-usage.md](docs/review-all-prs-usage.md)
- Example review: [docs/sample-review-output.md](docs/sample-review-output.md)

## Scripts

### `bin/chrome-mcp`

Launches Google Chrome with a separate profile configured for MCP (Model Context Protocol) browser debugging. This keeps your MCP/debugging session isolated from your regular browsing.

**Features:**
- Dedicated Chrome profile for MCP tools
- Remote debugging enabled on port 9222
- Default URL or custom URL support
- Clean separation from regular Chrome browsing

**Usage:**

```bash
# Launch with default URL
./bin/chrome-mcp

# Launch with custom URL
./bin/chrome-mcp https://example.com
```

**Installation:**

Copy to your local bin directory and make it executable:

```bash
cp bin/chrome-mcp ~/bin/chrome-mcp
chmod +x ~/bin/chrome-mcp
```

**What it does:**
- Creates/uses a dedicated Chrome profile at `~/Library/Application Support/Google/Chrome-MCP-Profile`
- Enables remote debugging on `localhost:9222`
- Opens Chrome in a separate window that can be controlled via MCP browser tools in Conductor
- Displays helpful debugging information and verification commands

**Verify it's working:**

```bash
curl http://localhost:9222/json/version
```

You should see JSON output with Chrome debugging information.

## Contributing

Add new commands, agents, or skills by creating appropriately named markdown files in their respective directories.
