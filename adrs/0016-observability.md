# ADR-0016: Observability

## Status

| Status | Date |
|---|---|
| Decided | 2026-10-29 |

## Priority

Could

## Question

What monitoring should the template set up by default?

## Why it matters

Without alarms, a failing service goes unnoticed until users report it.

## Evidence

[AWS: ECS monitoring tools](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/monitoring-automated-manual.html); [AWS re:Post: ECS alarming best practices](https://repost.aws/articles/ARWM9LX0HDSXuzjK42wYr0PQ/ecs-incident-detection-and-response-alarming-best-practices); [cloudonaut: monitoring ECS and Fargate](https://cloudonaut.io/how-to-monitor-container-workloads-running-on-ecs-and-fargate/).

## Decision

CloudWatch Logs with the configured retention ([C-09](../docs/configuration-defaults.md#c-09)), and:

- Container Insights enabled on the cluster. It is off by default and is needed for the running-task count.
- Alarms from AWS's recommended set for ECS: running tasks below the desired count, high CPU and memory, load balancer 5xx responses and target response time.
- Structured JSON logs.

If another monitoring platform is used, logs and alarms are forwarded to it from CloudWatch.
