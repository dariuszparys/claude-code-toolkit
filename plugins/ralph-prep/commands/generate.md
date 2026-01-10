---
description: Generate Ralph Wiggum Method artifacts for autonomous AI coding
argument-hint: "[--from-file <template.yaml>] <output-dir>"
allowed-tools:
  - Read
  - Write
  - Glob
  - Grep
  - Bash(tree:*)
  - Bash(find:*)
  - Bash(mkdir:*)
  - Bash(ls:*)
  - Bash(date:*)
  - Task
  - AskUserQuestion
---

# Generate Ralph Wiggum Method Artifacts

Generate a complete set of Ralph artifacts for autonomous AI coding loops.

## Arguments

- `--from-file <path>`: Load task details from a YAML template file
- `<output-dir>`: Directory to create artifacts in (required)

Parse arguments from: $ARGUMENTS

## Mode Detection

Check if `--from-file` is in the arguments:
- If yes: Read the template file and extract task details
- If no: Enter interactive mode

---

## Interactive Mode

If no `--from-file` provided, gather information step by step using AskUserQuestion:

### Step 1: Task Basics
Ask for:
1. **Task Name**: Short identifier (used for branch, directory names)
   - Example: "sql-migration-users", "add-auth-endpoint"
2. **Task Description**: 2-3 sentence summary of what needs to be built

### Step 2: Context
Ask for:
3. **Prior Work**: What existing code, patterns, or infrastructure to build on
   - Examples: "Users table exists", "Auth middleware in place"
4. **Constraints**: Technical limitations or requirements
   - Examples: "Must use existing database", "No breaking API changes"

### Step 3: Scope
Ask for:
5. **Scope Checklist**: What IS in scope (bullet points)
   - Example deliverables: Entity, Repository, Tests, Migration
6. **Key Paths**: Important directories or files
   - Source, tests, config, existing patterns to follow

### Step 4: Gotchas
Ask for:
7. **Gotchas**: Critical things the agent must know
   - Edge cases, pitfalls, special handling required

---

## Template File Mode

If `--from-file <path>` provided, read the YAML file:

```yaml
task:
  name: feature-name
  description: What needs to be built
  branch: ralph/feature-name  # optional, defaults to ralph/<name>

prior_work:
  - Existing patterns to build on
  - Previous work completed

constraints:
  - Technical limitations
  - Dependencies

scope:
  - "[ ] Deliverable 1"
  - "[ ] Deliverable 2"

key_paths:
  source: path/to/source/
  tests: path/to/tests/
  config: path/to/config/

gotchas:
  - Critical edge cases
  - Known pitfalls
```

---

## Step 5: Create Output Directory

```bash
mkdir -p <output-dir>
```

Verify the directory was created successfully.

---

## Step 6: Analyze Codebase and Generate Stories

Use the Task tool to invoke the **story-decomposer** agent:

Provide to the agent:
- Task name and description
- Prior work context
- Constraints
- Scope checklist
- Key paths to analyze
- Gotchas to consider

The agent will:
1. Analyze the codebase structure
2. Find existing patterns
3. Decompose the task into atomic stories
4. Return a complete prd.json

---

## Step 7: Generate Artifacts

Read templates from `${CLAUDE_PLUGIN_ROOT}/templates/` and substitute variables.

### Variables to substitute:

| Variable | Source |
|----------|--------|
| `${TASK_NAME}` | User input |
| `${TASK_DESCRIPTION}` | User input |
| `${BRANCH_NAME}` | User input or `ralph/<task-name>` |
| `${PRIOR_WORK}` | User input, formatted as bullet list |
| `${CONSTRAINTS}` | User input, formatted as bullet list |
| `${KEY_PATHS}` | User input, formatted as list |
| `${GOTCHAS}` | User input, formatted as bullet list |
| `${SCOPE}` | User input, formatted as checklist |
| `${STORY_COUNT}` | From prd.json userStories length |
| `${STORY_LIST}` | Summary of stories from prd.json |
| `${CODEBASE_PATTERNS}` | From agent analysis |
| `${BUILD_COMMAND}` | From agent analysis |
| `${DATE}` | Current date |
| `${MAX_ITERATIONS}` | Story count + 10 buffer |
| `${STORY_ID}` | Placeholder for prompt.md |
| `${STORY_TITLE}` | Placeholder for prompt.md |

### Files to generate:

1. **ralph.sh** - from `ralph.sh.template`
2. **prompt.md** - from `prompt.md.template`
3. **prd.json** - from agent output (already JSON)
4. **progress.txt** - from `progress.txt.template`
5. **AGENTS.md** - from `AGENTS.md.template`
6. **README.md** - from `README.md.template`

For each template:
1. Read the template file
2. Replace all `${VARIABLE}` patterns with collected values
3. Write to output directory

---

## Step 8: Summary

After generating all files, display:

```
Ralph artifacts generated in: <output-dir>/

Files created:
  - ralph.sh      (loop runner script)
  - prompt.md     (AI prompt context)
  - prd.json      (N user stories)
  - progress.txt  (progress tracking)
  - AGENTS.md     (agent instructions)
  - README.md     (usage guide)

First story to implement:
  US-001: <title>
  <description>

To start the autonomous loop:
  cd <output-dir>
  chmod +x ralph.sh
  ./ralph.sh
```

---

## Error Handling

- If output directory already exists and is not empty, warn the user and ask to confirm
- If template file (--from-file) doesn't exist, show error
- If codebase analysis fails, fall back to generic story templates
- If any required field is missing, prompt for it

---

## Example Usage

### Interactive
```
/ralph:generate ./my-feature

# Claude asks questions step by step
# Generates all artifacts in ./my-feature/
```

### From Template
```
/ralph:generate --from-file task.yaml ./my-feature

# Reads task.yaml for all details
# Generates all artifacts in ./my-feature/
```
