---
allowed-tools: Bash(find:*), Bash(tree:*), Bash(cat:*), Bash(head:*), Bash(wc:*), Bash(git rev-parse:*), Bash(pwd:*), Read, Write, Edit
description: Generate or update CLAUDE.md following best practices (concise, progressive disclosure, no linter duties)
---

# Task: Create/Update CLAUDE.md

You are helping me create or update a CLAUDE.md file. This file onboards AI coding agents into the codebase.

## Step 0: Detect Context

First, determine where you are:

```bash
# Get repo root
git rev-parse --show-toplevel

# Get current directory
pwd
```

**If current directory IS the repo root:**
→ You are creating a ROOT CLAUDE.md (comprehensive, sets up progressive disclosure)

**If current directory is BELOW the repo root:**
→ You are creating a SUBDIRECTORY CLAUDE.md (package/app-specific, additive to root)
→ Read the root CLAUDE.md first to avoid duplication

## Before Writing Anything

1. Explore the current directory structure using `find` or `tree`
2. Read existing documentation (README, CONTRIBUTING, existing CLAUDE.md)
3. If in subdirectory: read the root CLAUDE.md to understand what's already covered
4. Identify the tech stack from package.json, Cargo.toml, go.mod, or similar
5. Look for existing patterns in the code
6. Check for existing `agent_docs/` or similar directories

## Critical Constraints

### Instruction Budget
- Claude Code's system prompt uses ~50 instructions already
- LLMs reliably follow only ~150-200 instructions total
- Target: 60-100 lines ideal, never exceed 300 lines

### The Ignore Problem
Claude Code tells the model to ignore CLAUDE.md content that isn't relevant to the current task. Every line must be universally applicable or it gets skipped.

## What to Include

### 1. Project Identity (2-3 lines max)
- Project name and one-sentence purpose
- No marketing copy

### 2. Tech Stack (with versions)
- Framework, language, database, ORM, key libraries
- Include version numbers to prevent outdated suggestions

### 3. Essential Commands Only
- Only commands used in >50% of sessions
- Dev, test, build, database commands

### 4. Project Structure (non-obvious parts only)
- Skip standard framework conventions
- Focus on what would confuse a new developer

### 5. Hard Rules (universally applicable)
- Things that apply to EVERY task
- Critical "never do X" warnings
- Architectural boundaries

### 6. Domain Context
- Business logic that can't be inferred from code
- Key domain terms — this is what auto-generators miss

### 7. Progressive Disclosure Pointers
Reference external docs instead of embedding:
```
## Docs
- `docs/database.md` — before DB changes
- `docs/auth.md` — before touching auth
```

## What to EXCLUDE

- Code style guidelines → use linters (Biome, ESLint)
- Long code examples → they become outdated
- Framework basics → Claude already knows these
- Conditional instructions → put in separate docs
- Historical context → "we used to..." is noise
- TODO lists → not universally applicable

## Output Structure

### For ROOT CLAUDE.md (repo root)

```markdown
# Project: [Name]

[One sentence purpose]

## Stack
- [Framework + version]
- [Language + config]
- [Database + ORM]

## Commands
- `cmd` — description
- `cmd` — description

## Structure
[High-level: apps, packages, key directories]

## Rules
[Universal hard rules across entire repo]

## Domain
[Business logic, key concepts]

## Docs
- `agent_docs/topic.md` — when to read
```

Target: 60-100 lines, comprehensive but lean.

### For SUBDIRECTORY CLAUDE.md (apps/*, packages/*, etc.)

```markdown
# [Package/App Name]

[One sentence: what this specific package does]

## Commands
- `cmd` — description (only if different from root)

## Structure
[Package-specific directories only]

## Rules
[Package-specific rules NOT covered by root]

## Domain
[Package-specific business logic if any]
```

Target: 30-50 lines maximum. This is ADDITIVE to root — don't repeat anything already in root CLAUDE.md.

## Your Task

1. Detect context (root vs subdirectory)
2. If subdirectory: read root CLAUDE.md first
3. Explore the relevant directory thoroughly
4. Generate the CLAUDE.md content (or show diff if updating existing)
5. For root: list any `agent_docs/` files that should be created with brief descriptions
6. For subdirectory: explicitly note what you're NOT including because it's in root
7. For updates: explicitly state what to remove and why

## Final Checklist

### For ROOT CLAUDE.md
- [ ] Under 100 lines (or justified if longer)
- [ ] Every instruction is universally applicable
- [ ] No style guides (use linters)
- [ ] No code snippets that will become outdated
- [ ] Uses pointers to docs, not embedded content
- [ ] Domain knowledge included
- [ ] A senior dev could start contributing immediately

### For SUBDIRECTORY CLAUDE.md
- [ ] Under 50 lines
- [ ] Nothing duplicated from root CLAUDE.md
- [ ] Only package-specific commands, rules, structure
- [ ] Stack info only if different from root (e.g., different framework)
- [ ] No references to agent_docs (that's root's job)

After confirmation, write the CLAUDE.md file to the current directory.
