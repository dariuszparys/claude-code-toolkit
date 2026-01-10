# Ralph Prep

Generate Ralph Wiggum Method artifacts for autonomous AI coding loops.

## Overview

This plugin helps you set up the Ralph Wiggum Method - an autonomous AI coding loop that ships features while you sleep. It generates a complete set of artifacts needed to run the loop from your shell.

## Commands

### `/ralph:generate`

Generate Ralph artifacts for a task.

```bash
# Interactive mode - Claude asks questions step by step
/ralph:generate ./my-feature

# From template file
/ralph:generate --from-file task.yaml ./my-feature
```

## Generated Artifacts

| File | Purpose |
|------|---------|
| `prd.json` | User stories decomposed into atomic, single-iteration tasks |
| `ralph.sh` | Loop runner script for Claude Code CLI |
| `prompt.md` | Task-specific AI prompt context |
| `progress.txt` | Iteration tracking and completed story log |
| `AGENTS.md` | Agent instructions for the autonomous loop |
| `README.md` | Usage guide for the generated artifacts |

## Ralph Wiggum Method

The Ralph Wiggum Method follows these principles:

- **One iteration = One story = One commit**
- Each story is completable in a single AI iteration (~5-30 min)
- Stories are ordered by dependency (infrastructure first, consumers last)
- Every story ends with build/test verification
- The loop runs autonomously until all stories pass

## Template File Format

For `--from-file` mode, create a YAML file:

```yaml
task:
  name: my-feature
  description: Brief description of what needs to be built
  branch: ralph/my-feature

prior_work:
  - Existing code or patterns to build on
  - Previous PRs or commits

constraints:
  - Technical limitations
  - Dependencies

scope:
  - "[ ] Deliverable 1"
  - "[ ] Deliverable 2"
  - "[ ] Tests updated"

key_paths:
  source: path/to/source/
  tests: path/to/tests/
  config: path/to/config/

gotchas:
  - Critical edge cases
  - Known pitfalls
```

## Running the Loop

After generating artifacts:

```bash
cd ./my-feature
chmod +x ralph.sh
./ralph.sh
```

The loop will:
1. Read the current story from `prd.json` and `progress.txt`
2. Implement the story using Claude Code
3. Commit changes
4. Update `progress.txt`
5. Repeat until all stories complete
