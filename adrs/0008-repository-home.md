# ADR-0008: Repository home

## Status

| Status | Date |
|---|---|
| Decided | 2026-10-29 |

## Priority

Must

## Question

Where are the standard, the template and the applications' repositories held?

## Why it matters

The template must be usable from wherever application repositories are created. The pipelines' AWS trust uses the repository's immutable identifier, which includes its owner, so moving a repository to another owner breaks that trust until the roles are re-trusted.

## Decision

Private repositories in a personal GitHub account, with `ai-app-template` marked as a template repository. A repository that later moves to another owner is re-trusted in AWS as part of the move.
