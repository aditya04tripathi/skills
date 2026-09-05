---
name: dependency-upgrader
description: >-
  Upgrades dependencies in a controlled way (patch, minor, major, or
  security-only): inspects changelogs, identifies breaking changes, updates
  code, tests, and commits atomically via atomic-commits. Use when upgrading
  packages, applying security updates, or bumping dependencies without
  Dependabot-style chaos.
---

# Dependency Upgrader

More controlled than generic Dependabot chaos.

## Modes

- `patch`
- `minor`
- `major`
- `security-only`

If unspecified, prefer `security-only` when CVEs/advisories are the prompt; otherwise `patch`. Do not take `major` without an explicit ask.

## Behaviour

- inspect changelogs
- identify breaking changes
- update code
- test
- atomically commit

Do not automatically update unrelated dependencies. Do not invent changelog entries. If a changelog cannot be fetched, say so and inspect release notes / diff.

## Workflow

1. Inventory current deps in scope (named packages or the chosen mode). Record before versions.
2. Resolve target versions within the mode. For `security-only`, only advisory-related bumps.
3. Inspect changelog/breaking changes for each bump.
4. Apply updates with the repo's package manager (do not mix npm/pnpm/yarn/bun).
5. Fix compile/type/test breakages required by the bump. Stay in scope.
6. Run relevant validation (`test-impact` first, then repo-required checks).
7. Invoke `atomic-commits` for grouping and messages (e.g. chore per package family, not one `fix review issues` dump). In this skill, when the user asked to upgrade, the parent executes that script. Push only if asked. Never force push.
8. Do not modify lockfiles except as a result of the intended upgrade.

Protected: a major bump that is a public API or security-architecture change still follows `codebase-intelligence` confirmation rules when those apply.

## Output

```markdown
# Dependency Upgrade

## Mode
## Changes
package: from → to
## Breaking changes
## Code edits
## Tests run
## Commits
## Blocked
What was skipped and why.
```
