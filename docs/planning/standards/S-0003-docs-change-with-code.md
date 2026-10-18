---
type: standard
schema_version: '2'
id: S-0003
state: open/active
title: Docs change with the code they describe
created: 2026-10-18
related: []
tags:
  - kit/base/v1
applies_to:
  paths:
  - '**/*'
need_human_review: false
provenance: kit:base/docs-change-with-code@v1
---
# Docs change with the code they describe

## Summary

- A change that makes a doc wrong also fixes that doc. ^summary

## Rule

Update the docs in the same change as the code they describe.

## Why

Docs that lag behind the code mislead readers.

## How to apply

When you change behaviour, search for the docs that mention it and fix them in the same change.
