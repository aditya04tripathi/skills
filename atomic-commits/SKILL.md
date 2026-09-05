---
name: atomic-commits
description: >-
  Produces a copy-pasteable bash script that commits the current git working
  tree as many small, atomic, scoped conventional commits of related files.
  Never runs git commit, git add, or the generated script. Use when the user
  asks for atomic commits, scoped commits, incremental commits, numerous
  commits, a commit script, or to commit related files in increments inside a
  directory.
---

# Atomic Commits Script

Inspect git changes and output **one copy-pasteable bash script**. The user runs it. You do not.

## Hard rules

- Do **not** run `git add`, `git commit`, `git stash`, `git push`, or the generated script.
- Do **not** use `git add .`, `git add -A`, or `git commit --no-verify`.
- Read-only git is allowed: `status`, `diff`, `log`, `rev-parse`, `ls-files`.
- Do not commit secrets (`.env`, credentials, keys, tokens).
- If there is nothing to commit in scope, say so and emit no script.

## Workflow

1. Resolve scope: the directory the user named, else the current working directory. Only include changes under that path.
2. Inspect, read-only, from the repo root:

   ```bash
   git rev-parse --show-toplevel
   git status --short -- <scope>
   git diff HEAD -- <scope>
   git diff --cached -- <scope>
   git log -8 --oneline
   ```

3. Group **related files** into **many small** commits. Prefer more commits over fewer.
4. Reply with a short plan (one line per commit: type, subject, file list), then **one** bash script in a single ` ```bash ` fence.

## Grouping

- One concern per commit. Related files stay together (implementation + its test + its types).
- Split by feature, fix, refactor, docs, or chore — not by “all the diffs”.
- Whole files only. Do not use `git add -p` or interactive staging in the script.
- If one file mixes unrelated concerns, put it with the dominant concern and note it above the script.
- Order: foundations before dependents (config/types → implementation → tests → docs).
- Include untracked, modified, deleted, and renamed files in scope. Quote paths that contain spaces.

## Commit message format

Use this format **verbatim**:

```
<feat|fix|docs|chore|refactor>: <description under 100 words>

- bigger description in points
```

Rules:

- Type must be exactly one of: `feat`, `fix`, `docs`, `chore`, `refactor`.
- Map tests, CI, style, and build into those five types (`test` files that lock in a feature → `feat` or `fix`; tooling → `chore`).
- First line is a single line. Keep it short enough for conventional commitlint (`header-max-length` is typically 100 **characters**).
- After a blank line, use `- ` bullets for the bigger description. Each bullet is one concrete change.
- Subject says **why**; bullets say **what**.

## Script shape

Emit a complete script the user can paste into a terminal with no edits required:

```bash
#!/usr/bin/env bash
set -euo pipefail
cd "$(git rev-parse --show-toplevel)"

git add -- path/to/file-a path/to/file-b
git commit -m "$(cat <<'EOF'
feat: short subject

- bullet
- bullet
EOF
)"

git add -- path/to/file-c
git commit -m "$(cat <<'EOF'
fix: short subject

- bullet
EOF
)"
```

Script requirements:

- `set -euo pipefail` so a failed commit stops the rest.
- `cd` to the repo root (hardcode the absolute toplevel from `git rev-parse` if `$(git rev-parse --show-toplevel)` would be ambiguous).
- One `git add -- <files>` then one `git commit` per group. Named paths only.
- HEREDOC with `'EOF'` so commit text is literal.
- Cover **every** in-scope change. Leave out-of-scope files untouched.
- No `push`, no hook skips, no amend, no force, no config changes.

## Response

1. Scoped path and commit count.
2. Plan: one line per commit.
3. The full script in one bash fence — nothing after the closing fence except a one-line reminder that the user must paste and run it themselves.
