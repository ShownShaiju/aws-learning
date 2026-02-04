# AWS – Day 2: Auto Scaling

## What is Auto Scaling?
Auto Scaling automatically **adds or removes (provisions or de-provisions) EC2 instances** based on demand and health.

---

## Core Components of Auto Scaling

1. Launch Template  
2. Desired Capacity  
3. Minimum Capacity  
4. Maximum Capacity  

---

## Launch Template
A Launch Template is a **blueprint** that AWS uses to create new EC2 instances during scaling events.

It contains:
- **AMI**
- **Instance type**
- **Security groups**
- **IAM roles**
- **User data**

---

## Desired Capacity
Desired capacity is the **number of EC2 instances that must be running and healthy at any given time**.

AWS always tries to ensure the actual number of instances matches the desired capacity.

- Scaling **out** increases desired capacity  
- Scaling **in** decreases desired capacity  

---

## Minimum Capacity
Minimum capacity is the **lowest number of instances that must always be running**.

This means:
- Desired capacity can **never go below minimum capacity**

**Advantages:**
- Ensures baseline availability
- Prevents scale-in from shutting down the application completely

---

## Maximum Capacity
Maximum capacity is the **upper limit on the number of instances** that can be created.

This means:
- Desired capacity can **never exceed maximum capacity**

**Advantages:**
- Prevents uncontrolled scaling
- Protects against unexpected billing spikes

---

## Important Note
AWS Auto Scaling adjusts the number of EC2 instances by **modifying the desired capacity**, keeping it **between the minimum and maximum limits**, based on scaling policies and metrics.

---

## Scenario Question

**If:**
- Minimum = 1  
- Desired = 3  
- Maximum = 6  

And traffic suddenly drops to almost zero.

**Q:** How many instances will still be running, and why?

**A:**  
3 instances will continue running.

Auto Scaling always enforces the **desired capacity** unless a scaling policy changes it.  
A drop in traffic alone does not immediately reduce desired capacity to the minimum.

---

## Stability Principle
AWS prioritizes **stability over panic scaling**.  
Scale-in actions occur only when defined policies are triggered and sustained.

---

## Scale Out vs Scale In

### Scale Out
Adds instances when:
- Load balancer metrics increase
- Request count increases
- CPU utilization crosses a threshold

### Scale In
Removes instances when:
- Traffic drops
- CPU usage remains low for a sustained period

---

## What I Understood Today
I understood how Auto Scaling uses **desired capacity** as the control point and how minimum and maximum limits act as safety boundaries. I also learned that traffic changes alone do not instantly change instance count unless scaling policies are triggered.

---

## Cross-Day Understanding (From Day 5 Learning)

### Auto Scaling Group (ASG)
An Auto Scaling Group (ASG) is a **logical controller** that maintains the desired number of EC2 instances using a launch template.

Auto Scaling is the **service**, and ASG is the **controller inside that service**.

ASG workflow:
- A metric crosses a threshold (CPU, requests, etc.)
- A scaling policy is triggered
- Desired capacity is updated
- ASG adds or removes EC2 instances to match Desired

ASG always chases **Desired Capacity**, bounded by **Minimum and Maximum**.

---

### Question
Who monitors metrics and triggers scaling?

**Answer:**
- CloudWatch monitors metrics
- Scaling policies are attached to the ASG
- When a metric breaches a threshold:
  - CloudWatch alarms fire
  - The alarm triggers the scaling policy
  - The policy updates desired capacity

---

### Question
Who changes desired capacity?

**Answer:**  
The **scaling policy** changes desired capacity.


## Cross-Day Understanding (From Day 7 Learning)
### How ASG knows an instance is unhealthy

ASG can use:
- EC2 health checks (basic)
- ALB health checks (recommended)

Flow:
- Target Group marks instance unhealthy
- ALB removes it from traffic
- ASG terminates and replaces it
---
### Scaling Flow

Traffic increases
->
ALB metrics increase
->
CloudWatch alarm triggers
->
ASG scaling policy fires
->
Desired capacity increases
->
ASG launches new EC2
->
Target Group health checks pass
->
ALB starts routing traffic
