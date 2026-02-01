# AWS – Day 5: VPC Basics

## What is a VPC?
A VPC (Virtual Private Cloud) is **your own private network inside AWS** where your EC2 instances live.

In simple terms:
- It’s my internal network
- Resources inside it can talk to each other
- Internet is not required for internal communication
- It is **regional**
- Completely isolated from other AWS customers

---

## IP Addressing in AWS

Inside a VPC, I choose a **private IP range**, for example:
10.0.0.0/16


These IPs are:
- Private IP addresses (like home networks)
- Not reachable from the internet
- Free to use

**Notes:**
- AWS does not give public IP ranges by default because IPv4 is limited and costly
- In AWS, unused public IPv4 addresses still incur cost  
  (AWS essentially discourages wasting them)

---

## Subnets

A subnet is a **logical slice of a VPC’s IP range**.

Example:
10.0.1.0/24
Used to assign private IPs to EC2 instances.

### Key Rule
A subnet belongs to **exactly ONE Availability Zone (AZ)**.

### Example

Subnet A:
10.0.1.0/24 → AZ-A

- EC2-1: `10.0.1.10`
- EC2-2: `10.0.1.11`

Subnet B:
10.0.2.0/24 → AZ-B

- EC2-3: `10.0.2.10`

This is how AWS enforces **multi-AZ design**.

### What is NOT allowed
An EC2 in AZ-B **cannot** use:
10.0.1.1
10.0.1.20

or any IP from `10.0.1.0/24`, because that range belongs to AZ-A.

### Why AWS enforces this
- AZs are failure boundaries
- Subnets spanning AZs would break isolation
- AWS forces explicit high-availability design

---

## Internet Gateway (IGW)

An Internet Gateway is the **connection between a VPC and the internet**.

It is required for **any internet communication**.

Used by:
- Load Balancers (inbound traffic)
- NAT Gateways (outbound traffic)

---

## Public vs Private Subnets

A subnet is **public or private based on routing**, not IP addresses.

---

### Public Subnet

A subnet is public if it has a route like: 
 0.0.0.0/0 → Internet Gateway

Used for:
- Load Balancers
- NAT Gateways
- Bastion hosts (legacy setups)

---

### Private Subnet

A subnet is private if it **does not route to the Internet Gateway**.

- Cannot be reached directly from the internet

Used for:
- EC2 application servers
- Databases

---

## NAT Gateway

NAT = Network Address Translation.

A NAT Gateway:
- Lives in a **public subnet**
- Has a **public IP**
- Allows private EC2 instances to access the internet
- Blocks inbound connections from the internet

### Traffic Flow
Private EC2 → NAT Gateway → IGW → Internet
Internet → IGW → NAT Gateway → Private EC2


NAT is used for:
- Software updates
- Calling external APIs
- Secure outbound internet access

NAT is **never used for user-facing traffic**.

---

## Scenario

If a private EC2 instance needs to install updates:
- It sends the request to the NAT Gateway
- NAT tracks which private IP made the request
- NAT forwards the request to the internet
- Response is routed back to the correct EC2 instance

---

## Cost Reality Notes
- Private IPs → free
- Public IPv4 addresses → limited and costly

---

## What I Learned Today

### Question:
Does more NAT Gateways mean more public IPs and higher cost?

**Answer:**  
Yes. Each NAT Gateway requires a public IP, and each AZ needs its own NAT Gateway for high availability.  
More AZs → more NAT Gateways → higher cost.

---

### Question:
Is it possible to use a single NAT Gateway across multiple AZs?

**Answer:**  
Technically yes, but it’s a bad idea.

Why:
- NAT Gateways are AZ-scoped
- Sharing one NAT across AZs creates a single point of failure
- Adds latency

If AZ-A goes down:
- NAT goes down
- AZ-B instances lose internet access

---

### Question:
Do we need multiple NAT Gateways inside a single AZ?

**Answer:**  
No. One NAT Gateway per AZ is enough because AWS already makes the NAT Gateway highly available **within that AZ**.

