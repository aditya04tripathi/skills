---
name: codebase-intelligence
description: >-
  Umbrella orchestration for repository intelligence, PR review, specialist
  subagents, optional remediation, and merge-readiness. Default mode is
  review-only. Use when reviewing a PR, making a PR merge-ready, fixing review
  findings, analyzing the codebase, refreshing MEMORY.md or AGENTS.md, or before
  substantial coding, refactoring, or architecture work. Invokes the
  atomic-commits skill for all commits; does not invent commit logic.
---

# Codebase Intelligence

Repository intelligence, code review, refactoring, PR remediation, and merge-readiness orchestration for coding agents.

Understand the repository deeply, persist knowledge, dynamically spawn specialist subagents, review pull requests, optionally fix findings, refactor, validate, push to the existing PR branch, and determine merge-readiness.

The parent agent decides which leaves to activate. Do not create independent competing skills for each specialist concern. Do not spawn every specialist for every PR.

```text
codebase-intelligence
├── understand (internal mappers + sibling skills)
│   ├── summarize / architecture-map / coding-style / memory
│   ├── implementation-pathfinder
│   └── change-impact
├── build
│   └── task-bootstrap
├── debug
│   ├── bug-triage
│   ├── root-cause-analysis
│   ├── flaky-test-hunter
│   └── test-impact
├── review (internal specialists; do not spawn all)
│   ├── correctness, architecture, security, testing, performance, …
│   └── merge-ready
├── maintain
│   ├── monorepo-health
│   └── dependency-upgrader
├── environment
│   └── dev-environment-doctor
└── delivery
    ├── atomic-commits (external skill)
    ├── push-to-pr, self-review, merge-ready-gate
```

Invoke sibling skills instead of reimplementing them. Review specialists stay internal to this skill.

Read [reference.md](reference.md), [specialists.md](specialists.md), and [thresholds.md](thresholds.md) when those sections apply.

## Operating modes

Support exactly four modes. If invoked without a mode, use `review-only`. Do not modify the repository in that mode.

| Mode | Trigger examples | Extra behaviour |
|------|------------------|-----------------|
| `review-only` (DEFAULT) | `Use codebase-intelligence on PR #42` | Inspect intelligence + PR diff + surrounding code; spawn relevant specialists; classify findings; inline comments for P0/P1; long-form review. Do **not** modify code, commit, push, or auto-resolve findings. |
| `fix-critical` | `...and fix critical findings` | Everything in `review-only`, plus auto-fix P0 and P1, add/update tests where appropriate, validate, invoke `atomic-commits`, push to the same PR branch, re-review the resulting diff. Leave P2/P3 unchanged unless required to safely fix a P0/P1. |
| `fix-all` | `Review PR #42 and fix everything you reasonably can` | Everything in `fix-critical`, plus P2, worthwhile P3, safe architectural refactoring, duplication/structural fixes, tests, maintainability, non-critical performance, accessibility, docs. Stay within PR intent. Do not silently rewrite the repository. |
| `merge-ready` | `Make PR #42 merge-ready` | Strongest mode. Full review; fix P0–P3 as appropriate; refactor; tests; validate architecture, security, API compatibility, migrations, performance, accessibility, frontend visual behaviour, bundle size where relevant; complete repo validation; push; **fresh** re-review from scratch; iterate; decide merge-readiness. Never claim merge-ready only because you fixed your own findings. |

## Workflow

