# claudecode-enhancements

Custom slash commands and hooks for [Claude Code](https://docs.anthropic.com/en/docs/claude-code).

## Installation

Symlink the commands directory to your Claude Code configuration:

```bash
ln -s /path/to/claudecode-enhancements/commands ~/.claude/commands
```

Or copy individual command namespaces:

```bash
cp -r commands/dp ~/.claude/commands/
```

To use the hooks, copy `settings.json` to your project:

```bash
cp settings.json /path/to/your/project/.claude/settings.json
```

## Command: `/dp:create-claude-md`

Generates or updates a `CLAUDE.md` file following best practices for AI agent onboarding.

**What it does:**

- Auto-detects context (repo root vs subdirectory)
- Explores directory structure and existing documentation
- Generates concise, actionable CLAUDE.md content
- Root files: 60-100 lines covering stack, commands, structure, rules, domain
- Subdirectory files: 30-50 lines with package-specific additions only

**Usage:**

```
/dp:create-claude-md
```

## Hook: ADR Markdown Linting

A `PostToolUse` hook that enforces markdown standards on Architecture Decision Records.

**Behavior:**

- Triggers on `Edit` or `Write` operations
- Only affects files matching `docs/adr/*.md`
- Auto-fixes issues with `markdownlint --fix`
- Blocks the operation if unfixable lint errors remain

## Prerequisites

The ADR linting hook requires:

| Tool | Install |
|------|---------|
| `markdownlint-cli` | `npm install -g markdownlint-cli` |
| `jq` | `brew install jq` or `apt-get install jq` |
