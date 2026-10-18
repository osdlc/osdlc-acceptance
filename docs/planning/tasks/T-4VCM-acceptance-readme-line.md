---
type: task
schema_version: '9'
id: T-4VCM
state: closed/done
created: 2026-10-18
related: []
tags: []
need_human_review: false
impact: low
complexity: small
autonomy: autonomous/pr
prs:
  - https://github.com/osdlc/osdlc-acceptance/pull/5
  - https://github.com/osdlc/osdlc-acceptance/pull/8
  - https://github.com/osdlc/osdlc-acceptance/pull/13
  - https://github.com/osdlc/osdlc-acceptance/pull/16
last_reviewed: 2026-10-18
completion_note: "Shipped via #16."
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

- [x] AC-1: README.md contains the line `Acceptance run: ok`.

## Out of scope

Any other change to README.md or to the rest of the repository.

## Post-mortem

_Captured by /sdlc:task-work on 2026-10-18. PR: pending._

### Acceptance criteria coverage

- AC-1: agent-manual — inspected the README.md diff; the last line is `Acceptance run: ok` after a blank line

### What worked

- nothing notable

### Friction and automation gaps

- none observed
