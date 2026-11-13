# ADR-0012: Background processing pattern

## Status

| Status | Date |
|---|---|
| Decided | 2026-10-29 |

## Priority

Should

## Question

Should the template define a standard pattern for scheduled and asynchronous work?

## Why it matters

Most line-of-business applications have some scheduled or batch work, such as data loads from other systems, exports, notification processing and raising alarms when a time threshold passes. Each application inventing its own pattern defeats the template.

## Options

| Option | Use |
|---|---|
| EventBridge Scheduler starting an ECS task from the API image with a different command | Scheduled batch and loads |
| SQS queue with a worker ECS service | Asynchronous work triggered by API actions |
| Step Functions | Multi-step processing with retries and compensation |

## Evidence

AWS documents EventBridge Scheduler as the way to schedule ECS tasks. It is more scalable than EventBridge scheduled rules and runs cron schedules in any time zone ([AWS: scheduling ECS tasks with EventBridge Scheduler](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/tasks-scheduled-eventbridge-scheduler.html)).

## Decision

EventBridge Scheduler scaffolded; the SQS worker and Step Functions documented for applications that need them.
