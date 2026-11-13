# ADR-0001: Shape of the outcomes

## Status

| Status | Date |
|---|---|
| Decided | 2026-11-12 |

## Priority

Must

## Question

Should the documented standard, the ready-to-use template and the platform code every application needs be one artefact or several, and where should each live?

## Why it matters

Each application copies the template and then owns its copy. Anything in it that should stay common (decisions, rationale, the development process, platform code) drifts in every copy. Separating them too far loses the in-repository specifications Claude needs to read.

## Options

| Option | Description | Assessment |
|---|---|---|
| A. One repository | Template tree plus `docs/standard/` | Simplest to start. Every derived repository inherits a copy of the standard that then drifts. |
| B. Two repositories | `ai-app-standard` (decisions, design, development process, setup guide) and `ai-app-template` (scaffold, generic specifications, workflows, `CLAUDE.md`). Generic specifications cite decision IDs, and the derived repository records the template version it came from. | One source of truth for the "why". The template stays lean. Needs discipline to keep specifications and decisions aligned. Each application generates its own platform code (settings, logging, sign-in, the mock identity provider, Terraform and workflows) from the specifications, so that code drifts in every copy. |
| C. Standard on a wiki or documentation site | The only repository is the template | One repository to maintain. Claude cannot read the wiki from the repository, so the template's specifications must carry everything Claude needs. |
| D. Three repositories | As B, plus `ai-app-platform`, which releases the platform code as versioned packages | As B. An application installs the packages at a version and keeps no copy, so a platform fix reaches every application as a new version instead of being made in each copy. Needs versioned releases and a changelog. |

## Decision

D. `ai-app-platform` releases the API platform, the web platform, the mock identity provider, the Terraform modules and the reusable workflows as versioned packages. The template's specifications say what each package provides and what an application still writes.
