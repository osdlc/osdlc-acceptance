---
type: standard
schema_version: '2'
id: S-0004
state: open/active
title: No secrets in the repository
created: 2026-10-18
related: []
tags:
  - kit/base/v1
applies_to:
  paths:
  - '**/*'
need_human_review: false
provenance: kit:base/no-secrets-in-repo@v1
---
# No secrets in the repository

## Summary

- Keys, tokens and passwords never go into version control. ^summary

## Rule

Do not commit secrets. Keep them in a secret store or the environment.

## Why

A committed secret stays in history and must be treated as leaked.

## How to apply

Load secrets at run time. If one is committed, rotate it at once.
