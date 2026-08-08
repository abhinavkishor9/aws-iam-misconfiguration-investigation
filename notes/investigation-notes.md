# Investigation Notes

## Lab Summary

This investigation focused on reviewing the AWS IAM environment and analyzing an access-denied condition encountered while attempting to access CloudTrail Event History.

The investigation examined the IAM Dashboard, IAM Users, IAM User Groups, available IAM policies, and the specific `AccessDeniedException` returned for the `cloudtrail:LookupEvents` operation.

---

## Analyst Methodology

1. Open the AWS IAM console.
2. Review the IAM Dashboard.
3. Examine IAM Users.
4. Examine IAM User Groups.
5. Review available IAM policies.
6. Inspect the `AccessAnalyzerServiceRolePolicy`.
7. Attempt to access CloudTrail Event History.
8. Analyze the `AccessDeniedException`.
9. Identify the requesting IAM role.
10. Identify the denied AWS API action.
11. Identify the policy responsible for the explicit deny.
12. Correlate the IAM findings with the CloudTrail access failure.
13. Document the investigation findings.

---

## Investigation Scenario

While working with AWS cloud monitoring and audit services, an attempt was made to access CloudTrail Event History.

The operation returned an `AccessDeniedException`, indicating that the current AWS Academy identity was not authorized to perform:

`cloudtrail:LookupEvents`

The error also identified an explicit deny associated with:

`RegionPinningPolicy`

The investigation therefore shifted toward IAM to determine what identities, roles, policies, and permissions existed in the AWS account.

---

## Evidence Collected

### Evidence 1 – IAM Dashboard

Collected:

- IAM Dashboard
- Account resource information
- IAM role count
- IAM policy count
- IAM user count
- IAM user group count

Finding:

The IAM Dashboard showed:

- User groups: 0
- Users: 0
- Roles: 26
- Policies: 5
- Identity providers: 0

This indicated that the lab environment relied primarily on IAM roles rather than IAM users.

---

### Evidence 2 – IAM Users

Collected:

`IAM → Users`

Finding:

The IAM Users page showed:

`IAM users (0)`

and:

`No resources to display`

This confirmed that no IAM users were configured in the AWS Academy account.

---

### Evidence 3 – IAM User Groups

Collected:

`IAM → User groups`

Finding:

The IAM User Groups page showed:

`IAM user groups (0)`

and:

`No resources to display`

This confirmed that no IAM user groups were configured in the environment.

---

### Evidence 4 – IAM Policies

Collected:

`IAM → Policies`

Finding:

The IAM environment contained five policies according to the IAM Dashboard.

One of the reviewed policies was:

`AccessAnalyzerServiceRolePolicy`

The policy was examined to understand how IAM permissions are defined through specific AWS API actions.

---

### Evidence 5 – AccessAnalyzerServiceRolePolicy

Collected:

`IAM → Policies → AccessAnalyzerServiceRolePolicy`

The Permissions section displayed the policy document.

The reviewed policy contained permissions including:

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

Finding:

The policy demonstrated that IAM permissions are defined for specific AWS API actions and services.

The presence of permissions for DynamoDB and EC2 does not automatically provide permission to perform CloudTrail operations.

---

### Evidence 6 – CloudTrail Access Failure

Collected:

`AccessDeniedException`

The error stated that the current identity was not authorized to perform:

`cloudtrail:LookupEvents`

Finding:

The CloudTrail lookup operation was denied by AWS IAM authorization controls.

---

### Evidence 7 – Requesting Identity

Collected from the CloudTrail error:

`arn:aws:sts::406126516422:assumed-role/AWSLabsUser-gZ1KBAe5vmFTZBEweyN4/903132b6-c217-447e-b246-04deaec270c6`

Finding:

The request was made through an assumed AWS Academy role:

`AWSLabsUser-gZ1KBAe5vmFTZBEweyN4`

This confirmed that the CloudTrail request was being evaluated against the permissions available to the active assumed role.

---

### Evidence 8 – Explicit Deny

Collected from the CloudTrail error:

`explicit deny in an identity-based policy`

Policy identified:

`arn:aws:iam::406126516422:policy/RegionPinningPolicy`

Finding:

The failure was not simply caused by a missing Allow statement.

AWS identified an explicit deny in the identity-based policy `RegionPinningPolicy`.

This was the primary reason the `cloudtrail:LookupEvents` operation was rejected.

---

## Permission Analysis

The investigation demonstrated an important IAM concept: permissions are evaluated against the specific AWS API action being requested.

The denied operation was:

`cloudtrail:LookupEvents`

The reviewed `AccessAnalyzerServiceRolePolicy` contained permissions for services such as DynamoDB and EC2, but those permissions did not grant access to the CloudTrail lookup operation.

More importantly, the CloudTrail error explicitly identified a deny from:

`RegionPinningPolicy`

Therefore, the investigation identified the authorization failure as an IAM policy enforcement issue rather than a CloudTrail service failure.

---

## IAM Environment Analysis

The IAM Dashboard provided an overview of the account's identity environment.

The account contained:

- 0 IAM users
- 0 IAM user groups
- 26 IAM roles
- 5 IAM policies
- 0 identity providers

The absence of IAM users and groups was consistent with the restricted AWS Academy environment, where temporary assumed roles are commonly used for lab access.

The active identity observed in the CloudTrail error was an assumed role rather than a traditional IAM user.

---

## Access Denial Analysis

The access failure can be reconstructed as follows:

1. The AWS Academy identity attempted to access CloudTrail Event History.
2. AWS evaluated the requested API action.
3. The requested action was `cloudtrail:LookupEvents`.
4. The request was made through the `AWSLabsUser` assumed role.
5. AWS identified an explicit deny.
6. The deny was associated with `RegionPinningPolicy`.
7. The CloudTrail lookup request was rejected.
8. The IAM environment was reviewed to understand the authorization context.

This demonstrates how an access-denied event can itself become useful evidence during a cloud security investigation.

---

## DFIR Analysis

From a SOC perspective, the `AccessDeniedException` should not simply be treated as a technical error.

The event provided several useful investigation artifacts:

- AWS account ID
- Requesting assumed role
- Requested API action
- Explicit deny condition
- IAM policy responsible for the deny

These details allow an analyst to determine why a cloud operation failed and whether the failure was caused by missing permissions, an explicit deny, or another IAM control.

---

## MITRE ATT&CK Mapping

| Tactic | Technique | ID |
|---|---|---|
| Privilege Escalation | Valid Accounts | T1078 |
| Defense Evasion | Impair Defenses | T1562 |
| Discovery | Cloud Service Dashboard Discovery | T1526 |

The primary focus of this lab was IAM authorization analysis rather than direct ATT&CK technique execution. The mapping provides contextual relevance for SOC investigations involving cloud identities and access controls.

---

## Analyst Observations

- The AWS account contained 26 IAM roles but no IAM users.
- No IAM user groups were configured.
- The active CloudTrail request originated from an assumed `AWSLabsUser` role.
- The denied operation was `cloudtrail:LookupEvents`.
- AWS reported an explicit deny rather than simply a missing permission.
- `RegionPinningPolicy` was identified as the policy responsible for the explicit deny.
- IAM policies define permissions for specific AWS API actions.
- An Allow statement for one AWS service does not automatically grant access to another service.
- Permission-denied events can provide valuable evidence during cloud investigations.
- Restricted AWS Academy environments may intentionally prevent administrative operations.

---

## Investigation Findings

The investigation confirmed that the CloudTrail access failure was caused by IAM authorization controls.

The AWS Academy assumed role attempted to perform `cloudtrail:LookupEvents`, but AWS rejected the request because of an explicit deny associated with `RegionPinningPolicy`.

The IAM review also confirmed that the account contained no IAM users or user groups and instead relied heavily on IAM roles.

---

## Conclusion

The investigation demonstrated how SOC analysts can use IAM information to understand cloud access failures. By correlating the CloudTrail `AccessDeniedException` with the active assumed role and the identified `RegionPinningPolicy`, the investigation established the reason for the denied operation and reinforced the importance of analyzing IAM identities, policies, and explicit denies during AWS security investigations.
