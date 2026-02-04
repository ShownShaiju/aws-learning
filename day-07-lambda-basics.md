# AWS – Day 7: Lambda Basics

## What is Lambda?

AWS `Lambda` is **compute without servers**.  
It is an **event-driven compute service**.

Lambda is a function that:
- Runs when an event happens
- Runs for a short time
- Then disappears

**Note:**  
Unlike EC2 and similar to S3, Lambda is **fully managed by AWS** and is **regional**.

You don’t manage:
- OS
- Disks
- Scaling

You just upload code, and AWS runs it when needed.

---

## Lambda vs EC2

### EC2
- Always ON
- Has OS
- Has EBS
- Has IP (private/public)
- You pay even if idle

### Lambda
- Runs only when triggered
- No OS access
- No persistent disk (only temporary `/tmp`)
- No IP by default
- You pay only when it runs

Lambda is **not a “small EC2”**.  
It is a completely different execution model.

---

## How Lambda Is Triggered

Lambda doesn’t sit and wait like a server.

It is triggered by events such as:
- HTTP requests (via API Gateway)
- File uploads to S3
- Database changes
- Scheduled time (cron)
- CloudWatch / EventBridge events

**Examples:**
- “When a file is uploaded to S3 → run Lambda”
- “Every day at 2 AM → run Lambda”

Scheduling is handled by **EventBridge** (AWS’s cron replacement).

---

## Is Lambda Inside My VPC?

By default:
- Lambda is **NOT** inside your VPC
- It runs in AWS-managed infrastructure (similar to S3)

This allows:
- Easy access to AWS services
- No NAT, subnet, or route table configuration

Lambda is attached to a VPC **only if needed**, for example:
- Accessing private EC2 instances
- Accessing RDS in a private subnet

VPC attachment is **optional**, not default.

---

## Cold Start vs Warm Start

Lambda execution environments are temporary.

- First request after idle → **cold start**
- Subsequent requests → **warm start**

Cold start happens when:
- Lambda hasn’t run for some time
- A new execution environment is created
- Lambda is attached to a VPC (slower due to ENI creation)

Warm starts are faster because AWS reuses the environment.

**Important:**  
Lambda code must always assume **each execution is fresh and stateless**.

---

## Stateless Nature of Lambda

Lambda:
- Does NOT remember previous executions
- Does NOT keep state in memory
- Does NOT have persistent disk

Temporary storage (`/tmp`) exists but is **not reliable**.

Persistent data must be stored in:
- S3
- DynamoDB
- RDS

---

## Where Is Lambda Code Stored?

Lambda code is stored by AWS.

Important distinction:

**Code storage ≠ execution memory**

Lambda code:
- Is uploaded by the user
- Stored persistently by AWS
- Loaded when Lambda runs

Execution memory:
- Temporary
- Destroyed after execution

This is why Lambda has:
- No launch template
- No EBS
- No OS access

---

## Permissions (IAM Role)

Lambda uses an **IAM Role**.

The role defines:
- Which AWS services Lambda can access
- No access keys are stored
- Credentials are temporary

Same security model as EC2 IAM roles.

---

## Lambda Scaling

Lambda scales automatically.

- 1 request → 1 execution
- 100 requests → ~100 executions
- Scaling is horizontal by default

There is:
- A concurrency limit (default ~1000)
- Limit can be increased by AWS request

Lambda scaling does **not** use ASG or Load Balancers.

---

## Execution Time Limit

Lambda has a **maximum execution time of 15 minutes**.

Because of this, Lambda is **not suitable** for:
- Long-running processes
- Stateful applications
- OS-level custom software
- Heavy compute running for long durations

---

## Use Cases of Lambda

- API backends
- Event processing
- File transformations
- Automation jobs
- Glue logic between AWS services

---

## What I Learned Today

- Lambda is event-driven, stateless compute where AWS runs my code only when something happens.
- I learned about **EventBridge**, which is AWS’s equivalent of Linux cron.
- I also learned about **ENI (Elastic Network Interface)**, which Lambda uses to access VPC resources.
- ENI creation takes time, which increases cold start latency for VPC-attached Lambdas.
