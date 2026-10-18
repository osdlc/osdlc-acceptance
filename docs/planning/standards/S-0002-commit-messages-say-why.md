---
type: standard
schema_version: '2'
id: S-0002
state: open/active
title: Commit messages say why
created: 2026-10-18
related: []
tags:
  - kit/base/v1
applies_to:
  paths:
  - '**/*'
need_human_review: false
provenance: kit:base/commit-messages-say-why@v1
---
# Commit messages say why

## Summary

- A commit message states the reason for the change, not only what changed. ^summary

## Rule

Every commit message says why the change was made.

## Why

The diff shows what changed. Only the message can say why.

## How to apply

Lead with the reason in the first line or the body. Name the problem the change solves.
