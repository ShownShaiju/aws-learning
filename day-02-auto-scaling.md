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
I understood how Auto Scaling uses desired capacity as the control point and how minimum and maximum limits act as safety boundaries. I also learned that traffic changes alone do not instantly change instance count unless scaling policies are triggered.
