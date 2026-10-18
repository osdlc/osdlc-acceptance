---
type: task
schema_version: '9'
id: T-4VCM
state: open/ready
created: 2026-10-18
related: []
tags: []
need_human_review: false
impact: low
complexity: small
autonomy: autonomous/pr
---
# Add the acceptance line to the README

> AUTO-DEFINED: this spec was best-effort machine-authored by
> /sdlc:task-auto-define on 2026-10-18 because the task is
> autonomy: autonomous/pr. Review the Goal, Approach, Today,
> Areas, and Acceptance-criteria carefully before trusting it.

## Goal

Add one line to README.md that says "Acceptance run: ok". It gives the acceptance run a visible, checkable artifact in the repository.

## Today

README.md holds a title and a short paragraph describing the repository as an sdlc test project. It has no acceptance-run line.

## Proposed

README.md contains the line `Acceptance run: ok` as its own line, with the existing content unchanged.

## Approach

1. Open README.md.
2. Append a blank line, then the line `Acceptance run: ok`, at the end of the file.
3. Commit the change.

## Areas

| Area | Note |
|---|---|
| `README.md` | the one line |

## Acceptance criteria

- [ ] AC-1: README.md contains the line `Acceptance run: ok`.

## Out of scope

- none

## Dependencies

- none

## Post-mortem

_Captured by /sdlc:task-work on 2026-10-18. PR: pending._

### Acceptance criteria coverage

- AC-1: agent-manual — appended `Acceptance run: ok` to README.md and confirmed with `tail`

### What worked

- nothing notable

### Friction and automation gaps

- `sdlc quality run --diff-against-baseline` looks for the baseline under the worktree's `.sdlc/`, not the main checkout's — the skill should pass `--baseline-dir <project-root>/.sdlc/quality-baselines` in Step 7/9.
