# Project: claudecode-enhancements

Custom slash commands and configurations for Claude Code.

## Structure
- `commands/` — Claude Code slash commands organized by namespace
  - `dp/` — Personal namespace for custom commands

## Rules
- Command files use frontmatter for `allowed-tools` and `description`
- Keep commands focused on a single task
- Commands should be self-documenting with clear task descriptions

## Adding Commands
1. Create `commands/<namespace>/<command-name>.md`
2. Add frontmatter with `description` and optionally `allowed-tools`
3. Write the prompt template

## Domain
- **Slash commands**: Markdown files that expand to prompts when invoked via `/namespace:command`
- **Namespace**: Directory under `commands/` grouping related commands (e.g., `dp`)
