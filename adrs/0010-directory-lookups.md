# ADR-0010: Directory lookups

## Status

| Status | Date |
|---|---|
| Decided | 2026-10-29 |

## Priority

Should

## Question

Where an application needs to look people up in the identity provider's directory (for example Microsoft Graph for Entra ID), should the browser call the directory directly or should lookups go through the API?

## Why it matters

Browser-side directory calls bypass the API-first rule and give every signed-in user a token that can read the whole directory.

## Decision

Through the API, using an application permission scoped as narrowly as the directory allows, with results cached. The endpoint is optional, and an application that needs no lookups leaves it out.
