---
type: task
schema_version: '9'
id: T-6445
state: open/ready
title: Fix a typo in the README
created: 2026-10-18
related:
- '[[M-Y8EQ]]'
tags: []
need_human_review: false
impact: medium
complexity: medium
autonomy: autonomous/pr
kind: implementation
readiness_verified_at: 2026-10-18T00:00:00Z
---
# Fix a typo in the README

## Goal

Find one typo or unclear sentence in the README and fix it. A small, safe first task. Ready for an agent to pick up.

## Approach

1. Read the README and pick one typo or unclear sentence.
2. Fix it, changing nothing else.
3. Open a pull request.

## Acceptance criteria

- [ ] AC-1: The README no longer contains the typo or unclear sentence.
- [ ] AC-2: No other file changes.

## Out of scope

- Rewriting or restructuring the README.

## Post-mortem

_Captured by /sdlc:task-work on 2026-10-18. PR: pending._

### Acceptance criteria coverage

- AC-1: agent-manual — README sentence "do not edit by hand" reworded to "do not edit it by hand"; confirmed in diff
- AC-2: auto — `git diff --stat origin/main` shows only README.md and this task file's post-mortem

### What worked

- nothing notable

### Friction and automation gaps

- none observed
