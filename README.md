# aws-iam-misconfiguration-investigation

## Overview

AWS Identity and Access Management (IAM) controls who can access AWS resources and which actions they are allowed to perform. Incorrect or overly restrictive IAM policies can prevent legitimate operations, while overly permissive policies can create security risks.

In this hands-on cloud security investigation, an AWS API operation failed because the active AWS Academy identity was explicitly denied permission by an identity-based policy. The investigation then examined the IAM environment, including users, user groups, roles, and policies, to understand the permission model and identify the policy responsible for the access denial.

---

# Executive Summary

This investigation focused on an AWS `AccessDeniedException` generated when attempting to use the CloudTrail `LookupEvents` API. The error identified an explicit deny in the identity-based `RegionPinningPolicy`, which prevented the assigned AWSLabsUser role from performing `cloudtrail:LookupEvents`.

The IAM console was then examined to understand the account's identity structure. The account contained no IAM users or user groups, while multiple IAM roles and policies were present. An IAM policy was also reviewed to understand how AWS policies define allowed and denied actions.

The investigation demonstrated how a SOC analyst can use AWS error messages and IAM configuration evidence to determine why a cloud operation failed and identify the policy responsible for the restriction.

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
- Understand explicit identity-based policy denies.
- Document the investigation findings.

---

# Skills Demonstrated

- AWS IAM Analysis
- IAM Policy Analysis
- AWS Permission Troubleshooting
- Access Denied Investigation
- Cloud Security Monitoring
- CloudTrail Permission Analysis
- AWS Console Investigation
- Identity and Access Management
- Policy Document Analysis
- SOC Investigation Methodology
- Cloud Incident Documentation

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
11. Correlate the IAM configuration with the access-denied error.
12. Document the investigation findings.

---

# Investigation Scenario

During an AWS security investigation, an AWS API operation failed with an `AccessDeniedException`.

The error stated that the active AWSLabsUser assumed role was not authorized to perform:

`cloudtrail:LookupEvents`

The error also identified an explicit deny from:

`RegionPinningPolicy`

The objective of the investigation was to determine why the operation was denied and understand how the IAM configuration controlled access to the requested AWS action.

---

# Evidence Collected

- AWS `AccessDeniedException`
- AWSLabsUser assumed-role identity
- `cloudtrail:LookupEvents` denied action
- `RegionPinningPolicy` reference
- IAM Dashboard
- IAM user groups listing
- IAM users listing
- IAM policy configuration
- AccessAnalyzerServiceRolePolicy policy document

---

# Key Evidence

## Access Denied Error

The AWS console displayed:

```text
AccessDeniedException

User: arn:aws:sts::406126516422:assumed-role/AWSLabsUser-gZ1KBAe5vmFTZBEweyWrN4/903132b6-c217-447e-b246-04deaec270c6

is not authorized to perform:

cloudtrail:LookupEvents

with an explicit deny in an identity-based policy:

arn:aws:iam::406126516422:policy/RegionPinningPolicy
