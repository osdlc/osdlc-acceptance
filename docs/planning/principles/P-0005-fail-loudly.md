---
type: principle
schema_version: '2'
id: P-0005
state: open/published
title: Fail loudly, not silently
created: 2026-10-18
related: []
tags:
  - principle/technical
  - kit/base/v1
need_human_review: false
provenance: kit:base/fail-loudly@v1
---
# Fail loudly, not silently

## Summary

- A broken state should stop and say so. ^summary

## Statement

Report an error where it happens. Do not swallow it or guess a default.

## Why

A silent failure hides the problem until it is expensive to fix.

## How it applies

Return or raise a clear error that names the cause. Never catch and ignore.

## Implications

Problems show up early, close to their cause.
