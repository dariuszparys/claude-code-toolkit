# Project: claude-code-toolkit

Claude Code plugin marketplace with custom commands, skills, and hooks.

## Stack
- Claude Code plugins (commands, skills, hooks)
- Markdown with YAML frontmatter
- JSON for plugin manifests

## Structure
- `plugins/` — Individual plugins, each with `.claude-plugin/plugin.json`
  - `work-items/` — Platform-agnostic work item optimization
  - `azure-boards/` — Azure DevOps Boards integration
  - `dp-tools/` — CLAUDE.md and rules generators
  - `adr/` — Architecture Decision Records tooling
- `.claude-plugin/marketplace.json` — Plugin registry for this marketplace

## Commands
- `tree -L 3 plugins/` — View plugin structure

## Rules
- Each plugin lives in `plugins/<name>/` with its own `.claude-plugin/plugin.json`
- Commands go in `plugins/<name>/commands/<namespace>/<cmd>.md`
- Skills go in `plugins/<name>/skills/<namespace>/SKILL.md`
- Hooks are defined in `plugin.json` under the `hooks` key
- Command files use YAML frontmatter for `allowed-tools` and `description`
- Keep commands focused on a single task

## Adding a Plugin
1. Create `plugins/<name>/.claude-plugin/plugin.json` with name, version, description
2. Add commands in `plugins/<name>/commands/<namespace>/<cmd>.md`
3. Add skills in `plugins/<name>/skills/<namespace>/SKILL.md`
4. Register in `.claude-plugin/marketplace.json`

## Domain
- **Plugin**: A package containing commands, skills, and/or hooks
- **Command**: Markdown file that expands to a prompt via `/namespace:command`
- **Skill**: Auto-invoked behavior defined in `SKILL.md`
- **Hook**: Shell command triggered by tool events (PostToolUse, etc.)
- **Marketplace**: A repo with `.claude-plugin/marketplace.json` listing available plugins
