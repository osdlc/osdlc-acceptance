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

## Goal

Mark this repository as exercised by the sdlc acceptance run, so a reader of
README.md can see that the last run got a task merged.

## Today

README.md has a title and a short description. It has no line about the
acceptance run.

## Proposed

README.md ends with the line `Acceptance run: ok`, after a blank line. The
existing text is unchanged.

## Approach

1. Append a blank line and `Acceptance run: ok` to the end of README.md.

## Areas

| Area | Note |
|---|---|
| `README.md` | the one line |

## Acceptance criteria

- [ ] AC-1: README.md contains the line `Acceptance run: ok`.

## Out of scope

Any other change to README.md or to the rest of the repository.

## Post-mortem

_Captured by /sdlc:task-work on 2026-10-18. PR: pending._

### Acceptance criteria coverage

- AC-1: agent-manual — inspected `git diff`; README.md now ends with a blank line and `Acceptance run: ok`

### What worked

- nothing notable

### Friction and automation gaps

- `--cleanup-on-fail` was rejected with `--mode pass`, though task-work's Step 5a passes both flags — task-work should pass `--cleanup-on-fail` only on the fail path
- `sdlc task begin` no longer plants the Post-mortem stub the Step 8 prose says it does — Step 8 wording should say to create the section
