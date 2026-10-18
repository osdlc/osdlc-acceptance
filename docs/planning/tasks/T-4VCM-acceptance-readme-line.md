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

Add one line to README.md that says "Acceptance run: ok".

## Today

README.md holds a title and a short paragraph describing the repository as an sdlc test project. It has no acceptance-run status line.

## Proposed

README.md contains the line `Acceptance run: ok` on its own line.

## Approach

1. Open README.md.
2. Append a blank line followed by the line `Acceptance run: ok` at the end of the file.
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

- AC-1: agent-manual — confirmed `Acceptance run: ok` is the last line of README.md; `npm test` check gate passed (OK 1/1)

### What worked

- nothing notable

### Friction and automation gaps

- `task begin` did not plant the Post-mortem stub the skill says it does — begin should add the stub, or the skill should say to append it
- `ensure-ready-mutate` rejects `--cleanup-on-fail` with `--mode pass`, contradicting the skill's instruction to always pass it — skill should pass the flag only on the fail path
- Untrusted repo blocked baseline capture and setup — acceptance harness should run `sdlc repo trust` up front
- Setup `npm install` leaves an untracked package-lock.json in the worktree — add it to .gitignore in the starter repo