1. Resolve mode from the user request. Default: `review-only`.
2. Git safety: never force push, reset, discard, stash user work, rewrite unrelated history, rebase shared branches, delete branches, or merge the PR unless explicitly instructed. The skill may make a PR merge-ready; it does **not** merge unless separately authorized.
3. Read `AGENTS.md` and `.agents/MEMORY.md`. If missing or stale, run the repository intelligence workflow first (parallel subagents; write/update those files). Then give the calling agent the compact briefing in [reference.md](reference.md).
4. Load PR metadata (`gh pr view`, diff, files, target branch). Determine intended scope from title, description, linked issue, changed files, tests, docs, architecture.
5. Before classifying a defect, ask: is this behaviour intentional according to the PR? Do not "fix" intentional behaviour.
6. Select specialists dynamically from the diff and architecture. Spawn in parallel. Subagents must not modify files or push. Parent consolidates.
7. Never review only the visible diff when surrounding code is required: callers, callees, interfaces, implementations, tests, schemas, related config, dependency boundaries.
8. Dedupe findings. When specialists disagree, inspect code, tests, and architecture yourself. Never blindly average opinions. Report uncertainty when evidence is inconclusive.
9. Post inline PR comments **only** for P0 and P1 with sufficient evidence. P2/P3/Nit go in the long-form review.
10. Produce the long-form PR review ([reference.md](reference.md)).
11. Modes B/C/D only: request confirmation once for protected high-risk categories if the PR does not already intend them; apply in-scope fixes; validate; invoke `atomic-commits`; push to the **same** PR branch (no force); run the self-review loop.
12. Mode D: iterate Review → Fix → Atomic commits → Push → Fresh review → Validate until merge-ready or human judgment is required. Do not loop indefinitely. Output machine-readable merge status.

## Production merge awareness

Determine the PR target branch. Identify whether it is a production/release branch. Examples may include: `main`, `master`, `production`, `prod`, `release/*`, `stable`, and repository-specific production branches.

Do not rely exclusively on branch naming. Use repository CI/CD configuration and AGENTS.md/MEMORY.md when possible.

When the PR targets production, `merge-ready` requires additional gates when applicable: zero unresolved P2; performance; accessibility; bundle-size; database migration validation; frontend visual regression validation.

For non-production PRs, those extra gates are advisory unless the changed code makes them necessary.

P0 and P1 always block merge readiness regardless of target branch.

## Severity

**P0 Critical — must not merge:** remote code execution; authentication bypass; severe authorization failure; credential exposure; irreversible data corruption; destructive migration without safeguards; catastrophic production outage; severe privacy breach; exploitable injection; critical financial/data integrity failure.

**P1 High — must be fixed before merge:** functional correctness bug; major regression; broken authorization logic; serious race condition; common-path crash; broken API behaviour; missing critical validation; transaction/data consistency bug; important test failure; production-impacting performance regression.

**P2 Medium — should normally be fixed:** architectural violation; maintainability issue likely to cause defects; important edge case; moderate performance issue; weak abstraction; duplicated business logic; inadequate test coverage; accessibility issue; potential future compatibility issue. **P2 blocks merge readiness when targeting production.**

**P3 Low — non-blocking:** readability; naming; minor cleanup; small simplification; low-risk refactoring; documentation mismatch; minor style inconsistency.

**Nit:** purely cosmetic. Avoid excessive nitpicking. Do not generate review noise for formatting already enforced automatically.

Every finding tracks confidence: `high`, `medium`, `low`. Only P0/P1 with sufficient evidence get inline comments. If confidence is low, investigate further. Do not post a dramatic P0 because an agent got nervous about a variable name.

## Inline comments

Post inline comments ONLY for P0 and P1. Each must contain: severity; concise title; explanation; concrete impact; proposed remediation when practical.

Example: `[P1] Authorization is checked after resource access`

Explain why this can cause incorrect behaviour and what needs to change. Avoid vague comments such as "This could be improved."

Do not produce multiple review comments for the same underlying issue.

## Dynamic subagent selection

Before spawning specialists:

1. read repository intelligence
2. inspect PR metadata
3. inspect changed files
4. inspect diff statistics
5. determine impacted domains
6. determine architectural dependencies
7. determine risk level
8. choose appropriate specialist reviewers

Launch independent specialists concurrently (Task tool: `explore` for search, `generalPurpose` when a scope needs deeper reading). Each subagent receives: assigned scope; relevant paths; questions; do not modify files; cite important paths; return the specialist output contract in [reference.md](reference.md).

Selection examples (do not spawn the whole list):

- Database migrations → migration-reviewer, database-reviewer, testing-reviewer, correctness-reviewer
- Authentication → security-reviewer, correctness-reviewer, architecture-reviewer, testing-reviewer
- React UI → frontend-reviewer, accessibility-reviewer, testing-reviewer
- Large frontend production PR → also performance-reviewer, bundle-size-reviewer, visual-regression-reviewer
- API contracts → api-contract-reviewer, backend-reviewer, testing-reviewer, architecture-reviewer
- Infrastructure → infrastructure-reviewer, security-reviewer, ci-cd-reviewer

