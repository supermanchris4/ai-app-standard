# F02.4 Bootstrap network

Part of [E02 Template specifications](README.md) in the [work plan](../README.md). An enabler Feature: it specifies the minimal network the account bootstrap creates for an AWS account that has none.

## Benefits

- An application can be deployed to a new account without building a network by hand.
- The application's own Terraform stays the same whether the network is supplied or created.

## Acceptance criteria

1. The template's `12-aws-deployment.md` specifies the network the bootstrap creates, its inputs and its outputs.
2. Its outputs match the application Terraform's `vpc_id`, `app_subnet_ids` and `lb_subnet_ids` inputs.
3. The monthly cost of the network is stated, with how to reduce it.

## Dependencies

None.

## Stories

### S02.4.1 Specify the minimal network

As an application owner with a new AWS account, I want the bootstrap to create a minimal network, so that I can deploy without designing one.

**Builds on:** [D-05](../../docs/decision-register.md#d-05-network-placement), [D-06](../../docs/decision-register.md#d-06-ingress-and-exposure-model), [D-09](../../docs/decision-register.md#d-09-aws-bootstrap-resources).

**Acceptance criteria:** the Feature's acceptance criteria.

| Task | Description |
|---|---|
| T1 | Specify the VPC, the private subnets in two Availability Zones, the internet gateway CloudFront VPC origins need, and the NAT gateway |
| T2 | Assess VPC endpoints for ECR, CloudWatch Logs, Secrets Manager and S3 against the NAT gateway's cost, and record the choice |
| T3 | State the expected monthly cost of the network, the load balancer and a small RDS instance |
