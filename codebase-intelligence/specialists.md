# Specialist checklists

Use only the specialists justified by the diff. Do not invent visual, performance, or security conclusions without evidence.

## Test gap analysis

For each behavioural change identify: happy path; failure path; boundary cases; authorization path; null/empty state; concurrency risks where applicable; backwards compatibility where applicable.

Inspect existing tests. Determine whether the changed behaviour is covered.

Missing regression coverage: generally P2. Missing tests for highly critical business/security behaviour may be P1.

Modes B/C/D may add tests automatically.

## Security review

When security-sensitive code is affected, inspect: authentication; authorization; input validation; output encoding; injection; secret handling; SSRF; path traversal; file upload; cryptography; token handling; session handling; CORS; CSRF; privilege escalation; insecure direct object references; data leakage; logging of sensitive information; dependency trust boundaries.

Severity must depend on exploitability and impact. Do not label theoretical style preferences as security vulnerabilities.

## Performance review

When relevant, inspect: algorithmic complexity; repeated expensive work; N+1 queries; indexes; excessive network requests; excessive frontend rendering; serialization cost; memory use; blocking I/O; cache misuse; large payloads; expensive loops; concurrency bottlenecks; frontend bundle changes.

For production-targeting merge-ready PRs, performance validation is required when affected code has meaningful performance implications.

## Accessibility review

For relevant frontend changes inspect: semantic HTML; keyboard navigation; focus management; labels; ARIA correctness; contrast where determinable; screen reader behaviour; forms; interactive controls; modal/dialog behaviour.

Production-targeting merge-ready PRs must have no unresolved significant accessibility regressions.

## Bundle size review

For frontend production merges, when practical: compare bundle output; newly introduced heavy dependencies; accidental server/client boundary changes; duplicated libraries; unnecessarily client-side modules; major chunk growth.

Do not block merge based on tiny meaningless fluctuations.

## Database migration validation

For production-targeting merge-ready PRs with database changes inspect: forwards/backwards compatibility; migration ordering; lock risk; table rewrite risk; index creation behaviour; nullable transitions; default values; data backfills; destructive operations; rollback strategy; mixed-version deployment compatibility.

Migration risk must be explicitly stated in the final review.

## Frontend visual regression

For production-targeting merge-ready frontend changes, use the repository's available visual validation tooling when present: screenshot tests; Storybook; Playwright; Chromatic; visual snapshots; browser-based comparison.

Do not invent visual validation results if tooling is unavailable. When unavailable, inspect visual-risk areas and clearly state the limitation.

## Dependency health

When dependencies change inspect: necessity; version compatibility; duplicated functionality; bundle impact; transitive risk; maintenance status where information is available; license restrictions where repository policy requires; security implications.

Do not automatically update unrelated dependencies.

## Dead code analysis

When refactoring or reviewing affected modules inspect for: unused exports; unreachable code; obsolete feature flags; abandoned abstractions; duplicate utilities; stale configuration; unnecessary dependencies.

Do not perform repository-wide dead-code removal during an unrelated small PR.

## API contract review

When API contracts change inspect: request/response compatibility; validation; versioning; nullability; error contracts; client compatibility; schema generation; documentation; serialization.

Public breaking API changes require user confirmation unless explicitly intended by the PR.
