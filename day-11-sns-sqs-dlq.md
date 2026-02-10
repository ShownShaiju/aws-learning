# AWS – Day 11: SNS, SQS & DLQ (Event-Driven Systems)

## Why these services exist at all

In a production environment, you can't have every service talking to every other service directly. If one goes down, the whole chain breaks. We use SNS and SQS to decouple these systems so they can fail or scale independently without crashing the entire app.

If one service fails:
- The whole system should NOT collapse
- Other services should continue working

SNS and SQS exist to **decouple systems**.

---

## What is SNS (Simple Notification Service)

SNS is a **broadcasting service**.

>One to many

We use SNS when:
- One event happens
- Multiple systems need to know about it

Example:
- Order placed
- User signed up
- File uploaded to S3
- Alarm triggered

SNS does NOT process data.
SNS just says:  
**“Hey, something happened.”**

---

## How SNS works

Producer → SNS Topic → Subscribers


- Producer publishes a message
- SNS pushes the message to all subscribers
- Subscribers can be:
  - Lambda
  - SQS
  - Email / SMS
  - HTTP endpoints

SNS uses **push model**.

>Use Case:Real-time notifications
---

## Important thing about SNS

SNS does NOT guarantee processing.

If a subscriber is:
- Down
- Slow
- Broken

The message can be lost.

So SNS alone is NOT reliable for critical processing.

---

## What is SQS (Simple Queue Service)

SQS is a **queue**.

FIFO(First in First Out)

It is used when:
- Work must be processed
- Processing can take time
- Reliability matters

SQS stores messages until a consumer processes them successfully.

>Use Case:Decoupling & Buffering

---

## How SQS works

Producer → SQS Queue → Consumer

- Producer sends message
- Message waits in queue
- Consumer pulls message
- Message is deleted only after success

SQS uses **pull model**.

---

## Why SNS + SQS are often used together (Fan-out)

SNS broadcasts.
SQS stores.

Together:
- One event
- Many independent services
- Guaranteed processing

Example:
Order Service
→ SNS
→ SQS (Inventory)
→ SQS (Billing)
→ SQS (Email)


This pattern is called **fan-out** because:
- One source
- Multiple branches
- Looks like a fan opening

---

## What is DLQ (Dead Letter Queue)

DLQ exists to handle **failed messages**.

Sometimes:
- A message is malformed
- Code has a bug
- Processing always fails

Such messages are called **poison messages**.

---

## Why DLQ is needed instead of deleting messages

If we delete failed messages immediately:
- We lose data
- We lose debugging information
- We hide system issues

DLQ solves this by:
- Moving failed messages to a separate queue
- Stopping retries
- Allowing engineers to inspect later

DLQ = **quarantine for bad messages**

---

## How DLQ works

- SQS retries message processing
- After max retry count:
  - Message is moved to DLQ
- Main queue continues working normally

DLQ prevents:
- Infinite retry loops
- Queue blockage
- Resource wastage

---

**NOTE:**
- SNS → charged per message (cheap)
- SQS → charged per request (cheap)
- DLQ → just another SQS queue (no extra cost)

These services are designed to be cost-effective.

---
**NOTE:**
DLQ is not a special service rather its SQS queue used for special purpose via configuration.A SQS set up for simple isolation .



These services are the backbone of **event-driven architecture**.
