# AWS Learning Notes

This repository documents my learning of AWS fundamentals through structured notes, real-world scenarios, and cross-day clarifications.

The goal is to understand **how AWS services behave in real systems**, not just to prepare for exams.

---

## Contents

- `day-01-ec2-basics.md`  
  EC2 fundamentals, instance families, pricing models, and CPU credit behavior.

- `day-02-auto-scaling.md`  
  Auto Scaling concepts including launch templates, desired capacity, scaling policies, and ASG behavior.

- `day-03-load-balancer.md`  
  Application vs Network Load Balancers, multi-AZ design, target groups, and how ALB works with Auto Scaling.

- `day-04-iam-core3.md`  
  IAM users, roles, policies, credential lifecycle, and secure access patterns.

- `day-05-vpc-basics.md`  
  VPC fundamentals including IP addressing, subnets, Internet Gateway, NAT Gateway, and multi-AZ networking design.

- `day-06-s3-and-cloudfront.md`  
  S3 object storage concepts, bucket policies, public access control, static website hosting, and CloudFront as a CDN.

- `day-07-lambda-basics.md`  
  Lambda execution model, event-driven compute, cold vs warm starts, IAM roles, VPC access, and scaling behavior.

- `day-08-s3-lifecycle-and-storageClass.md`
  S3 storage classes, lifecycle policies, cost optimization strategies, minimum storage duration, and retrieval behavior.

- `day-09-rds-and-dynamo-db.md`
  RDS architecture (Multi-AZ vs Read Replicas), database scaling strategies, DynamoDB partitioning, consistency models, and global tables.

- `day-10-api-gateway.md`
  API Gateway architecture, Lambda integration, routing models, and differences from ALB.

- `day-11-sns-sqs-dlq.md`
  Messaging patterns, decoupling systems, SNS vs SQS, and dead-letter queue handling.

- `day-12-route53-and-global-accelerator.md`
  DNS fundamentals, routing policies, health checks, and global traffic optimization.

- `day-13-elastic-ip-&-elastic-cache(Redis).md`
  Elastic IP behavior, static addressing patterns, and caching fundamentals with ElastiCache.

More topics will be added as learning progresses.

---

## How to use this repo

* Each file represents one focused learning session
* Notes evolve as understanding deepens
* Cross-day sections connect services into system-level thinking
* Commit history reflects progression, corrections, and refinements

---

## Why this repo exists

* To build strong AWS and cloud architecture fundamentals
* To move from theoretical understanding to system-level clarity
* To practice disciplined documentation and Git workflows
* To create a visible record of technical growth
