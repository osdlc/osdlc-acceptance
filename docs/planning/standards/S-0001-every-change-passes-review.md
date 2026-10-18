---
type: standard
schema_version: '2'
id: S-0001
state: open/active
title: Every change passes review before it merges
created: 2026-10-18
related: []
tags:
  - kit/base/v1
applies_to:
  paths:
  - '**/*'
need_human_review: false
provenance: kit:base/every-change-passes-review@v1
---
# Every change passes review before it merges

## Summary

- A change merges only after it meets the project's review policy. ^summary

## Rule

Each change meets the project's review policy before it merges: every required judgement (people,
review agents or other judgement checks) has signed off, and CI and the deterministic checks pass.

## Why

Independent judgement catches mistakes and spreads knowledge, and automated checks catch what
judgement should not have to.

## How to apply

Open a pull request and merge it once the review policy is satisfied and CI passes.
