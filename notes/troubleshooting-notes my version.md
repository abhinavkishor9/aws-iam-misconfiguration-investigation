# Troubleshooting Notes

## Issue 1

Unable to CloudTrail Event History 

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

No remediation needed.

The absence of IAM users was documented as an investigation finding.


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

