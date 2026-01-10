---
name: ralph-method
description: Apply Ralph Wiggum Method patterns for decomposing tasks into atomic user stories
user-invocable: false
when_to_use: |
  Use this skill when:
  - Breaking down a feature or task into implementable user stories
  - Planning work for autonomous AI-assisted coding loops
  - Creating prd.json or story breakdown artifacts
  - Evaluating if a story is atomic enough for single-iteration completion
  - Generating Ralph Wiggum Method artifacts
---

# Ralph Wiggum Method

The Ralph Wiggum Method is an autonomous AI coding loop that ships features while you sleep. It breaks complex tasks into atomic user stories that can each be completed in a single AI iteration.

## Core Principles

1. **One iteration = One story = One commit**
2. Each story must be completable in a single AI iteration (5-30 minutes of work)
3. Stories are ordered by dependency (infrastructure first, consumers last)
4. Every story ends with build/test verification
5. The loop runs autonomously until all stories pass

## Story Atomicity Rules

A story is atomic when:

- It can be committed independently without breaking the build
- It leaves the codebase in a working state
- It has clear, verifiable "done" criteria
- It doesn't require human intervention mid-way
- It can be code-reviewed as a single logical change

## Story Decomposition Patterns

### API Endpoint
| Priority | Story | Purpose |
|----------|-------|---------|
| 1 | Create schema/types | Define data structures |
| 2 | Create service layer | Business logic |
| 3 | Create route handler | HTTP interface |
| 4 | Add integration test | Verification |

### Database Change
| Priority | Story | Purpose |
|----------|-------|---------|
| 1 | Create migration file | Schema change |
| 2 | Update model/entity | Code representation |
| 3 | Update seed data | Test data |
| 4 | Update consuming code | Integration |

### React Component
| Priority | Story | Purpose |
|----------|-------|---------|
| 1 | Create component skeleton | Structure |
| 2 | Add state/logic | Behavior |
| 3 | Add styling | Appearance |
| 4 | Add tests | Quality |

### Refactoring
| Priority | Story | Purpose |
|----------|-------|---------|
| 1 | Add new implementation alongside old | Parallel path |
| 2 | Migrate consumers one by one | Gradual switch |
| 3 | Remove old implementation | Cleanup |

### Configuration Change
| Priority | Story | Purpose |
|----------|-------|---------|
| 1 | Add environment variables | Define config |
| 2 | Create/update config file | Structure config |
| 3 | Update consuming code | Use config |

## prd.json Schema

```json
{
  "branchName": "ralph/feature-name",
  "taskDescription": "One-line summary of the task",
  "projectContext": {
    "framework": "e.g., Next.js, .NET",
    "testCommand": "e.g., npm test, dotnet test",
    "buildCommand": "e.g., npm run build, dotnet build"
  },
  "userStories": [
    {
      "id": "US-001",
      "title": "Short descriptive title",
      "description": "What to implement in detail",
      "acceptanceCriteria": [
        "Specific, verifiable criterion 1",
        "Specific, verifiable criterion 2",
        "Build/test passes"
      ],
      "priority": 1,
      "passes": false,
      "notes": "Hints, gotchas, references to existing code"
    }
  ]
}
```

### Field Descriptions

- **branchName**: Git branch for all work (format: `ralph/<feature-name>`)
- **taskDescription**: One-line summary for quick context
- **projectContext**: Build/test commands and framework info
- **userStories**: Array of atomic stories in dependency order
  - **id**: Unique identifier (US-001, US-002, etc.)
  - **title**: Short title for commit messages
  - **description**: Full implementation details
  - **acceptanceCriteria**: Verifiable conditions for "done"
  - **priority**: Execution order (1 = first)
  - **passes**: Set to true when story is complete
  - **notes**: Additional context, gotchas, file references

## Anti-Patterns to Avoid

### Vague Stories
- BAD: "Update all files"
- GOOD: "Update UserController to use new AuthService"

### Unbounded Scope
- BAD: "Fix bugs as found"
- GOOD: "Fix null reference in UserService.GetById"

### Circular Dependencies
- BAD: Story 3 depends on Story 5 which depends on Story 3
- GOOD: Linear dependency chain

### Too Large
- BAD: "Implement entire authentication system"
- GOOD: Split into: schema, service, middleware, routes, tests

### Missing Verification
- BAD: Story with no acceptance criteria
- GOOD: Every story ends with "Build succeeds" or "Tests pass"

## Story Sizing Guidelines

| Size | Time | Examples |
|------|------|----------|
| XS | 5-10 min | Add config value, create empty file, update import |
| S | 10-20 min | Create simple function, add basic test, update route |
| M | 20-30 min | Create service class, add migration, implement handler |
| L | 30+ min | TOO BIG - split into smaller stories |

## Verification Commands

Every story should end with verification. Common patterns:

```bash
# JavaScript/TypeScript
npm run build && npm test

# .NET
dotnet build && dotnet test

# Python
pytest && python -m py_compile src/*.py

# Go
go build ./... && go test ./...
```

## Progress Tracking

The `progress.txt` file tracks completed stories:

```
# Progress: feature-name
# Total stories: 5

Current story: 3
Completed: 2

---
# Story log
[2025-01-10 10:00] US-001: Create user entity - PASSED
[2025-01-10 10:15] US-002: Add user repository - PASSED
```

## Commit Message Format

```
story US-XXX: <title>

<description of what was done>
```

Example:
```
story US-001: Create user entity

Added UserEntity with id, email, and created_at fields.
Configured EF Core mapping in ApplicationDbContext.
```
