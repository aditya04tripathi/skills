---
name: task-bootstrap
description: >-
  Bootstraps a ticket, issue, or feature request into an implementation plan
  before coding. Reads MEMORY.md and AGENTS.md, maps affected areas and reusable
  abstractions, lists files likely to change, identifies risks and tests, and
  produces a task-specific checklist. Optionally scaffolds files. Use when
  starting a task, implementing a ticket, beginning a feature, or before
  substantial coding.
---

# Task Bootstrap

Turn a ticket, issue, or feature request into a plan the agent can execute without wandering.

Default: plan only. Do not start coding or scaffold unless the user asks, or they explicitly want bootstrap-then-implement.

## Flow

```text
Task
 ↓
Read MEMORY.md
 ↓
Read AGENTS.md
 ↓
Understand requirement
 ↓
Map impact
 ↓
Identify reusable abstractions
 ↓
Determine tests
 ↓
Create implementation plan
 ↓
Start coding
```

If `.agents/MEMORY.md` or `AGENTS.md` is missing or stale, invoke `codebase-intelligence` repository intelligence first. Do not rediscover the whole repo if memory is current.

Use `implementation-pathfinder` for where the work belongs and what to reuse. Use `change-impact` for blast radius. Do not invent parallel versions of those skills.

## Workflow

1. Capture the requirement (ticket/issue/request, acceptance criteria, constraints).
2. Read `.agents/MEMORY.md` and `AGENTS.md`.
3. Understand the requirement in repository terms. Ask only if blocking ambiguity remains.
4. Map impact: affected apps/packages/services, APIs, data, UI, jobs, config.
5. Identify reusable abstractions and files to inspect first (`implementation-pathfinder`).
6. Determine tests: existing coverage, gaps, how this repo tests this kind of change (`test-impact` when the change set is known).
7. List files likely to create/modify/delete. Mark confidence.
8. Identify risks and what not to duplicate.
9. Emit the checklist below.
10. Optionally scaffold repository-native files only if asked. Infer patterns from the codebase; no generic template sludge.
11. Start coding only when the user wants implementation.

## Output

```markdown
# Task Bootstrap

## Requirement
## Affected areas
## Reuse
Abstractions, patterns, and files to copy — not reinvent.
## Do not
## Files likely to change
## Risks
## Tests
## Implementation plan
Ordered steps, smallest coherent slices.
## Checklist
- [ ] inspect …
- [ ] implement …
- [ ] tests …
- [ ] validate …
```

Validation commands must come from AGENTS.md / MEMORY.md / repo config. Never fabricate commands.

Do not commit. If later implementation needs commits, invoke `atomic-commits`. Never force push, discard user work, or store secrets.
