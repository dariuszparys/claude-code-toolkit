# claudecode-enhancements

Custom slash commands for [Claude Code](https://docs.anthropic.com/en/docs/claude-code).

## Installation

Symlink the commands directory to your Claude Code configuration:

```bash
ln -s /path/to/claudecode-enhancements/commands ~/.claude/commands
```

Or copy individual command namespaces:

```bash
cp -r commands/dp ~/.claude/commands/
```

## Available Commands

### `dp` namespace

| Command | Description |
|---------|-------------|
| `/dp:create-claude-md` | Generate or update CLAUDE.md following best practices |

## Usage

After installation, commands are available in any Claude Code session:

```
/dp:create-claude-md
```

## Creating New Commands

1. Create a markdown file in `commands/<namespace>/<command-name>.md`
2. Add frontmatter:
   ```yaml
   ---
   description: Short description shown in command list
   allowed-tools: Tool1, Tool2  # Optional: restrict available tools
   ---
   ```
3. Write the prompt template below the frontmatter

### Example

```markdown
---
description: Run tests and fix failures
allowed-tools: Bash, Read, Edit
---

# Task: Fix Failing Tests

Run the test suite and fix any failures you find.
```
