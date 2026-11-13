# E01 Standard completion

Part of the [work plan](../README.md). An enabler Epic: it records every decision as an ADR and makes the checks the decisions still depend on.

## Pitch

Each of the 19 decisions was decided in a decision register, with its rationale held only there. This Epic records each one as an ADR, with the [ADR index](../../adrs/README.md) replacing the register, and makes the few checks that could still change a decision.

## Benefits

- Each decision's rationale is held once, in the ADR form the template and applications already use.
- A check that changes a decision is made before more is built on it.

## Measurable results

- Decisions recorded as ADRs, out of 19.
- Open checks with a recorded result.

## Acceptance criteria

1. Every decision has an ADR in [adrs/](../../adrs/), listed in the [index](../../adrs/README.md), and the register is removed.
2. Each assumption in [configuration-defaults.md](../../docs/configuration-defaults.md#assumptions) is confirmed or its consequence applied.

## Dependencies

None.

## Features

| Feature | Type | Horizon | Detail |
|---|---|---|---|
| F01.1 ADRs | Enabler | Now | [f01-1-adrs.md](f01-1-adrs.md) |
| F01.2 Open checks | Enabler | Now | [f01-2-open-checks.md](f01-2-open-checks.md) |
