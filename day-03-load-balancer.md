# AWS – Day 3: Load Balancers

## What is a Load Balancer?
A load balancer removes the **single point of failure** and enables **horizontal scaling**.

It:
- Sits in front of EC2 instances
- Receives all incoming user traffic
- Distributes requests across multiple **healthy** instances
- Stops sending traffic to unhealthy instances

In short, maintenance, traffic spikes, or failure of one instance does not bring down the application.

---

## Types of AWS Load Balancers

### 1. Application Load Balancer (ALB)
- Operates at **Layer 7 (HTTP/HTTPS)**
- Understands:
  - URLs
  - Paths
  - Headers
  - Hostnames

**Best suited for:**
- Web applications
- REST APIs
- Microservices
- Path-based routing

**Example routing:**
/login → EC2 Target Group A
/images → EC2 Target Group B
---

### 2. Network Load Balancer (NLB)
- Operates at **Layer 4 (TCP/UDP)**
- Does not inspect URLs or headers
- Forwards packets very fast

**Best suited for:**
- Extremely high traffic
- Low latency workloads
- Non-HTTP protocols
- Millions of concurrent connections

In short:  
**NLB is fast, not smart.**

---

### Summary Comparison

**ALB**
- HTTP / HTTPS
- APIs
- Websites
- Path-based routing

**NLB**
- TCP / UDP
- Very high performance
- Low latency
- Gaming, streaming, IoT

---

## Scenario Question 1

**Q:**  
A REST API with endpoints like:
/users
/orders
/payments
Traffic varies by endpoint.  
Which load balancer fits best, and why?

**A:**  
**Application Load Balancer (ALB)**  
Because it operates at Layer 7, understands URLs and paths, and supports path-based routing.

---

## Scenario Question 2

**Q:**  
If:
- One EC2 instance fails
- Auto Scaling replaces it
- ALB is in front

Will users notice downtime? Why or why not?

**A:**  
No, users typically will not notice downtime.

When an instance fails:
- The load balancer stops routing traffic to the unhealthy instance
- Traffic is distributed to remaining healthy instances
- Auto Scaling launches a new instance using the launch template

Instance replacement is not instantaneous, but the load balancer hides this delay by routing traffic only to healthy instances.

---

## Availability Zones (AZs)

An **Availability Zone (AZ)** is a data center within an AWS region.

**Example:**
- Region: `ap-south-1` (Mumbai)
- AZs: `ap-south-1a`, `ap-south-1b`, `ap-south-1c`

AZ characteristics:
- Located kilometers apart
- Close enough for low latency
- Far enough to avoid single disasters affecting all

---

## Important Notes
- EC2 instances do **not** automatically spread across AZs unless configured
- Load balancers have nodes in multiple AZs
- Load balancers automatically survive AZ failures when configured correctly

This makes ALB + multi-AZ deployment a key design pattern for high availability.


