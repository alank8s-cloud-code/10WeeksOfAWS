# Day 4 - IAM Hands-On Lab

## Overview

Today I practiced **AWS Identity and Access Management (IAM)** using users, groups, AWS managed policies, and a custom policy.

The main goal of this lab was to understand the **Principle of Least Privilege**.

> Give a user only the permissions they need to perform their job.

In this lab, I created read-only access for different AWS services and tested both **allowed** and **denied** actions.

---

# 1. What is AWS IAM?

## What?

**AWS Identity and Access Management (IAM)** is an AWS service used to control:

* Who can access AWS resources
* What actions they can perform
* Which resources they can access

IAM mainly works with:

* Users
* Groups
* Policies
* Roles
* Permissions

For this lab, the main components were **users, groups, and policies**.

---

## Why?

Without IAM permissions, every user could potentially have more access than necessary.

For example:

A person who only needs to **view S3 buckets** should not be able to:

* Delete buckets
* Delete objects
* Create resources
* Modify AWS services

Least privilege reduces the chance of accidental or unauthorized changes.

---

## How?

The basic IAM permission flow used in this lab was:

```text
IAM User
   |
   v
IAM Group
   |
   v
IAM Policy
   |
   v
AWS Resource
```

For example:

```text
learner-s3
     |
     v
S3ReadOnlyGroup
     |
     v
AmazonS3ReadOnlyAccess
     |
     v
S3
```

The user gets the permissions through the group.

---

# 2. Practice Rule - Least Privilege

## What?

The **Principle of Least Privilege** means giving only the minimum permissions required to perform a task.

For this lab, I used **read-only access**.

I did not provide full administrative permissions.

---

## Why?

Giving unnecessary permissions creates security risks.

For example:

```text
Required:
View S3 objects

Bad:
AdministratorAccess
```

Instead, the user should receive something closer to:

```text
S3 Read Only
```

This makes the environment safer and easier to control.

---

## How?

I followed this order:

```text
1. Create Group
       ↓
2. Attach Read-Only Policy
       ↓
3. Create User
       ↓
4. Add User to Group
       ↓
5. Test Allowed Access
       ↓
6. Test Denied Access
```

This helped me understand how IAM permissions are assigned and evaluated.

---

# 3. Lab 1 - S3 Read-Only Access

## Task

Create an IAM group, attach the S3 read-only policy, create a user, and test the permissions.

### Resources

| Resource | Name                     |
| -------- | ------------------------ |
| Group    | `S3ReadOnlyGroup`        |
| Policy   | `AmazonS3ReadOnlyAccess` |
| User     | `learner-s3`             |

---

## What?

`AmazonS3ReadOnlyAccess` is an AWS managed policy that provides read-only access to Amazon S3.

The user should be able to view S3 information but should not be able to make changes.

---

## Why?

This demonstrates how to give someone access to **view S3 resources without giving them write or delete permissions**.

For example, a developer or auditor might need to inspect S3 resources without being allowed to modify them.

---

## How?

### Step 1 - Create the group

Create:

```text
S3ReadOnlyGroup
```

---

### Step 2 - Attach the policy

Attach:

```text
AmazonS3ReadOnlyAccess
```

to the group.

---

### Step 3 - Create the user

Create:

```text
learner-s3
```

---

### Step 4 - Add the user to the group

Add:

```text
learner-s3
```

to:

```text
S3ReadOnlyGroup
```

The permission relationship becomes:

```text
learner-s3
     |
     v
S3ReadOnlyGroup
     |
     v
AmazonS3ReadOnlyAccess
     |
     v
Amazon S3
```

---

## Testing

Sign in as:

```text
learner-s3
```

### Test 1 - Allowed action

Try to open and view S3 resources.

Expected result:

```text
Allowed
```

---

### Test 2 - Denied action

Try to perform a write/delete operation.

Expected result:

```text
Access Denied
```

This proves that the user has read-only permissions.

---

## Expected Learning

I learned that attaching a read-only policy to a group allows users in that group to inherit the required permissions.

---

# 4. Lab 2 - EC2 Read-Only Access

## Task

