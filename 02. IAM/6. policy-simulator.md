## IAM Policy Simulator – In-Depth Explanation

IAM Policy Simulator is a tool used to **test and understand how IAM policies are evaluated** *before or after* they are attached to a user, group, or role.

It answers one core question:

> **“Why is this action allowed or denied?”**

---

## What IAM Policy Simulator Actually Does

The simulator **recreates AWS’s permission evaluation logic** without performing real actions.

It evaluates:

* Identity-based policies (user / group / role)
* Resource-based policies (if applicable)
* Permission boundaries
* Service control policies (SCPs – if in AWS Organizations)
* Conditions (IP, MFA, time, VPC, etc.)
* Explicit Deny vs Allow precedence

It then shows:

* **Allowed / Explicitly denied / Implicitly denied**
* **Which exact policy caused the decision**

---

## Why Policy Simulator Is Critical (Concept Level)

IAM permissions are **not evaluated line by line**.
AWS evaluates them using a **decision engine**:

### IAM Evaluation Order (Very Important)

1. **Explicit Deny** → always wins
2. **Explicit Allow** → only if no deny exists
3. **Implicit Deny** → default (no allow found)

The simulator visualizes this process clearly.

---

## Where to Find IAM Policy Simulator

Two ways:

### Option 1: From IAM Console

* IAM → **Policy Simulator**

### Option 2: From User / Role Page

* IAM → Users / Roles
* Select the identity
* Click **Policy Simulator**

---

## How to Use IAM Policy Simulator (Step by Step)

### Step 1: Select the Identity

Choose:

* IAM User
  or
* IAM Role
  or
* IAM Group

This tells AWS **whose permissions are being tested**.

---

### Step 2: Select the AWS Service

Example:

* EC2
* S3
* IAM
* RDS

Then select a specific action, for example:

* `ec2:RunInstances`
* `s3:PutObject`

---

### Step 3: (Very Important) Add Context Keys

Many permissions depend on **conditions**.

Examples of context keys:

* `aws:SourceIp`
* `aws:MultiFactorAuthPresent`
* `aws:CurrentTime`
* `aws:RequestedRegion`

For IP-based testing:

```
Key: aws:SourceIp
Value: your.current.public.ip
```

Without this step, IP-restricted policies may appear to allow access incorrectly.

---

### Step 4: Run Simulation

The simulator returns:

* **Allowed / Denied**
* A detailed explanation:

  * Which policy allowed it
  * Which policy denied it
  * Whether deny was explicit or implicit

---

## Understanding the Simulator Output

### Example Results

**Allowed**

* Means at least one policy explicitly allowed the action
* No explicit deny was found

**Explicit Deny**

* A deny policy matched
* Access blocked, even if admin permissions exist

**Implicit Deny**

* No allow policy matched
* Default deny behavior

---

## Real Explanation for IP-Based Confusion (Very Common Issue)

Scenario:

* A policy allows EC2 only from IP A
* User still creates EC2 from IP B

Simulator reveals:

* Another policy (Admin / EC2FullAccess) allows EC2
* No explicit deny exists
* So AWS allows it

This is why **Allow + Condition alone is not enforcement**.

---

## Correct Enforcement Pattern (Concept)

To truly restrict access:

* Use **Explicit Deny with NotIpAddress**
* Simulator will then show:

  * Explicit Deny → overrides all allows

This is the **exact reason AWS provides Policy Simulator** — to make this visible.

---

## What Policy Simulator Does NOT Do

* It does **not** create or modify resources
* It does **not** replace real testing
* It does **not** evaluate network-level controls like Security Groups or NACLs
* It does **not** test AWS Console UI flows — only API permissions

---

## When to Use IAM Policy Simulator

* Before attaching a complex policy
* When access works unexpectedly
* When IP / MFA / time conditions behave strangely
* When debugging AdminAccess bypass issues
* When teaching IAM evaluation logic

---

## One Clear Statement

> “IAM Policy Simulator shows how AWS decides Allow or Deny by evaluating all policies together, exactly the same way AWS does internally.”

