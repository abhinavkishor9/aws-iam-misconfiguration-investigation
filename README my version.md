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

This is why IAM configuration is an important cloud security and SOC investigation area.

In this hands-on cloud security investigation, an AWS API operation failed because the active AWS Academy identity was explicitly denied permission by an identity-based policy. The investigation then examined the IAM environment, including users, user groups, roles, and policies, to understand the permission model and identify the policy responsible for the access denial.

---

# Executive Summary

Hands-on AWS SOC investigation lab focused on identifying excessive IAM permissions, analyzing policy configurations, evaluating privilege risks, and documenting least-privilege security recommendations.

---

# Investigation Objectives

- Understand AWS IAM and permission evaluation.
- Identify the cause of an AWS `AccessDeniedException`.
- Determine which identity attempted the denied operation.
- Identify the denied AWS API action.
- Identify the policy responsible for the denial.
- Review IAM users and user groups.
- Review IAM roles and policies.
- Examine an IAM policy document.


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
| Active Identity | AWSLabsUser assumed role |
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




# SOC Analyst Perspective

When investigating a suspicious AWS identity, don't look only at the username.

Correlate:

Identity
Attached policies
Group membership
Role permissions
Access keys
MFA configuration
Recent CloudTrail activity
Resources accessed
IAM changes performed by the identity

For example:

CloudTrail:
AWSLabsUser performed an unexpected s3:GetObject.

The next question is:

Was this user actually supposed to have S3 access?

IAM answers that question.

---

# Evidence Correlation

The investigation correlated the AWS access-denied message with the IAM environment.

The access-denied error identified:

- AWSLabsUser assumed role
- `cloudtrail:LookupEvents`
- Explicit identity-based policy deny
- `RegionPinningPolicy`

The IAM Dashboard showed:

- 0 IAM users
- 0 IAM user groups
- 26 IAM roles
- 5 IAM policies

The IAM Users and User Groups pages confirmed that no traditional IAM users or groups were present.

The policy investigation demonstrated how IAM policy documents define permissions through structured statements containing fields such as `Effect` and `Action`.

Together, these artifacts provided sufficient evidence to understand the permission failure without assuming that the AWS service itself was malfunctioning.

---

# MITRE ATT&CK Mapping

| Tactic | Technique | ID |
|--------|-----------|----|
| Discovery | Cloud Service Dashboard | T1526 |
| Discovery | Account Discovery | T1087 |
| Discovery | Cloud Account | T1087.004 |

The mappings represent the cloud identity and discovery activities examined during the investigation. They do not by themselves indicate malicious activity.

---