Create an IAM group with EC2 read-only permissions and assign it to a user.

### Resources

| Resource | Name                      |
| -------- | ------------------------- |
| Group    | `EC2ReadOnlyGroup`        |
| Policy   | `AmazonEC2ReadOnlyAccess` |
| User     | `learner-ec2`             |

---

## What?

`AmazonEC2ReadOnlyAccess` is an AWS managed policy that provides read-only access to EC2 resources.

The user can inspect EC2 resources but should not be able to create or terminate instances.

---

## Why?

An administrator is not always required to manage EC2 resources.

For example, someone may only need to:

* View instances
* Check instance status
* Inspect configuration
* View EC2 information

They do not need permission to create or terminate instances.

---

## How?

### Step 1 - Create the group

Create:

```text
EC2ReadOnlyGroup
```

---

### Step 2 - Attach the policy

Attach:

```text
AmazonEC2ReadOnlyAccess
```

---

### Step 3 - Create the user

Create:

```text
learner-ec2
```

---

### Step 4 - Add the user to the group

Add:

```text
learner-ec2
```

to:

```text
EC2ReadOnlyGroup
```

Permission flow:

```text
learner-ec2
     |
     v
EC2ReadOnlyGroup
     |
     v
AmazonEC2ReadOnlyAccess
     |
     v
Amazon EC2
```

---

## Testing

Sign in as:

```text
learner-ec2
```

### Test 1 - Allowed action

Open the EC2 dashboard and view EC2 resources.

Expected:

```text
Allowed
```

---

### Test 2 - Denied action

Try to create an EC2 instance.

Expected:

```text
Access Denied
```

---

### Test 3 - Denied action

Try to terminate an EC2 instance.

Expected:

```text
Access Denied
```

---

## Expected Learning

I learned that EC2 read-only permissions allow users to inspect EC2 resources without giving them permission to change infrastructure.

---

# 5. Lab 3 - Billing Read-Only Access

## Task

Create a group with billing read-only permissions and assign it to a user.

### Resources

| Resource | Name                       |
| -------- | -------------------------- |
| Group    | `BillingViewGroup`         |
| Policy   | `AWSBillingReadOnlyAccess` |
| User     | `learner-billing`          |

---

## What?

`AWSBillingReadOnlyAccess` provides read-only access to supported AWS billing information.

The goal is to allow the user to view billing information without giving them permissions to manage unrelated AWS resources.

---

## Why?

Different people in an organization may need to monitor AWS costs without being AWS administrators.

For example:

```text
Finance Team
     |
     v
View AWS Billing
```

They may not need:

```text
Create EC2
Delete S3
Modify IAM
Create VPC
```

This is another example of least privilege.

---

## How?

### Step 1 - Create the group

Create:

```text
BillingViewGroup
```

---

### Step 2 - Attach the policy

Attach:

```text
AWSBillingReadOnlyAccess
```

---

### Step 3 - Create the user

Create:

```text
learner-billing
```

---

### Step 4 - Add the user to the group

Add:

```text
learner-billing
```

to:

```text
BillingViewGroup
```

Permission flow:

```text
learner-billing
       |
       v
BillingViewGroup
       |
       v
AWSBillingReadOnlyAccess
       |
       v
AWS Billing
```

---

## Testing

Sign in as:

```text
learner-billing
```

### Test 1 - Allowed action

Open the Billing Dashboard and view available billing information.

Expected:

```text
Allowed
```

---

### Test 2 - Unrelated AWS service

Try to perform an action that requires permissions outside the billing read-only policy.

Expected:

```text
Access Denied
```

---

## Expected Learning

I learned that IAM permissions can be designed around a specific responsibility instead of giving users broad access to AWS.

---

# 6. Lab 4 - Custom S3 Read-Only Policy

## Task

Create a custom customer-managed IAM policy that provides read-only access to a specific S3 bucket.

Policy name:

```text
CustomS3ReadOnlyTrainingPolicy
```

---

## What?

Instead of using an AWS managed policy, I created my own policy.

The custom policy gives three types of permissions:

```text
ListAllMyBuckets
       +
ListBucket
       +
GetObject
```

