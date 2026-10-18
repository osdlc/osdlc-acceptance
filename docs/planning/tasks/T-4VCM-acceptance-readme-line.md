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