Possible roles: correctness-reviewer, architecture-reviewer, security-reviewer, performance-reviewer, accessibility-reviewer, frontend-reviewer, backend-reviewer, mobile-reviewer, database-reviewer, migration-reviewer, api-contract-reviewer, testing-reviewer, test-gap-analyzer, type-safety-reviewer, dependency-reviewer, dead-code-reviewer, concurrency-reviewer, infrastructure-reviewer, ci-cd-reviewer, visual-regression-reviewer, bundle-size-reviewer, refactoring-reviewer.

Domain checklists: [specialists.md](specialists.md).

## Repository intelligence

Before substantial review work, read `AGENTS.md` and `.agents/MEMORY.md`. If they do not exist or are stale, invoke the repository intelligence workflow.

Understand: repository layout; architecture; domain boundaries; dependency direction; applications/services; build system; APIs; state management; database; authentication; deployment; tests; coding conventions; architectural constraints; common commands; known risks.

Ignore or deprioritize `node_modules`, `vendor`, `dist`, `build`, `coverage`, `.next`, `.turbo`, generated artifacts, compiled binaries, dependency caches, and `.gitignore` paths unless they contain repository-specific configuration.

Do not infer architecture solely from directory names. Prefer representative high-value files. Do not enumerate every trivial file. When information cannot be determined reliably, mark it unknown. Never fabricate commands, conventions, architecture, or dependencies.

**Incremental:** if MEMORY.md has a previous commit, inspect HEAD, branch, git status, and files changed since that commit; update only affected knowledge where practical. Full analysis when memory is missing, architecture or tooling has materially changed, the saved commit cannot be compared, or the user requests full analysis.

Never discard user changes. Never store secrets. Do not modify application source during intelligence-only analysis. Do not modify lockfiles. Do not run destructive database commands. Do not install dependencies unless necessary and explicitly permitted.

Templates and briefing: [reference.md](reference.md).

## Coding style

Infer conventions from representative files. Evaluate: naming; file/directory organization; imports/exports; component/function/object/class patterns; typing; error handling; async; state management; API patterns; validation; dependency injection; testing; documentation; configuration.

Distinguish: (1) enforced conventions (2) strongly established conventions (3) inconsistent patterns. Do not invent repository conventions from one isolated file.

## Hard refactoring thresholds

Use hard thresholds when auditing code. Unless the repository defines stricter thresholds, **a breach is a hard trigger for a finding**, not necessarily a hard mandate to split or rewrite.

The reviewer must justify whether remediation improves the architecture. Do not split a 500-line declarative route/config table merely because a line count was crossed if splitting would make the code worse.

Details: [thresholds.md](thresholds.md).

## Architectural refactoring

Refactoring mode is ARCHITECTURAL. The agent may: extract modules; split large files; split responsibilities; move domain logic; change package boundaries; introduce or remove abstractions; consolidate duplicated logic; restructure components/services; improve dependency direction, state boundaries, and API boundaries.

Refactoring must remain justified by evidence. Do not refactor unrelated code merely because it exists nearby. Do not perform repository-wide dead-code removal during an unrelated small PR.

## Protected high-risk changes

The agent may modify any code necessary to correctly fix the PR.

Request user confirmation **ONCE** before making any of the following unless the PR explicitly intends that category:

- destructive database migration (dropping tables; dropping populated columns; irreversible data conversion)
- public API breaking change (removing endpoint; incompatible public response contract; removing exported API; incompatible schema change)
- security architecture change (replacing authentication model; changing identity provider or authorization architecture; replacing token/session strategy; modifying encryption/key architecture)

If the PR already explicitly intends such a change, continue without an extra confirmation. Do not repeatedly ask for confirmation for multiple changes in the same category during the same remediation cycle.

## PR scope

Determine intended scope from PR title, description, linked issue, changed files, existing implementation, and repository conventions.

Fixes should remain consistent with that intent. Do not introduce unrelated features.