The important difference is that the bucket-specific permissions are limited to:

```text
YOUR-BUCKET-NAME
```

---

## Why?

AWS managed policies are useful when they match the requirement.

However, sometimes an organization needs more specific permissions.

For example:

```text
User
  |
  +---- View bucket list
  |
  +---- List one specific bucket
  |
  +---- Read objects from that bucket
```

The user does not need broad S3 permissions.

This is a practical example of **least privilege**.

---

## How?

Create a customer-managed policy named:

```text
CustomS3ReadOnlyTrainingPolicy
```

Use the following structure and replace:

```text
YOUR-BUCKET-NAME
```

with the actual bucket name.

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
        "s3:ListAllMyBuckets"
      ],
      "Resource": "*"
    },
    {
      "Effect": "Allow",
      "Action": [
        "s3:ListBucket"
      ],
      "Resource": "arn:aws:s3:::YOUR-BUCKET-NAME"
    },
    {
      "Effect": "Allow",
      "Action": [
        "s3:GetObject"
      ],
      "Resource": "arn:aws:s3:::YOUR-BUCKET-NAME/*"
    }
  ]
}
```

---

# 7. Understanding the Custom Policy

## Statement 1 - List S3 Buckets

```json
{
  "Effect": "Allow",
  "Action": [
    "s3:ListAllMyBuckets"
  ],
  "Resource": "*"
}
```

### What?

Allows the user to list S3 buckets.

### Why?

The S3 console may need permission to display the available buckets.

### How?

The action:

```text
s3:ListAllMyBuckets
```

allows listing buckets.

The resource is:

```text
*
```

because this action operates at the account level rather than being restricted to one bucket ARN.

---

# 8. Statement 2 - List One Specific Bucket

```json
{
  "Effect": "Allow",
  "Action": [
    "s3:ListBucket"
  ],
  "Resource": "arn:aws:s3:::YOUR-BUCKET-NAME"
}
```

### What?

Allows the user to list the contents of one specific bucket.

### Why?

The user only needs access to the training bucket.

### How?

Replace:

```text
YOUR-BUCKET-NAME
```

with the actual bucket name.

For example:

```text
arn:aws:s3:::my-training-bucket
```

The user can list that bucket but does not automatically get access to every bucket.

---

# 9. Statement 3 - Read Objects

```json
{
  "Effect": "Allow",
  "Action": [
    "s3:GetObject"
  ],
  "Resource": "arn:aws:s3:::YOUR-BUCKET-NAME/*"
}
```

### What?

Allows the user to read objects inside the specified bucket.

### Why?

The user needs to download or view objects without modifying or deleting them.

### How?

The:

```text
/*
```

means objects inside the bucket.

For example:

```text
arn:aws:s3:::my-training-bucket/*
```

allows reading objects stored inside that bucket.

---

# 10. Understanding Allow and Deny

IAM evaluates whether an action is permitted based on the applicable policies.

In this lab, the custom policy contains:

```text
Effect: Allow
```

for specific read actions.

There is no permission to:

```text
PutObject
DeleteObject
CreateBucket
DeleteBucket
```

Therefore, those actions should not be allowed by this policy.

The basic idea is:

```text
Requested Action
       |
       v
Does a policy allow it?
       |
   +---+---+
   |       |
  YES      NO
   |       |
   v       v
Allowed  Denied
```

---

# 11. Submission Evidence

For the custom S3 policy lab, I should capture evidence showing that the configuration and testing were completed.

## Screenshots

Capture:

* IAM group
* IAM user
* Attached policy
* Allowed S3 access
* Denied access

---

## Policy File

Save the custom policy as a JSON file.

Example:

```text
CustomS3ReadOnlyTrainingPolicy.json
```

Suggested submission structure:

```text
day-04/
├── README.md
├── CustomS3ReadOnlyTrainingPolicy.json
└── screenshots/
    ├── s3-group.png
    ├── s3-user.png
    ├── s3-policy.png
    ├── allowed-access.png
    └── denied-access.png
```

---

# 12. Lab Summary

| Lab   | Group              | Policy                           | User                | Main Goal                 |
| ----- | ------------------ | -------------------------------- | ------------------- | ------------------------- |
| Lab 1 | `S3ReadOnlyGroup`  | `AmazonS3ReadOnlyAccess`         | `learner-s3`        | S3 read-only access       |
| Lab 2 | `EC2ReadOnlyGroup` | `AmazonEC2ReadOnlyAccess`        | `learner-ec2`       | EC2 read-only access      |
| Lab 3 | `BillingViewGroup` | `AWSBillingReadOnlyAccess`       | `learner-billing`   | Billing read-only access  |
| Lab 4 | Custom             | `CustomS3ReadOnlyTrainingPolicy` | Training user/group | Bucket-specific S3 access |

---

# 13. What I Learned

Through this lab, I learned:

* What AWS IAM is
* Why IAM is important for AWS security
* How IAM users work
* How IAM groups work
* How policies provide permissions
* How users inherit permissions from groups
* How AWS managed policies work
* How customer-managed policies work
* How to provide read-only access
* How to test allowed permissions
* How to test denied permissions
* How the Principle of Least Privilege works
* How S3 permissions can be restricted to a specific bucket
* Why users should not receive unnecessary permissions

---

# 14. Important IAM Concepts

## User

An IAM user represents an identity that can authenticate to AWS.

Example:

```text
learner-s3
```

---

## Group

A group is a collection of IAM users.

Example:

```text
S3ReadOnlyGroup
```

Policies can be attached to groups so multiple users can receive the same permissions.

---

## Policy

A policy is a JSON document that defines permissions.

Example:

```text
Effect → Allow
Action → s3:GetObject
Resource → specific bucket objects
```

---

## Permission

A permission determines whether an identity can perform an action on a resource.

Example:

```text
s3:GetObject
```

means permission to read an S3 object.

---

# 15. Overall IAM Permission Flow

The complete learning flow from this lab is:

```text
                  IAM
                   |
        +----------+----------+
        |          |          |
      Users     Groups     Policies
        |          |          |
        +----------+----------+
                   |
                   v
              Permissions
                   |
                   v
              AWS Services
        +----------+----------+
        |          |          |
       S3         EC2       Billing
```

A practical example:

```text
learner-s3
     |
     v
S3ReadOnlyGroup
     |
     v
AmazonS3ReadOnlyAccess
     |
     v
Read S3
     |
     +---- View → ALLOWED
     |
     +---- Delete → DENIED
```

---

# 16. If I Get Stuck

I should submit the part I successfully completed instead of giving up on the entire lab.

For example:

```text
I completed S3 read-only access.
I got stuck while testing EC2 denied access.
```

This makes it clear what was completed and where the problem occurred.

---

# 17. Interview Relevance

### Q: What is IAM?

IAM is an AWS service used to manage identities and control access to AWS resources.

### Q: What is the Principle of Least Privilege?

It means giving an identity only the permissions required to perform its job.

### Q: Why use IAM groups?

Groups make it easier to manage permissions for multiple users by attaching policies to the group.

### Q: What is an IAM policy?

An IAM policy is a JSON document that defines which actions are allowed or denied on which resources.

### Q: What is the difference between AWS managed and customer-managed policies?

AWS managed policies are maintained by AWS, while customer-managed policies are created and maintained by the AWS customer.

### Q: Why test denied access?

Testing denied actions confirms that the user does not have unnecessary permissions and helps verify least-privilege access.

---

# 18. Final Takeaway

The main lesson from Day 4 is:

> **Do not give users more AWS permissions than they need.**

Instead of:

```text
User
  |
  v
AdministratorAccess
```

prefer:

```text
User
  |
  v
Specific Group
  |
  v
Required Policy
  |
  v
Required AWS Resource
```

This is the foundation of secure AWS access management and an important concept for DevOps and cloud engineering.

---

## Day 4 Status

* [x] Create IAM groups
* [x] Attach read-only policies
* [x] Create IAM users
* [x] Add users to groups
* [x] Test allowed access
* [x] Test denied access
* [x] Understand AWS managed policies
* [x] Create a custom S3 read-only policy
* [x] Understand least privilege
* [x] Document the lab

