# AWS – Day 1: EC2 Basics

## EC2 – Virtual Server
An **instance** is a single running virtual server created from an AMI.

---

## EC2 Instance Families

- **T** – Burstable, low-cost, low traffic workloads (testing, small apps)
- **M** – Balanced workloads with consistent CPU
- **C** – Compute-optimized workloads
- **R** – Memory-optimized workloads

**Examples:** `t3.micro`, `m5.large`

---

## AMI (Amazon Machine Image)
An AMI contains:
- Operating system
- Preinstalled software

**Examples:** `Amazon Linux`, `Ubuntu`, `Windows Server`

---

## Pricing Models

### On-Demand Instances
- Pay per second (minimum 60 seconds)
- No commitment
- Most flexible
- More expensive

### Reserved Instances
- Commit for 1 or 3 years
- Much cheaper
- Best for predictable workloads

### Spot Instances
- Use unused AWS capacity
- Very cheap
- Can be terminated anytime by AWS
- Used for batch jobs, background processing, and non-critical workloads

---

## Security
EC2 instances are protected using **Security Groups**, which act as a virtual firewall.

---

## CPU Credits (T Family)
- T instances have a low baseline CPU
- CPU credits accumulate when usage is below baseline
- Credits are consumed when bursting above baseline
- At baseline usage, credits neither increase nor decrease

---

## Instance Size Explanation

Example: `t3.micro`

- `t` → instance family
- `3` → hardware generation (newer is usually better)
- `micro` → instance size

Instance size affects:
- CPU
- RAM
- Network bandwidth

Larger size → higher baseline performance.

---

## Scenario Question

**Q:** If you need a cheap server for a low-traffic web app that may suddenly get short spikes, which EC2 family makes sense and why?

**A:**  
T-family on **On-Demand pricing**, because it is low-cost, supports CPU bursting during short spikes, and does not require long-term commitment.

---

## What I Understood Today
I learned how the T-family CPU credit system works and why it is suitable for workloads with low baseline usage and occasional spikes. I also understood why M-family instances cost more, since they provide consistent CPU performance without relying on credits.

T-family instances can burst independently as long as CPU credits are available and instance limits are not exceeded. Bursting is not directly affected by other tenants due to AWS’s isolation mechanisms (Nitro system).