When an issue requires a larger architectural change outside reasonable PR scope: document it, classify it, leave it unresolved unless merge safety requires remediation.

## Atomic commits

DO NOT implement custom commit-generation logic.

Use the existing `atomic-commits` skill (`~/.agents/skills/atomic-commits/SKILL.md` or `~/.cursor/skills/atomic-commits/SKILL.md`).

Whenever changes need to be committed:

1. group modifications into logically independent changes
2. invoke `atomic-commits`
3. allow that skill to determine appropriate atomic commit boundaries/messages
4. preserve existing PR history
5. never rewrite unrelated commits
6. never force push unless the user explicitly instructs it

`atomic-commits` produces the grouping, conventional messages, and script. It does not invent a different format. In Modes B, C, and D the **parent** executes that script (user requested remediation), then pushes to the existing PR branch. Do not dump a script and stop. Do not skip hooks. Named paths only — never `git add .` / `git add -A`.

Expected conceptual separation: fix correctness issue; add regression test; refactor supporting abstraction; update documentation. Do not squash everything into `fix review issues` unless `atomic-commits` determines a single commit is genuinely appropriate.

Push to the existing PR branch. Never create a replacement PR unless explicitly requested.

## Same-PR remediation

In Modes B, C, and D: identify the PR source branch; ensure the working tree corresponds to it; preserve unrelated user changes; make required fixes; use atomic commits; push to the same PR branch. Never force push by default.

## Validation

Determine commands from AGENTS.md, MEMORY.md, package configuration, task runners, CI, and build scripts. Possible validations: formatting; lint; typecheck; unit/integration/E2E tests; builds; schema validation; migration validation; generated-code checks.

Never claim a command passed unless it was actually run successfully. Only check validation items that executed successfully.

## Self-review loop

Modes B, C, and D require a self-review cycle after fixes:

1. inspect the complete resulting diff
2. treat it as a fresh review
3. dynamically select reviewers again
4. run specialist reviews
5. consolidate findings
6. run relevant validation
7. detect regressions introduced by remediation

Do not rely on the original review findings.

## Merge-ready loop

Mode D may iterate: Review → Findings → Fix → Atomic commits → Push → Fresh review → Validate → Remaining findings? → Fix again if necessary → Final validation → Merge readiness decision.

Do not loop indefinitely. When unresolved ambiguity requires human judgment, mark the PR as not merge-ready and explain why.

`merge-ready: true` for ALL PRs requires: no P0; no P1; required validation passing; no known correctness regression; no unresolved security blocker; no unintended public breaking change; no unsafe migration; appropriate test coverage; working tree changes accounted for; final self-review completed.

PRODUCTION merges additionally require, when relevant to the changed code: no unresolved P2; required performance, accessibility, bundle-size, migration, and visual regression validation.

Mode D always includes machine-readable status ([reference.md](reference.md)). Risk: `low` | `medium` | `high` | `critical`.

## Review noise control

Useful review, not maximum comment count. Avoid: formatting comments handled by tooling; personal stylistic preferences; speculative concerns without evidence; repeating identical issues; excessive nits; explanations of obvious code.

Prioritize: correctness; architecture; security; regressions; test gaps; maintainability; performance; operational safety.

## Git safety and secrets

Never automatically: force push; reset; discard uncommitted work; stash user changes; rewrite unrelated history; rebase shared branches; delete branches; merge the PR — unless explicitly instructed.

Never include secret values in comments, AGENTS.md, MEMORY.md, logs, review output, or commits. Sensitive configuration may be described structurally without exposing values.

## Primary objective

Behave like a highly capable senior/staff engineer coordinating specialized reviewers.

Understand the codebase before judging changes. Focus reviewers on relevant domains. Identify real defects instead of producing review theatre. Apply fixes safely. Refactor architecture where justified. Preserve user work. Commit atomically via `atomic-commits`. Push remediation to the existing PR. Validate own changes. Review after remediation. Persist repository intelligence. Produce detailed, actionable reviews. Provide a trustworthy merge-readiness decision.

The objective is not merely to generate comments. The objective is to improve the actual quality of the codebase and, when requested, bring a PR to a defensible merge-ready state.
