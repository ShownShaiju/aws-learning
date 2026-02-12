# AWS – Day 8: S3 Lifecycle & Storage Classes

## S3 Lifecycle

### What is an S3 Lifecycle Policy?

A lifecycle policy is a rule that automatically moves objects between storage classes (or deletes them) based on time.

Its like saying "After X days, store this data in a cheaper tier."

No manual work. No scripts. AWS does it

---

### Why lifecycle policies exist

S3 Standard is expensive.
Most data:
- Is hot only for a short time
- Then becomes cold

Lifecycle = cost optimization

A lifecycle policy is:
- A time-based rule
- Attached to an S3 bucket
- That moves objects between storage classes
---
## Storage Classes

### S3 Standard
- Frequent access
- Highest Cost
- Multi-AZ
- Immediate access

It is used when Data is actively used or during the first phase of lifecycle

Example:
- Website assets
- User-uploaded images (active)
- Application data
- Anything frequently accessed

**NO Minimum Storage duration**

### S3 Standard-IA(Imfrequent Access)
- Lower storage cost
- Immediate access
- Retrieval cost exists
- Multi-AZ

Used when:
- Data must be available instantly
- But accessed rarely

Example:
Invoices older than 6 months, rarely opened but must load instantly
**Minimum Storage duration**: 30 days
### S3 One Zone-IA
- Cheaper than Standard-IA
- Single AZ
- Lower availability
- Immediate access

Use ONLY when:
- Data is re-creatable
- Availability is not critical

**NOTE**: S3 One Zone-IA is the only single AZ S3 class rest are designed to be multi-AZ.

**NOTE**:
If data importance is not explicitly low -then not choose this

**Minimum Storage duration**: 30 days
### S3 Glacier Instant Retrieval
- Archive tier
- Immediate access
- Higher retrieval cost
- Designed for very rare access

Use when:
- Almost never accessed
- But must be immediately retrievable

**Minimum Storage duration**: 90 days

Example:

Legal records that might be requested once a year

### S3 Glacier (Flexible Retrieval)
- Minutes to hours retrieval 
- Very cheap
- Long-term archival
**Minimum Storage duration**: 90 days

Example:

System logs retained for 7 years

### S3 Glacier Deep Archive
- hours retrieval 
- cheapest 
- Long-term archival
**Minimum Storage duration**: 180 days

Example:

Medical records kept for decades
---
### Lifecycle transition Example

You can define rules like:
- Day 0 → S3 Standard
- Day 30 → S3 Standard-IA
- Day 90 → Glacier Instant
- Day 365 → Delete

**Lifecycle rules are:**
- Prefix-based (e.g. /images/*)
- Tag-based
- Bucket-wide
---
## What I Learned Today

**Question:** Some Storage Buckets have Low Storage cost but have Retrieval cost.What all are considered as "Retrievel"
**Answer:**
Examples that count as retrieval:
- Downloading a file
- Serving an image to a user
- A browser loading a static website file
- An application reading an object

while 
- Uploading a file
- Deleting a file
-Transitioning via lifecycle 
isn't counted as retrieval

**Question:**What are re-creatable data?

**Answer:**
Data that even if lost that can be generated again
Example: Tumbnails generated from original images

One Zone-IA stores data in one AZ only.

If that AZ dies:
- Data is gone
- No recovery

So AWS expects you to use it only for data you can recreate.

**Question:** What Does minimum Storage Duration means?What happens if i try to move the File before minimum duration?Can it be bypassed?
**Answer:**
Minimum duration means the minimum amount of time an object needed to be stored in the bucket but it dosn,t means we cant move it what it actually means is that aws is gonna bill you for that period of time whether u completly use it or not and cannot be bypassed because it’s enforced at billing level.

i.e,Minimum storage duration does NOT prevent lifecycle transitions.
It only affects billing

---

# Cross-Day Understanding (From Day 13 Learning)


## What is Versioning 

When versioning is enabled:

Every time I:

* Upload
* Update
* Delete

S3 does NOT remove old data.

Instead it creates versions.

Example:

```
report.pdf (v1)
report.pdf (v2)
```

If I delete the object:

S3 creates something called a **delete marker**.

Delete marker:

* Becomes the latest version
* Makes object appear deleted
* But older versions still exist underneath

This is why versioning protects from:

* Accidental deletion
* Overwriting
* Ransomware attacks

It does NOT stop attack.
It prevents permanent data loss.

---

## How does Delete Marker work

When I delete object with versioning enabled:

S3:

* Adds a delete marker
* Does NOT delete previous versions

If I want the file back:

* I remove the delete marker
* Or restore older version

So deletion becomes reversible.

That’s the whole point.
```
[v3] Delete Marker (Current)  <-- App sees "404 Not Found"
[v2] report.pdf               <-- Still exists, costing money
[v1] report.pdf               <-- Still exists, costing money
```
---

## What is Object Lock

Used when:
Data must NOT be deleted for fixed period.

Requires:

* Versioning enabled

Two modes:

---

## Governance Mode

Admins with special permission can override.

Used when:

* Company wants control
* Legal team may remove if needed

---

## Compliance Mode

No one can delete.
Not even root user.

Used for:

* Financial records
* Government regulation
* Legal mandates

If question says:
“Even administrator must not delete”

Answer → Compliance mode.

---

## What is Legal Hold

Different from retention period.

Retention:

* Time based
* Auto expires

Legal Hold:

* No expiry
* Must be manually removed

Used during:

* Investigation
* Audit
* Lawsuit

It freezes the object.

---

## What is Replication

Replication copies objects automatically.

Requires:

* Versioning enabled
* IAM role

---

## CRR (Cross Region Replication)

Region A → Region B

Used for:

* Disaster recovery
* Data residency
* Global redundancy

S3 assumes IAM role to replicate data.
Bucket policy alone is not enough.

> Hidden Cost: Cross-Region Replication (CRR) isn't just a copy. You pay Data Transfer fees for every byte that leaves Region A to go to Region B. Don't turn this on for massive buckets unless you have a budget.

---

## SRR (Same Region Replication)

Same region, different bucket.

Used when:

* Separate access control required
* Analytics team needs copy
* Compliance separation

Not as common but exam may mention it.

---

## Encryption (Server-Side Encryption)

SSE = Server-Side Encryption

Data is encrypted before being stored.

---

### SSE-S3

AWS manages encryption key.
Simple.
Default option.

---

### SSE-KMS

Using AWS Key Management Service (KMS) for encrytion.

Gives:

* Audit trail
* Key rotation
* Fine-grained control

---

### SSE-C

Customer provides encryption key.

Rarely Used.

More operational complexity.

---
