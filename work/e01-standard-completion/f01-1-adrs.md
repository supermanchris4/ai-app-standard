# F01.1 ADRs

Part of [E01 Standard completion](README.md) in the [work plan](../README.md). An enabler Feature: it records each decision as an architecture decision record (ADR).

## Benefits

- Each decision's rationale is held once, in a form reviewers recognise.
- The register becomes an index.

## Acceptance criteria

1. Every decision has an ADR in [adrs/](../../adrs/), written from the [ADR template](../../adrs/0000-adr-template.md) and numbered to match (ADR-0004 records D-04).
2. Each register entry shows only its ID, title, status and a link to its ADR.

## Dependencies

None.

## Stories

### S01.1.1 Record the platform decisions

As the owner of the standard, I want the decisions that shape every application recorded as ADRs, so that their rationale is read in one place.

**Builds on:** [D-01](../../docs/decision-register.md#d-01-shape-of-the-outcomes) to [D-04](../../docs/decision-register.md#d-04-identity-and-authorisation-model), [D-18](../../docs/decision-register.md#d-18-api-consumers-and-path-naming).

**Acceptance criteria:** ADR-0001 to ADR-0004 and ADR-0018 exist and are accepted.

| Task | Description |
|---|---|
| T1 | Write each ADR from its register entry |
| T2 | Check the exact wording of RFC 10017's recommendation before ADR-0003 quotes it |

### S01.1.2 Record the AWS and pipeline decisions

As the owner of the standard, I want the infrastructure and delivery decisions recorded as ADRs, so that the template's deployment specifications cite them.

**Builds on:** [D-05](../../docs/decision-register.md#d-05-network-placement) to [D-09](../../docs/decision-register.md#d-09-aws-bootstrap-resources), [D-12](../../docs/decision-register.md#d-12-background-processing-pattern) to [D-16](../../docs/decision-register.md#d-16-observability), [D-19](../../docs/decision-register.md#d-19-api-exposure).

**Acceptance criteria:** ADR-0005 to ADR-0009, ADR-0012 to ADR-0016 and ADR-0019 exist and are accepted.

| Task | Description |
|---|---|
| T1 | Write each ADR from its register entry |

### S01.1.3 Record the remaining decisions

As the owner of the standard, I want the remaining decisions recorded as ADRs, so that every decision is held the same way.

**Builds on:** [D-10](../../docs/decision-register.md#d-10-directory-lookups), [D-11](../../docs/decision-register.md#d-11-local-development-environment), [D-17](../../docs/decision-register.md#d-17-reporting-and-data-supply).

**Acceptance criteria:** ADR-0010, ADR-0011 and ADR-0017 exist and are accepted.

| Task | Description |
|---|---|
| T1 | Write each ADR from its register entry |

### S01.1.4 Shrink the register

As a reader, I want the register to be an index once the ADRs exist, so that a decision's rationale is not held twice.

**Acceptance criteria:** the Feature's acceptance criterion 2.

| Task | Description |
|---|---|
| T1 | Shrink each entry to its ID, title, status and a link, as its ADR is accepted |
