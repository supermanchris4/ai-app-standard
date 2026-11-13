# ADR-0018: API consumers and path naming

## Status

| Status | Date |
|---|---|
| Decided | 2026-10-29 |

## Priority

Must

## Question

How does an application serve more than one kind of client (its own web interface, other systems, a separate front end for external users) without duplicating logic? How are API paths named so that who calls is not confused with what the data is?

## Why it matters

When each interface carries its own logic, behaviour drifts between them and replacing an interface means rebuilding it. "Customer" in a path could mean who is calling or what the data is, and staff work with customer data too.

## Options

| Option | Example | Assessment |
|---|---|---|
| i. A prefix naming the consumer | `/api/v1/customer/accounts/{id}` | Reads as customer data as easily as customer use. Duplicates operations shared by several consumers. |
| ii. Paths name the resource only | `/api/v1/customers/{id}` or `/api/v1/accounts/{id}`, with who may call each operation set by the caller's identity and roles | One operation serves every caller. The path says only what the data is. |
| iii. A channel-neutral prefix for operations published outside the application | `/api/v1/external/accounts/{id}` | Still encodes the consumer in the path, and duplicates shared operations |

## Decision

Every interface is a client of the same API, and features are built as API operations first. Paths follow option ii. Any screens an application provides for external users are clients of the same operations other consumers call.
