# AWS – Day 12: Elastic IP & ElastiCache (Redis)


# What is Elastic IP (EIP)

Elastic IP is a **static public IPv4 address** provided by AWS.

Normally in AWS:

* Public IPs change if you stop/start EC2.

ie, They are dynamic.

Elastic IP does NOT change.

we can attach it to:

* EC2 instance
* NAT Gateway

It stays ours until we release it.

---

## Why Does Elastic IP Exist?

Because normal public IPs are temporary.

If you:

* Stop instance
* Or it crashes

Public IP may change.

That’s bad if:

* Firewall is whitelisting your IP
* External system depends on fixed IP
* DNS already pointed to that IP

Elastic IP solves that.

---

##  Does NAT Come With Public IP?

Yes.

When you create NAT Gateway:

* You must attach an Elastic IP.

So NAT does NOT magically “borrow from pool”.
It uses a real, dedicated Elastic IP.

If NAT is deleted → that EIP can be reused or released.

---

## Is Elastic IP Free?

Here’s the trick.

Elastic IP is:

* Free **only when attached to a running resource**

You pay if:

* It’s allocated but not attached
* Or attached to stopped instance

AWS doesn’t like people hoarding IPv4.

---

## Do We Normally Attach Elastic IP to EC2?

In production — rarely.

Better pattern:

* EC2 in private subnet
* ALB in public subnet
* ALB handles public access

Why?
Because attaching public IP directly to EC2 increases attack surface.

Elastic IP is mostly used for:

* NAT Gateway
* Bastion Host
* Rare legacy setups

### Limitation
>You can only have 5 Elastic IPs per region by default. If you try to allocate a 6th one, AWS will block you. You have to open a support ticket to get more.

---

# ElastiCache (Redis)

### What Is Redis?

Redis is NOT an AWS invention.

It is:

* An open-source in-memory data store
* Key-value based
* Extremely fast

It stores data in RAM, not disk.

Think of it as:
Ultra-fast temporary memory layer.

---

##  What Is ElastiCache?

ElastiCache is AWS-managed Redis (or Memcached).

Meaning:
AWS runs Redis for you.
You don’t manage EC2, OS, or installation.

It runs inside your VPC.

It has:

* Private IP
* ENI
* Security Groups

So yes — it lives inside your network.

---

## Why Do We Need Redis?

Databases like RDS:

* Store data on disk
* Slower

If same data is requested 1000 times:
Instead of hitting RDS every time,
We cache it in Redis.

Flow:

User → App → Redis
If found → return
If not → fetch from RDS → store in Redis → return

This reduces:

* DB load
* Latency
* Cost

---

## What Happens If Redis Crashes?

Nothing catastrophic.

Worst case:

* Cache cleared
* Next request hits database
* Slight latency spike

Redis is performance optimization.
Not source of truth.

---

## Can Redis Be Used With Lambda?

Yes.

Common for:

* Session storage
* Rate limiting
* Caching API responses

But:
It must be inside VPC.
Lambda must be attached to VPC to access it.

---

## Does Redis Replace DynamoDB?

No.

Redis = Cache
DynamoDB = Database

Redis does not permanently store critical data.

---

## Redis Pub/Sub 

Redis can also:

* Publish message
* Other service subscribes

Used for:

* Real-time notifications
* Chat systems
* Event systems


---

## When To Use Redis?

Use it when:

* Read-heavy workload
* Repeated queries
* Session management
* Leaderboards
* Rate limiting


