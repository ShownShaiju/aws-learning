# AWS – Day 12: Route 53 & Global Accelerator

## What Route 53 Actually Is

Route 53 is AWS’s DNS service.

That 
> Convert domain names into IP addresses.


It does NOT:

* Handle traffic
* Distribute load
* Cache content
* Replace ALB or CloudFront

It simply answers:

```
User: where is myapp.com?
Route 53: go to this IP.
```

After that, Route 53 is out of the picture.

---

## DNS Records (A, AAAA, CNAME, Alias)

They are entries inside a Hosted Zone.

Think of a Hosted Zone like a DNS database for one domain.

### A Record

Maps domain → IPv4 address

```
myapp.com → 54.10.20.30
```

### AAAA Record

Maps domain → IPv6 address

### CNAME

Maps domain → another domain

```
www.myapp.com → myalb.amazonaws.com
```

### Alias (AWS special)

Like CNAME but for AWS services.
Better for:

* ALB
* API Gateway
* CloudFront

Use Alias when pointing to AWS services.

---

## What “Routing” Means in Route 53

Route 53 does NOT route packets.

It chooses which DNS answer to give.

That decision happens:

* Once per DNS lookup
* Before any HTTP request is made

It does not sit in the data path.

---

## Route 53 Routing Policies

### Simple Routing

One domain → one destination.

Basic setup.

---

### Weighted Routing

Split traffic by percentage.

Example:

* 80% → old version
* 20% → new version

Used for gradual rollout.

---

### Latency-Based Routing

User goes to region with lowest latency.

India user → Mumbai
US user → Virginia

This is DNS-level decision.

---

### Failover Routing

Primary and secondary setup.

If primary health check fails:

* Route 53 stops returning it
* Starts returning secondary

**NOTE**:
This is region-level failover.

ALB failover is instance-level. Different layer.

---

## Where Route 53 Sits in Architecture

```
User
 ↓
Route 53 (DNS answer)
 ↓
ALB / API Gateway / CloudFront
 ↓
EC2 / Lambda
```

Route 53 is always first.

---

## Now – Global Accelerator

Global Accelerator is NOT DNS.

It gives you:

* 2 static public IPv4 addresses
* Anycast IPs
* Fast failover
* AWS backbone transport

---

## What is Anycast?

One IP address that exists in multiple physical locations.

Same IP:

* Advertised from Mumbai edge
* Advertised from London edge
* Advertised from US edge

Internet automatically routes user to nearest AWS edge.

The IP doesn’t move.
The network chooses the closest entry point.

---

## What is AWS Backbone?

AWS’s private global fiber network connecting:

* Edge locations
* Regions
* Data centers

Once traffic enters AWS edge:

* It leaves public internet
* Travels inside AWS network
* More stable
* Lower latency
* Less packet loss

---

## Exact Flow with Global Accelerator

```
User types myapp.com
 ↓
Route 53 returns Global Accelerator IP
 ↓
User connects to that static IP
 ↓
Traffic enters nearest AWS edge (Anycast)
 ↓
AWS backbone carries traffic internally
 ↓
ALB
 ↓
EC2
```

No DNS delay during failover.
Failover happens at connection level.

---

## Global Accelerator vs Route 53

Route 53:

* DNS-based
* Chooses region
* One decision per DNS query
* Cheap

Global Accelerator:

* Static IP-based
* Uses Anycast
* Per-connection routing
* Faster failover
* More expensive

Route 53 decides where to go.
Global Accelerator gets you there faster.

---

## Global Accelerator vs CloudFront

They both use edge locations, but they are NOT the same.

### Global Accelerator

* Network layer
* No caching
* Accelerates TCP/UDP
* Good for APIs, gaming, real-time systems

### CloudFront

* HTTP layer
* Caches content
* Reduces backend load
* Best for static content and CDN

CloudFront stores.
Global Accelerator transports.
