# ADR-0017: Reporting and data supply

## Status

| Status | Date |
|---|---|
| Decided | 2026-10-29 |

## Priority

Should

## Question

Where is reporting built for an application, and how may data leave the application?

## Why it matters

Data taken out of an application loses the controls the application applies to it: roles, record scope, audit and retention.

## Decision

As in the table.

| Need | Where it is built |
|---|---|
| Operational screens | In the application |
| A defined supply of data to another organisation or system | A defined export or integration from the application, recorded each time it runs |
| Reporting in a separate BI tool | Only through an application ADR. The tool reads a published set of reporting views through a read-only database role, never the application's tables, audit records or personal information without the data owner's approval. |
| Generic data export | None. The template provides none, and an application does not add one. |
