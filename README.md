# claude-code-toolkit

A Claude Code plugin marketplace with custom commands, skills, and hooks.

## Installation

```bash
# Add this repo as a marketplace
/plugin marketplace add dariuszparys/claude-code-toolkit

# List available plugins
/plugin

# Install the plugins you want
/plugin install work-items      # Platform-agnostic work item tools
/plugin install azure-boards    # Azure DevOps integration (requires setup)
/plugin install dp-tools        # CLAUDE.md and rules generators
/plugin install adr             # Architecture Decision Records tooling
/plugin install ralph-prep      # Ralph Wiggum Method: autonomous AI coding loops
```

## Available Plugins

| Plugin | Description |
|--------|-------------|
| `work-items` | Analyze, optimize, and break down work items for LLM-assisted development |
| `azure-boards` | Azure DevOps Boards integration with `az boards` CLI |
| `dp-tools` | Tools for creating CLAUDE.md files and Claude Code rules |
| `adr` | Architecture Decision Records: create ADRs, suggest documenting decisions, auto-lint and regenerate index |
| `ralph-prep` | Ralph Wiggum Method: Generate artifacts for autonomous AI coding loops that can ship features while you sleep |

---

## Plugin: work-items

Platform-agnostic tools for analyzing, optimizing, and breaking down work items.

### Commands

| Command | Description |
|---------|-------------|
| `/work-items:analyze` | Check if a work item is ready for development |
| `/work-items:optimize` | Transform work items into token-efficient LLM format |
| `/work-items:tasks` | Generate implementation task breakdown |

### Skills (Auto-invoked)

| Skill | Description |
|-------|-------------|
| `work-items` | Platform-agnostic optimization patterns for any issue tracker |

### Example Usage

```
# Analyze a work item for completeness
/work-items:analyze https://github.com/org/repo/issues/123

# Optimize for LLM context
/work-items:optimize

# Generate tasks from a work item
/work-items:tasks 12345
```

---

## Plugin: azure-boards

Azure DevOps Boards integration for working with PBIs, Bugs, and Tasks.

> **Note:** This plugin requires setup. See [plugins/azure-boards/README.md](plugins/azure-boards/README.md) for prerequisites.

### Prerequisites

- Azure CLI installed
- Azure DevOps CLI extension (`az extension add --name azure-devops`)
- Personal Access Token (PAT) with Work Items: Read & Write scope
- Default organization and project configured

### Skills (Auto-invoked)

| Skill | Description |
|-------|-------------|
| `azure-boards` | Azure DevOps-specific with `az boards` CLI integration |

### Quick Setup

```bash
# Install Azure DevOps extension
az extension add --name azure-devops

# Configure defaults
az devops configure --defaults \
  organization=https://dev.azure.com/<your-org> \
  project=<your-project>

# Authenticate (choose one)
export AZURE_DEVOPS_EXT_PAT=<your-pat>
# or
az login
```

### Example Usage

Once configured, Claude will automatically use Azure Boards commands:

```
User: "Show me PBI 12345"
Claude: [Uses az boards work-item show --id 12345]

User: "What's assigned to me in Sprint 3?"
Claude: [Uses az boards query with WIQL]
```

---

## Plugin: dp-tools

Tools for creating CLAUDE.md files and Claude Code rules.

### Commands

| Command | Description |
|---------|-------------|
| `/dp:create-claude-md` | Generate or update CLAUDE.md for AI agent onboarding |
| `/dp:create-rules` | Create modular rules files in `.claude/rules/` |

### `/dp:create-claude-md`

Generates or updates a `CLAUDE.md` file following best practices.

- Auto-detects context (repo root vs subdirectory)
- Explores directory structure and existing documentation
- Root files: 60-100 lines covering stack, commands, structure, rules, domain
- Subdirectory files: 30-50 lines with package-specific additions only

### `/dp:create-rules`

Creates modular Claude Code rules files for larger projects.

| Use Case | Approach |
|----------|----------|
| Project overview, stack, commands | Use `/dp:create-claude-md` |
| Path-specific conventions (API, tests) | Use `/dp:create-rules` |
| Large project with many conventions | Use `/dp:create-rules` |

Example output:
```
.claude/
├── CLAUDE.md           # Main project memory
└── rules/
    ├── typescript.md   # Language conventions
    ├── testing.md      # Testing standards
    └── api.md          # API-specific (paths: src/api/**)
```

