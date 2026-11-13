# ADR-0004: Identity and authorisation model

## Status

| Status | Date |
|---|---|
| Interim | 2026-11-13 |

## Priority

Must

## Question

How should internal users, external users and other systems authenticate and be authorised, and how does local development work without a real identity provider?

## Why it matters

Every deployed environment needs real token validation, with simulated sign-in only on developers' machines, and an authorisation model to go with it.

## Sub-decisions

| # | Sub-decision | Options | Position | Status | Date |
|---|---|---|---|---|---|
| a | Identity provider | A named provider (Entra ID, Cognito, Auth0); any OpenID Connect provider configured by discovery URL | Any OpenID Connect provider, configured by its discovery URL, with Entra ID as the documented default for internal users ([C-01](../docs/configuration-defaults.md#c-01)). The code holds no provider-specific logic beyond claim names. | Decided | 2026-10-29 |
| b | Source of roles | App roles in the token's `roles` claim; groups; an application role table | App roles. They arrive in the token, need no extra lookup, and are assigned in the provider. | Decided | 2026-10-29 |
| c | App registrations | One shared by web and API; separate web and API registrations; one confidential registration per issuer, plus one per consumer | One application registration per issuer, as a confidential web client that also exposes the API's scope and app roles. Each consumer system has its own registration. There is no separate web registration, because the browser does not sign in by itself. | Decided | 2026-10-29 |
| d | Role taxonomy | A fixed set of roles; application-defined roles under a standard pattern | Application-defined roles named `{area}.{action}`, with human and machine roles separate and system roles prefixed `system.`. There is no role hierarchy: each route lists every role it allows. | Decided | 2026-10-29 |
| e | Local development | Skip authentication when unset; a client-supplied role header; a local mock issuer that signs test tokens validated by the same code path | Local mock issuer. Production code then has no bypass branch. | Decided | 2026-10-29 |
| f | External users | None; a second issuer validated alongside the internal one | Optional. An application with external users, such as customers or people from other companies, adds a second issuer (Entra External ID by default). | Decided | 2026-10-29 |
| g | Calls a consumer makes for an external user | The user's own access token passed to the API; the consumer's own system token, with the user's identity asserted in the request | The user's own token for anything about that user, so the API itself enforces that they reach only their own records. The consumer's system token only for calls not about one user, such as reference data. | Decided | 2026-10-29 |
| h | External user authorisation | Roles only; roles plus a record-level check | A record-level check in the service layer. Roles alone cannot set which records an external user can see, so each application defines that. | Decided | 2026-10-29 |
| i | Time within which withdrawn access ends ([AR-15](../docs/design.md#application-rules)) | At the next sign-in, so within the session's maximum lifetime (10 hours by default); at a periodic re-check of the person's account and roles during the session; at once, through the identity provider's revocation events | A periodic re-check, at a configured interval, with the session ending when the person's account is disabled or their roles are removed. The template reads roles only at sign-in today, so a disabled person keeps access for up to the session's maximum lifetime ([OWASP ASVS 5.0](https://github.com/OWASP/ASVS), requirement 7.4.2). | Proposed | 2026-11-13 |

## Decision

Sub-decisions a to h as in their rows. Sub-decision i has no position adopted yet.
