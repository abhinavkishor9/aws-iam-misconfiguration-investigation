# Investigation Notes

## Lab Summary

This investigation focused on reviewing AWS Identity and Access Management (IAM) configuration and identifying permission restrictions within the AWS Academy environment.

The investigation examined the IAM Dashboard, IAM users, IAM user groups, IAM roles, IAM policies, and a permission-denied CloudTrail operation. The investigation also reviewed the permissions defined in the `AccessAnalyzerServiceRolePolicy` policy to understand how IAM policies control access to AWS services and actions.

---

## Analyst Methodology

1. Open the AWS IAM console.
2. Review the IAM Dashboard.
3. Examine IAM users.
4. Examine IAM user groups.
5. Review available IAM roles and policies.
6. Inspect an IAM policy and its permissions.
7. Attempt to review CloudTrail event history.
8. Analyze the resulting `AccessDeniedException`.
9. Identify the policy responsible for the explicit deny.
10. Correlate the IAM configuration with the permission failure.
11. Document the findings.

---

## Investigation Scenario

The AWS Academy environment was investigated to understand how IAM controls access to AWS services and how permission restrictions appear during security investigations.

During the investigation, an attempt to access CloudTrail Event History resulted in an `AccessDeniedException`. The error indicated that the current AWSLabsUser role was explicitly denied permission to perform the `cloudtrail:LookupEvents` action by an identity-based policy named `RegionPinningPolicy`.

The IAM environment was then reviewed to understand the available identities, groups, roles, and policies and to examine how permissions were defined.

---

## Evidence Collected

### Evidence 1 – IAM Dashboard

The IAM Dashboard displayed the resources available in the AWS account.

Observed resources:

- User groups: 0
- Users: 0
- Roles: 26
- Policies: 5
- Identity providers: 0

The dashboard also displayed the AWS account information and the available IAM Policy Simulator.

Finding:

The account contained no IAM users or user groups, while multiple IAM roles and policies were available. This indicates that the AWS Academy environment primarily relied on IAM roles for access.

---

### Evidence 2 – IAM Users

The IAM Users section was examined.

Observed result:

`IAM users (0)`

The page displayed:

`No resources to display`

The available columns included:

- User name
- Path
- Groups
- Last activity
- MFA
- Password age

Finding:

No IAM users were configured in the account.

This is important because IAM users normally represent identities with long-term credentials. The absence of IAM users indicates that the lab environment was using role-based access rather than individually created IAM users.

---

### Evidence 3 – IAM User Groups

The IAM User Groups section was reviewed.

Observed result:

`IAM user groups (0)`

The page displayed:

`No resources to display`

The page also explained that an IAM user group is a collection of IAM users used to specify permissions for multiple users.

Finding:

No IAM user groups were configured.

Because no IAM users were present, there were also no user groups available to centrally manage permissions for those users.

---

### Evidence 4 – IAM Roles

The IAM Dashboard showed:

`Roles: 26`

Finding:

The account contained 26 IAM roles.

The presence of multiple roles was significant because the AWS Academy environment operates using temporary role-based access. The permission-denied event identified the active identity as an assumed AWSLabsUser role.

This demonstrates the difference between IAM users and IAM roles:

- IAM users generally represent long-term identities.
- IAM roles provide temporary permissions that can be assumed by users, services, or other trusted identities.

---

### Evidence 5 – IAM Policies

The IAM Dashboard showed:

`Policies: 5`

The investigation then examined an available IAM policy:

`AccessAnalyzerServiceRolePolicy`

The policy page contained the following sections:

- Permissions
- Entities attached
- Policy versions (23)
- Last Accessed

Finding:

The policy contained explicitly defined permissions controlling which AWS actions could be performed.

---

### Evidence 6 – AccessAnalyzerServiceRolePolicy

The permissions defined in the policy were reviewed.

The policy contained:

```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "AccessAnalyzerServiceRolePolicy",
            "Effect": "Allow",
            "Action": [
                "dynamodb:GetResourcePolicy",
                "dynamodb:ListStreams",
                "dynamodb:ListTables",
                "ec2:DescribeAddresses",
                "ec2:DescribeByoipCidrs",
                "ec2:DescribeSnapshotAttribute",
                "ec2:DescribeSnapshots",
                "ec2:DescribeVpcEndpoints",
                "ec2:DescribeVpcs",
                "ec2:GetSnapshotBlockPublicAccessState"
            ]
        }
    ]
}
