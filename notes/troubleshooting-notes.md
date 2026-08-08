# Troubleshooting Notes

## Issue 1

CloudTrail Event History could not be accessed.

### Error

`AccessDeniedException`

AWS reported that the current identity was not authorized to perform:

`cloudtrail:LookupEvents`

### Cause

The AWS Academy lab role was restricted by an explicit deny in the identity-based policy:

`RegionPinningPolicy`

### Resolution

The issue was not caused by an incorrect CloudTrail configuration.

The error was analyzed as an IAM authorization restriction.

The important details were:

- Action: `cloudtrail:LookupEvents`
- Result: Access Denied
- Deny Type: Explicit deny
- Policy: `RegionPinningPolicy`

The investigation was continued by reviewing the IAM configuration and available policies.

---

## Issue 2

IAM Users page displayed no resources.

### Observation

The IAM Users page showed:

`IAM users (0)`

and:

`No resources to display`

### Cause

No IAM users had been created in the AWS Academy account.

### Resolution

No remediation was required.

The absence of IAM users was documented as an investigation finding.

The IAM Dashboard was reviewed to determine whether the environment was using roles instead.

The dashboard showed:

- User groups: 0
- Users: 0
- Roles: 26
- Policies: 5

This confirmed that the environment contained IAM roles even though no IAM users were present.

---

## Issue 3

IAM User Groups page displayed no resources.

### Observation

The IAM User Groups page showed:

`IAM user groups (0)`

and:

`No resources to display`

### Cause

No IAM user groups were configured in the lab account.

### Resolution

No remediation was required.

The result was documented as part of the IAM environment assessment.

Because the account also contained zero IAM users, there were no users available to organize into IAM groups.

---

## Issue 4

CloudTrail access appeared to be blocked even though the AWS account contained IAM policies.

### Cause

Having IAM policies in the account does not automatically mean that every identity has permission to perform every AWS API action.

IAM permissions are evaluated against the identity making the request and the specific API action being requested.

In this case, the requested action was:

`cloudtrail:LookupEvents`

The AWS error specifically identified an explicit deny.

### Resolution

The IAM environment was reviewed to identify the active role and understand the permission structure.

The CloudTrail error identified:

`AWSLabsUser-gZ1KBAe5vmFTZBEweyN4`

as the assumed role and:

`RegionPinningPolicy`

as the policy containing the explicit deny.

---

## Issue 5

An IAM policy contained permissions for AWS services but did not provide access to unrelated operations.

### Cause

IAM policies are action-specific.

The reviewed `AccessAnalyzerServiceRolePolicy` contained permissions such as:

- `dynamodb:GetResourcePolicy`
- `dynamodb:ListStreams`
- `dynamodb:ListTables`
- `ec2:DescribeAddresses`
- `ec2:DescribeSnapshots`
- `ec2:DescribeVpcEndpoints`
- `ec2:DescribeVpcs`

These permissions do not automatically provide permission to perform:

`cloudtrail:LookupEvents`

### Resolution

The policy was reviewed through:

`IAM → Policies → AccessAnalyzerServiceRolePolicy`

The Permissions section and JSON policy document were examined to understand which AWS API actions were explicitly allowed.

---

## Issue 6

Unable to continue CloudTrail investigation because of the permission restriction.

### Cause

The active AWS Academy role did not have the required permission to perform the CloudTrail lookup operation.

The error stated:

`not authorized to perform: cloudtrail:LookupEvents`

and identified an:

`explicit deny in an identity-based policy`

### Resolution

The permission failure itself was treated as investigation evidence.

Instead of attempting to bypass the restriction, the IAM configuration was examined to determine:

- Which identity was making the request.
- Which AWS API action was denied.
- Whether the denial was explicit.
- Which policy caused the denial.
- What IAM roles and policies existed in the account.

This allowed the investigation to continue from the IAM side without modifying the restricted lab environment.

---

## Analyst Takeaway

Permission errors should not immediately be treated as technical failures.

During a cloud security investigation, an `AccessDeniedException` can provide valuable evidence about IAM enforcement.

The analyst should identify:

1. The requesting identity.
2. The requested AWS API action.
3. Whether the denial is explicit or implicit.
4. The policy responsible for the decision.
5. Whether the requested action is actually included in the identity's allowed permissions.

In this lab, the CloudTrail access failure provided direct evidence of an explicit IAM deny enforced through `RegionPinningPolicy`.
