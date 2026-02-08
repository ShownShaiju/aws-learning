# AWS – Day 9: RDS & DynamoDB

## Why databases matter in AWS

EC2 and Lambda are **stateless** by nature.
If an instance crashes, restarts, or scales down, anything stored in memory is gone.

So the real question becomes:

> Where does changing and important data live safely?

That responsibility is handled by **RDS** and **DynamoDB**.

---

## RDS (Relational Database Service)

### What RDS actually provides

RDS is AWS’s managed offering for **SQL databases**, such as:

* MySQL
* PostgreSQL
* MariaDB
* Oracle
* SQL Server

AWS takes care of:

* Operating system
* Patching
* Backups
* Replication
* Failover

From my side, the mental model does not change. I still work with:

* Tables
* Rows
* Columns
* Relationships
* Joins

RDS removes operational work, not relational concepts.

---

## Replication in RDS (two different meanings)

### Multi-AZ (for availability)

Multi-AZ exists purely for **high availability**, not performance.

How it works:

* One primary database in AZ-A
* One standby database in AZ-B
* Synchronous replication
* Every write goes to both at the same time

If the primary fails:

* AWS automatically promotes the standby
* The database endpoint updates
* The application reconnects
* AWS creates a new standby in another AZ

Important points:

* I never connect to the standby directly
* It does not serve reads
* It exists only for failover

Multi-AZ is used when:

* Data correctness is critical
* Automatic failover is required
* Downtime is unacceptable

---

### Read Replicas (for performance)

Read replicas exist for **read scaling**, not availability.

How they work:

* One primary database handles all writes
* One or more replicas handle read-only queries
* Replication is asynchronous
* Small replication lag is expected

Important details:

* Writes always go to the primary
* Replicas never accept writes
* Replicas may return slightly stale data
* Failover is manual, not automatic

Read replicas are useful when:

* SELECT queries dominate
* Reporting and dashboards are common
* Slightly stale data is acceptable

---

## Multi-AZ vs Read Replicas (core distinction)

Multi-AZ is about **survival**.
Read replicas are about **speed**.

Multi-AZ:

* Synchronous replication
* Automatic failover
* No read traffic

Read replicas:

* Asynchronous replication
* Manual failover
* Used for read scaling

These two are often confused but solve completely different problems.

---

## Read-heavy workloads

A workload is considered read-heavy when:

* SELECT operations greatly outnumber INSERT or UPDATE operations

Common examples:

* User profile pages
* Product catalogs
* Social feeds

A key rule:

> One writer, many readers.
> Never many writers.

---

## How applications use read replicas

AWS does not load-balance databases automatically.

Instead, the application decides:

* Writes go to the primary endpoint
* Reads go to replica endpoints

There is no ALB here.
Database connections are stateful and must be managed by the application.

---

## RDS pricing (high level)

You pay for:

* Database instance size
* Storage (GB per month)
* I/O
* Backup storage beyond the free limit

RDS does not have storage tiers like S3.

---

## DynamoDB (NoSQL, serverless)

### What DynamoDB really is

DynamoDB is:

* A key-value and document database
* Fully managed
* Serverless
* Automatically scaling
* Extremely low latency

It is not:

* SQL with JSON
* A relational database replacement
* A system that supports joins

---

## Core mental shift

In SQL:

> I store data first, then decide how to query it.

In DynamoDB:

> I decide how I want to query data, then design storage around that.

This is known as **access-pattern-driven design**.

---

## Core building blocks

* Table: collection of items
* Item: similar to a row
* Attributes: similar to columns, but optional
* Primary key: defines how data is stored and accessed

---

## Primary key (most important concept)

### Partition key

* Determines where data is physically stored
* Cannot be changed after table creation

### Partition key + sort key

* Partition key groups related data
* Sort key orders data within that group

Example:

* Partition key: user_id
* Sort key: timestamp

This allows queries like:

* Get all events for a user
* Get the latest events for a user

---

## Why partition keys cannot be changed

Partition keys determine physical data placement.

Changing them would require:

* Rehashing all items
* Moving data across internal partitions

If access patterns change later:

* Create a new table
* Add a GSI
* Or intentionally duplicate data

This is normal in DynamoDB design.

---

## Global Secondary Indexes (GSI)

GSIs allow:

* Alternate partition keys
* Additional query patterns

Trade-offs:

* Additional cost
* More planning
* Still less flexible than SQL

---

## Horizontal scaling in DynamoDB

Horizontal scaling means adding more machines, not making one machine bigger.

In DynamoDB:

* Tables are split into internal partitions
* Data is distributed automatically
* AWS adds partitions as traffic grows
* I manage nothing manually

This is why DynamoDB can handle:

* Millions of requests per second
* Global-scale applications

---

## Consistency models (exam-relevant)

### Eventual consistency (default)

* Reads may return stale data
* Faster and cheaper
* Scales better

Used for:

* Social feeds
* Likes
* Views
* Analytics

---

### Strong consistency

* Reads always return the latest data
* Slightly higher latency
* Available only within a single region

Used for:

* Authentication
* Authorization
* Financial checks

Important rule:

> Global data cannot be strongly consistent.

---

## Global Tables

Global Tables mean:

* Same DynamoDB table replicated across regions
* Active-active writes
* Eventual consistency only

Used for:

* Global applications
* Low-latency access worldwide
* Disaster recovery

RDS does not support true active-active writes.

---

## DynamoDB pricing (high level)

You pay for:

* Read and write requests
* Storage (GB per month)

There are no retrieval costs or storage tiers like S3.

---

## Choosing between RDS and DynamoDB (real-world thinking)

Use RDS when:

* Relationships matter
* Transactions are required
* Data consistency is critical

Use DynamoDB when:

* Scale is massive
* Access patterns are predictable
* Serverless operation is preferred
* Global active-active writes are needed

Large systems split data based on **criticality**, not convenience.

---

## What I learned today

**Why DynamoDB scales horizontally, not vertically**

DynamoDB does not run on one large database server.
Each table is split into multiple internal partitions.

Each partition:

* Stores a subset of items
* Handles reads and writes independently

As traffic increases:

* AWS adds partitions
* Load is redistributed
* No user intervention is required

---

**Why profile picture updates sometimes appear delayed**

This behavior is caused by **eventual consistency**, not strong consistency.
Different systems update at different times.

---

**Why DynamoDB supports active-active but RDS does not**

DynamoDB:

* Uses simple key-value access
* Has predictable conflict resolution
* Is designed for horizontal scaling

SQL databases involve:

* Transactions
* Joins
* Constraints
* ACID guarantees

Active-active SQL writes across regions would cause conflicts and data corruption.
That’s why RDS uses a single-writer model by design.

---

**Why read replicas are not for availability**

Because:

* Replication lag exists
* Failover is manual
* Writes stop if the primary fails

Multi-AZ is the correct solution for availability.

---

**Can SQL perform key-value lookups?**

Yes, but with more overhead:

* Query parsing
* Execution planning
* Locks
* Transactions

DynamoDB is faster because it intentionally does less work.

---

## Final takeaway

* RDS prioritizes correctness and relationships.
* DynamoDB prioritizes scale and performance.

Both exist because they solve different problems.
