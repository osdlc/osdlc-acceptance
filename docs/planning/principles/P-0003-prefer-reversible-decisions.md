---
type: principle
schema_version: '2'
id: P-0003
state: open/published
title: Prefer reversible decisions
created: 2026-10-18
related: []
tags:
  - principle/project
  - kit/base/v1
need_human_review: false
provenance: kit:base/prefer-reversible-decisions@v1
---
# Prefer reversible decisions

## Summary

- When unsure, pick the option that is cheapest to undo. ^summary

## Statement

Choose the path that is easy to reverse. Take the one-way door only when you must, and then take it
with care.

## Why

We learn after we ship. A reversible choice keeps the cost of being wrong small.

## How it applies

Ask how hard it is to back out. Prefer flags, small steps and narrow scopes.

## Implications

Mistakes stay cheap, and the team can move faster.
