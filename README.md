# aws-iam-misconfiguration-investigation

## Overview

AWS Identity and Access Management (IAM) controls who can access AWS resources and which actions they are allowed to perform. Incorrect IAM permissions can prevent legitimate operations, while overly permissive policies can create security risks.

In this hands-on cloud security investigation, an AWS API operation failed because the active AWS Academy identity was explicitly denied permission by an identity-based policy. The investigation then examined the IAM environment, including users, user groups, roles, and policies, to understand the permission model and identify the policy responsible for the access denial.

---

# Executive Summary

This investigation focused on an `AccessDeniedException` generated when attempting to use the CloudTrail `LookupEvents` API. The error identified the active AWSLabsUser assumed role and showed that the request was explicitly denied by the identity-based `RegionPinningPolicy`.

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

## Evidence 1 – Access Denied Error

The AWS console displayed an `AccessDeniedException`.

The error identified the following assumed-role identity:

`arn:aws:sts::406126516422:assumed-role/AWSLabsUser-gZ1KBAe5vmFTZBEweyWrN4/903132b6-c217-447e-b246-04deaec270c6`

The denied operation was:

`cloudtrail:LookupEvents`

The error further stated that the request was denied by an identity-based policy:

`arn:aws:iam::406126516422:policy/RegionPinningPolicy`

### Finding

The error directly identified the cause of the failure.

The AWSLabsUser assumed role attempted to perform `cloudtrail:LookupEvents`, but the request was explicitly denied by the identity-based `RegionPinningPolicy`.

---

## Evidence 2 – IAM Dashboard

The IAM Dashboard displayed the following AWS account resources:

| Resource | Count |
|----------|------:|
| User Groups | 0 |
| Users | 0 |
| Roles | 26 |
| Policies | 5 |
| Identity Providers | 0 |

### Finding

The account contained no IAM users or user groups at the time of investigation.

The environment contained 26 IAM roles and 5 IAM policies.

This was important because the denied operation was associated with an assumed role rather than a traditional IAM user.

---

## Evidence 3 – IAM User Groups

The IAM User Groups page displayed:

`IAM user groups (0)`

and:

`No resources to display`

### Finding

No IAM user groups were configured in the account.

There were therefore no group-based permissions available to review during this investigation.

---

## Evidence 4 – IAM Users

The IAM Users page displayed:

`IAM users (0)`

and:

`No resources to display`

### Finding

No traditional IAM users were present in the account.

The investigation therefore focused on IAM roles and policies rather than long-term IAM user identities.

---

## Evidence 5 – IAM Policy

The investigation also examined the policy:

`AccessAnalyzerServiceRolePolicy`

The policy viewer showed the standard IAM policy structure containing:

- `Version`
- `Statement`
- `Sid`
- `Effect`
- `Action`

The displayed policy contained an `Allow` statement with actions including:

- `dynamodb:GetResourcePolicy`
- `dynamodb:ListStreams`
- `dynamodb:ListTables`
- `ec2:DescribeAddresses`
- `ec2:DescribeByoipCidrs`
- `ec2:DescribeSnapshotAttribute`
- `ec2:DescribeSnapshots`
- `ec2:DescribeVpcEndpoints`
- `ec2:DescribeVpcs`
- `ec2:GetSnapshotBlockPublicAccessState`

### Finding

The policy demonstrated how AWS IAM uses policy statements to define which actions can be performed.

The `Effect` field determines whether an action is allowed or denied, while the `Action` field specifies the AWS API operations covered by the statement.

---

# IAM Permission Analysis

The most important evidence in this investigation was the explicit deny identified in the AWS error.

The permission flow can be understood as:

AWSLabsUser Assumed Role

↓

Requested Action: `cloudtrail:LookupEvents`

↓

IAM Permission Evaluation

↓

Explicit Deny

↓

`RegionPinningPolicy`

↓

`AccessDeniedException`

The AWS error explicitly states that the denial originated from an identity-based policy named `RegionPinningPolicy`.

An explicit deny takes precedence over an allow during AWS policy evaluation.

Therefore, if an applicable policy allows an action but another applicable policy explicitly denies the same action, the explicit deny prevents the operation from succeeding.

---

# SOC Analyst Perspective

IAM permission failures are important during cloud security investigations because they can represent either legitimate administrative restrictions or potentially suspicious attempts to access functionality outside an identity's intended permissions.

During an investigation, a SOC analyst should determine:

- Which identity performed the action?
- Which AWS service was targeted?
- Which API action was requested?
- Was the request allowed or denied?
- Was the denial caused by an explicit policy?
- Which IAM policy generated the denial?
- Was the behavior expected?
- Was the identity operating within its intended permissions?

In this investigation, the AWS error provided several of these answers immediately.

The active identity was an AWSLabsUser assumed role, the requested operation was `cloudtrail:LookupEvents`, and the explicit denial originated from `RegionPinningPolicy`.

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

# Investigation Findings

The investigation produced the following findings:

1. The AWS API request for `cloudtrail:LookupEvents` was denied.
2. The active identity was an AWSLabsUser assumed role.
3. The AWS error explicitly identified an identity-based policy deny.
4. The policy responsible for the denial was `RegionPinningPolicy`.
5. The IAM account contained no IAM users.
6. The IAM account contained no IAM user groups.
7. The account contained 26 IAM roles.
8. The account contained 5 IAM policies.
9. IAM policies use structured statements to define allowed or denied actions.
10. The observed failure was caused by IAM authorization rather than a CloudTrail service failure.

---

# Investigation Limitations

The available evidence confirms that `RegionPinningPolicy` explicitly denied `cloudtrail:LookupEvents`.

However, the available screenshot evidence does not contain the complete JSON contents of `RegionPinningPolicy`.

Therefore, this investigation does not claim:

- That the policy was modified.
- That the explicit deny was removed.
- That CloudTrail access was restored.
- That the configuration represented malicious activity.

The investigation is limited to identifying and analyzing the permission failure shown by the AWS console.

---

# Key Takeaway

AWS IAM is a critical security control because it determines which identities can perform actions within an AWS environment. An explicit deny can override otherwise permitted actions and result in an `AccessDeniedException`. For SOC analysts, understanding the relationship between an AWS identity, requested API action, and IAM policy is essential for investigating cloud permission failures and distinguishing legitimate restrictions from potentially suspicious activity.
