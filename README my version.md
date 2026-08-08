# aws-iam-misconfiguration-investigation
## Overview

AWS Identity and Access Management (IAM) controls who can access AWS and what they are allowed to do.

IAM mainly works through four components:

Users – identities representing people or applications.
Groups – collections of users that share permissions.
Roles – identities that can be assumed temporarily by users, services, or other AWS accounts.
Policies – documents that define allowed or denied actions.

A policy can be thought of as:

Who can do what, to which resource, under what conditions?

An IAM misconfiguration occurs when permissions are broader, weaker, or otherwise different from what the identity actually needs.

Common examples include:

Excessive permissions
Action: "*"
Resource: "*"
Unnecessary administrative privileges
Users belonging to overly privileged groups
Excessive role-assumption permissions
Unused access keys
Missing MFA for sensitive identities
Permissions that allow users to modify IAM itself

The important point is that a broad permission is not automatically malicious. An administrator may legitimately require extensive access. The analyst has to determine whether the permission is appropriate for the identity's purpose.

Why is IAM misconfiguration dangerous?

Imagine an attacker obtains the credentials of a normal employee account.

If the account only has:

s3:GetObject

the attacker's options are limited.

But if the same account has:

iam:*

or:

Action: "*"
Resource: "*"

the compromise could become much more serious.

The attacker may potentially:

Compromised credentials
        ↓
AWS Console / API access
        ↓
Excessive permissions
        ↓
Access sensitive resources
        ↓
Modify security controls
        ↓
Create persistence / escalate privileges

In this hands-on cloud security investigation, an AWS API operation failed because the active AWS Academy identity was explicitly denied permission by an identity-based policy. The investigation then examined the IAM environment, including users, user groups, roles, and policies, to understand the permission model and identify the policy responsible for the access denial.

---

# Executive Summary

AWS SOC investigation lab focused on identifying excessive IAM permissions, analyzing policy configurations, evaluating privilege risks, and documenting least-privilege security recommendations.

---

# Investigation Objectives

An AWS identity has been granted more permissions than it actually needs. As a SOC analyst, our task is to identify the excessive permissions, determine what resources or actions they allow, understand the security impact, and recommend a safer permission model.

---



# Tools Used

- AWS Management Console
- AWS IAM Console
- AWS CloudTrail Console
- IAM Policy Viewer
- AWS Academy Learner Lab

---

# Lab Environment

| Component | Details |
|-----------|---------|
| Cloud Platform | Amazon Web Services |
| Environment | AWS Academy Learner Lab |
| Investigation Type | Cloud Security / IAM Investigation |
| Primary Service | AWS IAM |
| Related Service | AWS CloudTrail |
| Account ID | 406126516422 |
| Active Identity | AWSLabsUser role |
| Primary Artifact | RegionPinningPolicy |
| Investigation Focus | Explicit IAM Policy Deny |

---

# Investigation Workflow

1. Identify the AWS access denial.
2. Examine the denied API operation.
3. Identify the active IAM role.
4. Identify the policy responsible for the denial.
5. Open the IAM Dashboard.
6. Review IAM account resources.
7. Review IAM user groups.
8. Review IAM users.
9. Review IAM policies.
10. Examine an IAM policy document.

---

# Investigation Scenario

An AWS identity has been granted more permissions than it actually needs. As a SOC analyst, our task is to identify the excessive permissions, determine what resources or actions they allow, understand the security impact, and recommend a safer permission model.

---
