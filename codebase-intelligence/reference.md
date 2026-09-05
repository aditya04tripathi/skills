# Templates and output contracts

Read this file when writing MEMORY.md / AGENTS.md, collecting specialist results, producing the long-form review, or emitting merge-readiness status.

## Specialist output contract

Every specialist subagent must return:

### Findings

For each finding:

- severity
- confidence
- affected file(s)
- affected lines when possible
- explanation
- impact
- recommended fix
- whether automatic remediation is safe

### Architecture Context

Explain relevant architecture or dependency relationships.

### Tests

Identify:

- existing tests covering the change
- missing tests
- regressions that should be tested

### Risk

Describe any risks introduced by the change.

### Recommendation

One of: `approve` | `approve-with-notes` | `changes-required` | `blocking`

Subagents must NOT independently push changes. All modifications are orchestrated by the parent agent.

---

## Calling-agent briefing

After repository intelligence analysis, provide:

### Repository

What it does.

### Architecture

Major systems and relationships.

### Changed Area

How the current PR fits into the architecture. Omit if there is no PR.

### Relevant Rules

Coding and architecture rules relevant to the task.

### Important Paths

Paths likely needed during remediation.

### Validation

Commands relevant to the affected system.

### Risk Areas

Anything requiring special care.

This summary should allow the calling agent to act without rereading the entire repository.

Confirm `.agents/MEMORY.md` and `AGENTS.md` were created or refreshed.

---

## `.agents/MEMORY.md`

Machine-oriented repository intelligence. Concise. Headings and bullets. Concrete paths. High information density. No source dumps. No enormous file listings. No secrets.

```markdown
# Repository Memory

## Repository Overview
## Repository Type
## Technology Stack
## Workspace Structure
## Architecture
## Important Entry Points
## Important Paths
## Domain Model
## Data Flow
## API and Integrations
## Authentication and Authorization
## Persistence
## Coding Conventions
## Architectural Rules
## Testing Strategy
## Common Commands
## Environment and Configuration
## CI/CD and Deployment
## Known Risks and Technical Debt
## Agent Guidance
## Last Analysis

- date/time
- commit SHA
- branch
- full or incremental
```

Include: applications/services/packages; dependency relationships; dangerous areas; last analyzed commit/branch; analysis timestamp.

Environment: describe structure without exposing credentials, API keys, passwords, private certificates, access tokens, or `.env` values.

Known Risks: evidence-backed only. Not a generic code review.

Agent Guidance: files to inspect before changes; abstractions to reuse; patterns not to bypass; dangerous areas; required validation commands.

If AGENTS.md exists: read first; preserve useful repository-specific guidance; remove stale, duplicated, generic, or contradictory content; rewrite into a concise current version. Do NOT endlessly append.

---

## `AGENTS.md`

Human-readable operational guidance. Concise. Repository-specific.

```markdown
# AGENTS.md

## Repository Overview
## Repository Structure
## Architecture Rules
## Development Workflow
## Coding Standards
## Implementation Guidance
## Testing Requirements
## Validation Commands
## Known Forbidden Patterns
## Completion Checklist
```

Implementation Guidance: only categories that apply (new features, bug fixes, components, APIs, database changes, shared libraries, tests).

---

## Long-form PR review

Every review mode produces this. Adaptive detail based on PR size and risk.

```markdown
# PR Review

## Verdict

One of: APPROVED | APPROVED WITH NOTES | CHANGES REQUIRED | BLOCKED | MERGE READY

## Merge Readiness

When applicable: `merge-ready: true/false`

## Executive Summary

What the PR changes and overall quality.

## Scope Reviewed

Changed areas; affected architecture; relevant dependencies.

## Findings

### P0
Findings or "None."

### P1
Findings or "None."

### P2
Findings or "None."

### P3
Findings or "None."

## Fixes Applied

Modes B/C/D: what changed; why; relevant commits.

## Architecture
## Correctness
## Security
## Performance
## Accessibility
## Database / Migrations
## API Compatibility

Include the relevant sections only.

## Testing

Existing coverage; tests added; gaps; test results.

## Validation

- [ ] formatter
- [ ] lint
- [ ] typecheck
- [ ] unit tests
- [ ] integration tests
- [ ] build

Only check items actually executed successfully. Adapt to the repository.

## Risk Assessment
## Final Recommendation
```

---

## Mode D merge-readiness YAML

Always include machine-readable status in Mode D.

```yaml
merge-ready: true
risk: low
target: production
blocking-findings: []
```

or:

```yaml
merge-ready: false
risk: high
target: production
blocking-findings:
  - severity: P1
    title: Authorization bypass in resource mutation
    file: ...
```

`risk` may be: `low` | `medium` | `high` | `critical`.
