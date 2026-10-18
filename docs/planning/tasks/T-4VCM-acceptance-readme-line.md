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
