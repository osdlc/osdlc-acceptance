---
type: principle
schema_version: '2'
id: P-0001
state: open/published
title: Prefer small reviewable changes
created: 2026-10-18
related: []
tags:
  - principle/project
  - kit/base/v1
need_human_review: false
provenance: kit:base/small-reviewable-changes@v1
---
# Prefer small reviewable changes

## Summary

- Split work into changes a reviewer can hold in their head. ^summary

## Statement

Make each change cover one concern. Keep it small enough to review in one sitting.

## Why

A small change gets a real review. A large one gets a skim.

## How it applies

Split refactors from behaviour changes. Land work in steps.

## Implications

Reviews are quick, reverts are cheap, and history reads clearly.
