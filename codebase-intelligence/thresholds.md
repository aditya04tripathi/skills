# Hard refactoring thresholds

Unless the repository defines stricter thresholds, flag the following.

A breach is a **hard trigger for a finding**. It is **not** a hard mandate to split or rewrite. Justify whether remediation improves the architecture.

Useful: `>500 source lines` → finding.

Not useful: blindly splitting a 500-line declarative route/config table because a line count was crossed.

## File size

Source file > 500 logical lines: P2 by default.

Generated files are exempt. Configuration/data files may be exempt where splitting provides no benefit.

## Function size

Function/method > 50 logical lines: P2 by default.

Allow justified orchestration functions when decomposition would reduce clarity.

## Cyclomatic complexity

Complexity > 10: P2.

Complexity > 20: P1 when the complexity materially increases correctness risk.

## Nesting

Control-flow nesting > 4 levels: P2.

## Parameter count

More than 5 parameters: P2 unless using a clearly appropriate framework API or configuration boundary.

## Duplicate logic

Substantially duplicated business logic appearing in 3 or more locations: P2.

## Component size

Frontend component > 300 logical lines: P2.

Frontend component > 500 logical lines: P1 when responsibilities are clearly mixed.

## Type safety

Unsafe broad types such as `any`, unbounded casts, ignored compiler errors: flag when avoidable and materially risky.

Repeated unsafe typing in business-critical code: P2 or P1 depending on impact.

## Circular dependencies

Any newly introduced circular dependency: P1.

Existing circular dependency touched by the PR: P2 unless it creates runtime risk.

## Dead code

New unreachable or unused implementation: P2.

Clearly obsolete touched code: P3 unless it materially increases complexity.