---

## Plugin: adr

Architecture Decision Records tooling. Create ADRs with MADR template, get suggestions for documenting decisions, and automatically lint and regenerate index.

> **Note:** Hooks require project-level dependencies. See [plugins/adr/README.md](plugins/adr/README.md) for setup.

### Commands

| Command | Description |
|---------|-------------|
| `/adr:create` | Create a new ADR using the MADR template |

### Skills (Auto-invoked)

| Skill | Description |
|-------|-------------|
| `adr` | Suggests documenting significant architectural decisions as ADRs |

### Hooks (Auto-activated)

| Hook | Trigger | Action |
|------|---------|--------|
| ADR Linting | `docs/adr/*.md` | Auto-fix markdown, block on errors |
| ADR Log | `docs/adr/NNNN-*.md` | Regenerate ADR index |

### Prerequisites for Hooks

Add to your project's `package.json`:

```json
{
  "devDependencies": {
    "adr-log": "^2.2.0",
    "markdownlint-cli": "^0.47.0"
  }
}
```

### Example

```
# Create a new ADR
/adr:create

# When Claude detects an architectural decision:
Claude: "This introduces a new caching layer. Consider documenting this with /adr:create"

# After editing ADR files, hooks run automatically:
# Hook 1: markdownlint --fix
# Hook 2: adr-log regenerates index
```

---

## Plugin: ralph-prep

Ralph Wiggum Method tooling for autonomous AI coding loops. Generate artifacts that allow AI agents to ship features autonomously while you sleep.

### Commands

| Command | Description |
|---------|-------------|
| `/ralph:generate` | Generate Ralph Wiggum Method artifacts (interactive or template-based) |

### Skills (Auto-invoked)

| Skill | Description |
|-------|-------------|
| `ralph` | Invoked when setting up autonomous AI coding loops |

### What It Generates

The `/ralph:generate` command creates 6 artifacts in your project:

1. **`prd.json`** - Product requirements with atomic user stories
2. **`ralph.sh`** - Autonomous loop execution script
3. **`prompt.md`** - Instructions for the AI agent
4. **`progress.txt`** - Story completion tracking
5. **`AGENTS.md`** - Learning capture from each iteration
6. **`README.md`** - Setup and usage guide

### Key Features

- **Atomic Stories**: One iteration = One story = One commit model
- **Dependency Ordering**: Stories sequenced by dependencies
- **Interactive Mode**: Guided artifact generation with prompts
- **Template Mode**: Use existing files as templates
- **Progress Tracking**: Track story completion automatically
- **Learning Capture**: Document insights between iterations
- **Opus-Powered**: Uses Claude Opus 4.5 for generation

### Example Usage

```
# Generate Ralph artifacts interactively
/ralph:generate

# Or use template files
/ralph:generate path/to/template.json
```

The generated `ralph.sh` script runs autonomous coding loops:
```bash
./ralph.sh  # Ship features while you sleep
```

---

## Repository Structure

```
claude-code-toolkit/
├── .claude-plugin/
│   └── marketplace.json       # Lists available plugins
├── plugins/
│   ├── work-items/            # Platform-agnostic work item tools
│   │   ├── .claude-plugin/
│   │   │   └── plugin.json
│   │   ├── commands/
│   │   │   └── work-items/
│   │   ├── skills/
│   │   │   └── work-items/
│   │   └── README.md
│   ├── azure-boards/          # Azure DevOps integration
│   │   ├── .claude-plugin/
│   │   │   └── plugin.json
│   │   ├── skills/
│   │   │   └── azure-boards/
│   │   └── README.md
│   ├── dp-tools/              # CLAUDE.md and rules tools
│   │   ├── .claude-plugin/
│   │   │   └── plugin.json
│   │   ├── commands/
│   │   │   └── dp/
│   │   └── README.md
│   ├── ralph-prep/            # Ralph Wiggum Method artifacts
│   │   ├── .claude-plugin/
│   │   │   └── plugin.json
│   │   ├── commands/
│   │   │   └── ralph/
│   │   ├── skills/
│   │   │   └── ralph/
│   │   └── README.md
│   └── adr/                   # ADR tooling with hooks
│       ├── .claude-plugin/
│       │   └── plugin.json
│       ├── commands/
│       │   └── adr/
│       ├── skills/
│       │   └── adr/
│       └── README.md
└── README.md
```
