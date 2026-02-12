# AWS – Day 6: S3 Core Concepts and CloudFront

## What is S3?
S3 (Simple Storage Service) is **object storage**, not a disk and not a server.

- Think of it like Google Drive for applications
- Used to store files such as images, videos, backups, logs, and static website assets
- Nothing “runs” inside S3 (no OS, no database, no backend logic)
- It is **regional** and fully managed by AWS

**In short:**  
S3 stores *dead data*, not running software.

---

## Core S3 Terms

### Bucket
A bucket is a container that holds objects in S3.

- Buckets are **regional**
- Bucket names must be **globally unique**
  (If someone anywhere in the world uses a name, no one else can)
- Creating a bucket is free
- You pay only for what you store and use (pay-per-use)

---

### Object
An object is the actual data stored in a bucket.

It consists of:
- The file (data)
- Metadata

---

### Key
A key is the **name of the object**.

**Example:**
Bucket: my-app-data

Key: images/profile/user1.jpg


S3 does **not** have real folders.  
The `/` is just part of the object name and is used for logical organization.

Keys are also used in URLs to access objects.

---

## S3 Scaling & Availability

- S3 scales automatically
- No size planning required
- No Multi-AZ configuration needed
- AWS replicates data across multiple AZs by default

---

## Cost Reality

You **don’t pay** for:
- Creating buckets

You **do pay** for:
- Storage used (GB per month)
- Requests (PUT, GET, LIST, etc.)
- Data transfer out of AWS

---

## How S3 Is Accessed

S3 is **not inside your VPC**.

So there is:
- No subnets
- No NAT Gateway
- No Internet Gateway
- No Load Balancer

Users access S3 via **AWS-managed public endpoints**.

**Flow:**
User → Internet → S3 Endpoint


AWS handles:
- Networking
- Scaling
- Availability

---

## S3 Permissions

### Default Behavior
- Buckets are **private by default**
- No access unless explicitly allowed

---

### IAM Policy (Identity-Based)
Used when AWS identities need access to S3, such as:
- EC2
- Lambda
- IAM users

**Example:**
An EC2 instance uploads logs to S3.

**Flow:**
EC2 → IAM Role → S3


IAM policies control **who** can access S3.

---

### Bucket Policy (Resource-Based)
Used when access is based on the **bucket itself**, such as:
- Public access
- CloudFront access
- Cross-account access

Bucket policies control **who can access this bucket**.

Object-level control is possible.

**Example:**
/public/* → public

/private/* → restricted


Implemented using bucket policy conditions.

---

### Public Access Block (Safety Switch)
Public Access Block is a **bucket-level safety feature**.

When enabled:
- Public bucket policies are ignored
- Even if a policy allows public access, AWS blocks it

This exists to prevent accidental data leaks.

For static websites:
- Public Access Block must be **intentionally adjusted**

---

## Static Website Hosting with S3

S3 can host static websites consisting of:
- HTML
- CSS
- JavaScript
- Images

**Limitations:**
- No backend logic
- No server-side code

Used for:
- Portfolio websites
- Frontend apps
- Simple static sites

Buckets must be public **or** fronted by CloudFront.

---

## Why CloudFront Exists

**Problem:**
- An S3 bucket exists in one region
- Global users experience higher latency

**Solution:** CloudFront

---

## What is CloudFront?
CloudFront is a **Content Delivery Network (CDN)**.

In simple terms:
- Caches content closer to users
- Serves files from nearby edge locations
- Reduces load on S3
- Improves speed and security

CloudFront never owns data.  
It stores cached copies **temporarily**.

---

## CloudFront Caching

Cache duration is controlled using **TTL (Time To Live)**.

- TTL can be seconds, minutes, hours, or days
- Static content → long TTL
- Frequently changing content → short TTL

Caching behavior is configurable.

---

## CloudFront + S3 (Best Practice)

Instead of making S3 public:

- Keep S3 **private**
- Allow access only from CloudFront using a bucket policy
- Users access CloudFront, not S3 directly

**Flow:**
User → CloudFront → Private S3


**Benefits:**
- Better security
- No direct S3 exposure
- Global performance
- Built-in DDoS protection

---

## Does CloudFront Need IAM?

No.

CloudFront:
- Is **not** an IAM identity
- Uses bucket policies for access
- Uses Origin Access Control (OAC)

IAM is used for:
- EC2
- Lambda
- IAM users

Bucket policies are used for:
- CloudFront
- Public access
- Cross-account access

---

## Geo Restrictions

CloudFront can:
- Allow only specific countries
- Block specific countries

Useful for:
- Licensing
- Regional compliance
- Cost control

---

## CloudFront URL & DNS

CloudFront provides a default URL:
dxxxxx.cloudfront.net


DNS is optional:
- Route 53
- Or any external DNS provider

DNS simply maps:
mydomain.com → CloudFront URL

---

## What CloudFront Is NOT

- Not backend logic
- Not a database
- Not a replacement for ALB
- Not for internal-only traffic

CloudFront is for **content delivery**, not computation.

---

## Final Mental Model

- S3 → stores files
- CloudFront → delivers files globally
- EC2 → runs code
- EBS → disk for EC2
- IAM → controls AWS identities
- Bucket Policy → controls S3 access
- Public Access Block → safety override
- DNS → maps names to endpoints

---

## What I Learned Today

### Question:
Why not just use bucket policy instead of CloudFront?

**Answer:**  
Bucket policies control access, but CloudFront adds:
- Speed
- Global caching
- Security features

Bucket policy alone is fine for demos or small projects.  
CloudFront is for production.

---

### Question:
Does S3 need NAT or Internet Gateway?

**Answer:**  
No. S3 is outside the VPC and fully managed by AWS.

---

### Question:
Can IAM alone make S3 public?

**Answer:**  
No. Public users are not IAM identities.  
Public access requires bucket policies or CloudFront.


# Cross-Day Understanding (From Day 13 Learning)

### Intelligent Tiering (Advanced Storage Layer)

Intelligent Tierning is used when access pattern is unknown and cant make a effective lifecycle policy:

In Intelligent Tiering aws moniter the access pattern and automatically move storage class.

AWS:

* Monitors access
* Moves object between frequent and infrequent tiers

No manual lifecycle required.

Small monitoring cost.

Used when:
Future access pattern uncertain.

```
NOTE:
Intelligent Tiering has a minimum object size of 128KB for auto-tiering. If I upload millions of tiny 10KB files, they:
- Never move to infrequent access (stuck in high cost).
- Still incur the monitoring fee per object. Rule: Only use this for larger files (images, backups, videos), not tiny logs.
