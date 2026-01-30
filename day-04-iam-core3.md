# AWS – Day 4: IAM Core Concepts

IAM (Identity and Access Management) is a security and permission management service in AWS.

It controls **who can access what** inside an AWS account.

---

## Core IAM Concepts

1. User  
2. Role  
3. Policy  

---

## IAM Users

An IAM user represents:
- A **person**, or
- An **external application/tool**

IAM users have **direct AWS access** using credentials.

**Examples:**
- Admin user  
- Read-only user  
- Developer user  
- Intern user (practically a guest user)

---

### What I Learned

Above all IAM users, there is the **AWS root user**.

- There can be many admin users
- But there is **only one root user**
- The root user can bypass all restrictions

**Best practices:**
- Never use the root user for daily work
- Secure it with MFA
- Create IAM users for actual humans

Earlier CI/CD pipelines often used IAM users with access keys.  
Modern best practice is to use **IAM roles instead**.

---

## IAM Roles

IAM roles:
- Have **no long-term credentials**
- Cannot be used to log in directly
- Are **assumed temporarily** by something

They can be assumed by:
- EC2
- Lambda
- Other AWS services
- Even IAM users

IAM users can assume a role when a lower-privileged user needs **temporary access** to higher-level permissions.

---

### What I Learned

IAM roles are **temporary in usage**, which reduces long-term security risk.

Key points:
- Roles themselves do **not expire**
- Credentials issued from roles **do expire**
- This prevents credential leakage from being useful long-term

IAM roles are mainly used for **internal AWS systems**.

Permissions come from **policies attached to the role**.

In EC2:
- The role is referenced in the **launch template**
- EC2 automatically assumes the role
- Credentials die when the instance dies

---

### How IAM Roles Actually Work (Precise Model)

**IAM Role itself:**
- Is permanent
- Exists in IAM until deleted
- Does not die with the EC2 instance

**Credentials from the role:**
- Are temporary
- Issued to the EC2 instance
- Automatically rotated
- Expire every few hours (typically around 6 hours)

Even if an EC2 instance runs for months or years, AWS:
- Rotates credentials automatically
- Injects new temporary credentials
- Expires old credentials

All of this happens **without human intervention**.

---

## IAM Policies

An IAM policy is a **JSON document** that defines:
- What actions are allowed or denied
- On which resources

Policies can be attached to:
- A User
- A Role
- A Group

**Example permissions:**
- Allow EC2 to read from S3
- Deny deleting DynamoDB tables
- Allow writing logs to CloudWatch

---

### What I Learned

Policies are initially written by the **root user**.

Flow:
1. Root user creates the first IAM admin
2. Admin users or admin roles then create and manage policies

By default:
- **Everything is denied**
- Except for the root user

Permissions exist **only if explicitly allowed**.

---

## Scenario Question

An application running on EC2 needs to:
- Read from S3
- Write logs to CloudWatch

**What would you do? Explain the process.**

### Answer

1. Create an IAM policy allowing:
   - S3 read access
   - CloudWatch log write access
2. Attach the policy to an IAM role
3. Attach the role to the launch template
4. Auto Scaling launches EC2 instances with that role

This allows secure access without storing credentials on the instance.
