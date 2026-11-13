# ADR-0020: Reporting tool access to application data

## Status

| Status | Date |
|---|---|
| Proposed | 2026-11-13 |

## Priority

Should

## Question

Where an application's ADR adopts a separate reporting tool ([ADR-0017](0017-reporting-and-data-supply.md)), how does the tool reach the application's database in AWS, what does it read, and how are the application's controls kept for the people who see the reports?

## Why it matters

The database sits in private subnets, and a reporting tool is often a cloud service outside AWS. The application enforces roles and record scope in its API, so a report reading the database directly is outside those checks.

## Sub-decisions

| # | Sub-decision | Options | Position | Status | Date |
|---|---|---|---|---|---|
| a | Network path | A data gateway on a host that can reach the database, one per application; a shared gateway run for many applications; the tool's own managed connection into a private network, which may need a higher licence tier and a VPN or private link to AWS | A shared gateway where several applications report through the same tool, with network access to each application's database subnets granted per application. A gateway per application where only one does. | Proposed | 2026-11-13 |
| b | What the tool reads | The application's tables; a published set of reporting views in a reporting schema | Reporting views. The application can then change its tables without breaking reports, and the views hold only the columns reports need, leaving out audit records and personal information unless the data owner approves. Each new report need adds or changes a view, maintained by the application team. | Proposed | 2026-11-13 |
| c | Access to report data | Each report shown only to an audience entitled to all the data it shows; the tool's row-level security reproducing the application's roles and record scope | An audience entitled to all the data by default. Row-level security only where one report must serve people with different scope, with the rules derived from the application's own scope assignments and reviewed with the data owner. | Proposed | 2026-11-13 |
| d | Database credentials | A read-only reporting role with a password held by the gateway; a role per report | One read-only reporting role per application, with access to the reporting views only. Its rotation updates the gateway's stored credential, so it cannot use RDS-managed rotation unchanged ([ADR-0013](0013-secrets-management-and-rotation.md)). | Proposed | 2026-11-13 |
| e | Storage mode and load | A copy refreshed on a schedule and held in the tool's service; live queries against the database; a read replica for either | Set against the data's classification, because a refreshed copy holds the data outside AWS. A read replica only when report load affects the application. | Proposed | 2026-11-13 |
| f | Export from reports | The tool's default, which often allows summarised and underlying data; summarised data only; no export | No export by default. Summarised data only where a report owner justifies it. | Proposed | 2026-11-13 |

## Depends on

[ADR-0013](0013-secrets-management-and-rotation.md) and [ADR-0017](0017-reporting-and-data-supply.md).

## Recommendation

As in the table. Confidence: Medium on b, c and f; Low on a, d and e until a reporting tool is chosen, because its gateway options, storage modes and export settings decide them.
