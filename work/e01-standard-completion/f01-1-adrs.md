# F01.1 ADRs

Part of [E01 Standard completion](README.md) in the [work plan](../README.md). An enabler Feature: it records each decision as an architecture decision record (ADR).

## Benefits

- Each decision's rationale is held once, in a form reviewers recognise.
- The [ADR index](../../adrs/README.md) replaces the register.

## Acceptance criteria

1. Every decision has an ADR in [adrs/](../../adrs/), written from the [ADR template](../../adrs/0000-adr-template.md) and numbered as its register entry was (ADR-0004 records D-04).
2. The [ADR index](../../adrs/README.md) lists every ADR with its status and date, and the register is removed.

## Dependencies

None.

## Stories

### S01.1.1 Record the platform decisions

As the owner of the standard, I want the decisions that shape every application recorded as ADRs, so that their rationale is read in one place.

**Builds on:** [ADR-0001](../../adrs/0001-shape-of-the-outcomes.md) to [ADR-0004](../../adrs/0004-identity-and-authorisation-model.md), [ADR-0018](../../adrs/0018-api-consumers-and-path-naming.md).

**Acceptance criteria:** ADR-0001 to ADR-0004 and ADR-0018 exist and are Decided.

| Task | Description |
|---|---|
| T1 | Write each ADR from its register entry |
| T2 | Check the exact wording of RFC 10017's recommendation before ADR-0003 quotes it |

### S01.1.2 Record the AWS and pipeline decisions

As the owner of the standard, I want the infrastructure and delivery decisions recorded as ADRs, so that the template's deployment specifications cite them.

**Builds on:** [ADR-0005](../../adrs/0005-network-placement.md) to [ADR-0009](../../adrs/0009-aws-bootstrap-resources.md), [ADR-0012](../../adrs/0012-background-processing-pattern.md) to [ADR-0016](../../adrs/0016-observability.md), [ADR-0019](../../adrs/0019-api-exposure.md).

**Acceptance criteria:** ADR-0005 to ADR-0009, ADR-0012 to ADR-0016 and ADR-0019 exist and are Decided.

| Task | Description |
|---|---|
| T1 | Write each ADR from its register entry |

### S01.1.3 Record the remaining decisions

As the owner of the standard, I want the remaining decisions recorded as ADRs, so that every decision is held the same way.

**Builds on:** [ADR-0010](../../adrs/0010-directory-lookups.md), [ADR-0011](../../adrs/0011-local-development-environment.md), [ADR-0017](../../adrs/0017-reporting-and-data-supply.md).

**Acceptance criteria:** ADR-0010, ADR-0011 and ADR-0017 exist and are Decided. ADR-0020, a new decision on how a separate reporting tool reaches an application's data, exists and is Proposed; it is decided when an application first adopts a reporting tool.

| Task | Description |
|---|---|
| T1 | Write each ADR from its register entry |

### S01.1.4 Replace the register with the index

As a reader, I want one index of the ADRs in place of the register, so that a decision's rationale is not held twice.

**Acceptance criteria:** the Feature's acceptance criterion 2.

| Task | Description |
|---|---|
| T1 | Write the index in `adrs/README.md`, with the priorities and statuses, and remove the register |
