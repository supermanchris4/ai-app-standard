# ADR-0006: Ingress and exposure model

## Status

| Status | Date |
|---|---|
| Decided | 2026-10-29 |

## Priority

Must

## Question

How do people and other systems reach the application, and is it reachable from the internet?

## Why it matters

The exposure model decides which edge services are used, how traffic to the API is encrypted and whether a web application firewall is needed. AWS recommends HTTPS between CloudFront and the load balancer. CloudFront VPC origins let the load balancer sit inside the network, reachable only through CloudFront, replacing the older approach of a public load balancer protected by a shared secret header.

## Options

| Option | Description | Assessment |
|---|---|---|
| A. Public ALB with ACM | ALB in public subnets, HTTPS, path routing | Simple. No edge caching for static files. |
| B. CloudFront to S3 and to a public ALB | Single origin, no CORS, default CloudFront certificate; the ALB accepts only requests carrying a shared secret header | No domain needed. CloudFront connects to the ALB over HTTP. |
| C. CloudFront to S3 and to an internal ALB as a VPC origin | As B, with AWS WAF on the distribution and an internal ALB as a CloudFront VPC origin over HTTPS. The ALB security group admits only the CloudFront managed prefix list. | Encrypted end to end, and the ALB is not reachable from the internet. Needs a certificate for the ALB, and so a domain. VPC origins do not support IPv6-only subnets. The VPC must have an internet gateway attached, which does not route traffic to the ALB. |
| D. Internal only | Internal ALB reachable over a private network or VPN | Smallest exposure. Excludes anyone not on that network. |

## Evidence

[AWS: CloudFront with Application Load Balancers](https://aws.amazon.com/blogs/networking-and-content-delivery/optimizing-application-performance-the-strategic-benefits-of-combining-amazon-cloudfront-with-application-load-balancers); [AWS re:Post: CloudFront VPC origins](https://www.repost.aws/knowledge-center/cloudfront-vpc-origins); [AWS: CloudFront VPC origins developer guide](https://docs.aws.amazon.com/AmazonCloudFront/latest/DeveloperGuide/private-content-vpc-origins.html); [AWS: cross-account VPC origins](https://aws.amazon.com/about-aws/whats-new/2025/11/amazon-cloudfront-cross-account-vpc-origins).

## Decision

C as the template default, with D documented for applications whose users are all on a private network.
