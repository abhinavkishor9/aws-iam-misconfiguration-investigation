# Investigation Timeline

| Sequence | Activity | Evidence |
|----------|----------|----------|
| 01 | Opened AWS IAM Dashboard | IAM Dashboard |
| 02 | Reviewed IAM account resources | 0 users, 0 user groups, 26 roles, 5 policies |
| 03 | Opened IAM Users | IAM Users page |
| 04 | Confirmed no IAM users were configured | `IAM users (0)` |
| 05 | Opened IAM User Groups | IAM User Groups page |
| 06 | Confirmed no IAM user groups were configured | `IAM user groups (0)` |
| 07 | Reviewed available IAM policies | IAM Policies |
| 08 | Opened `AccessAnalyzerServiceRolePolicy` | Policy Permissions |
| 09 | Reviewed permissions defined in the policy | DynamoDB and EC2 permissions |
| 10 | Attempted to access CloudTrail Event History | CloudTrail |
| 11 | CloudTrail returned an access-denied error | `AccessDeniedException` |
| 12 | Identified the requested API action | `cloudtrail:LookupEvents` |
| 13 | Identified the requesting identity | `AWSLabsUser` assumed role |
| 14 | Identified the explicit deny | `RegionPinningPolicy` |
| 15 | Correlated CloudTrail failure with IAM authorization | IAM + CloudTrail evidence |
| 16 | Documented investigation findings | Investigation completed |

---

# Investigation Flow

Investigation Started

↓

Opened AWS IAM Dashboard

↓

Reviewed IAM Account Resources

↓

Confirmed 0 IAM Users

↓

Confirmed 0 IAM User Groups

↓

Reviewed IAM Roles and Policies

↓

Opened `AccessAnalyzerServiceRolePolicy`

↓

Reviewed Defined Permissions

↓

Attempted CloudTrail Event History Access

↓

`AccessDeniedException`

↓

Identified `cloudtrail:LookupEvents`

↓

Identified `AWSLabsUser` Assumed Role

↓

Identified Explicit Deny

↓

`RegionPinningPolicy`

↓

Correlated IAM and CloudTrail Evidence

↓

Investigation Completed

---

# Key Evidence Flow

**Request**

`cloudtrail:LookupEvents`

↓

**Requesting Identity**

`AWSLabsUser` assumed role

↓

**Authorization Evaluation**

AWS IAM permission evaluation

↓

**Explicit Deny**

`RegionPinningPolicy`

↓

**Result**

`AccessDeniedException`

---

# Summary

The investigation reconstructed an AWS authorization failure by correlating the CloudTrail access-denied message with the IAM environment. The evidence showed that the `AWSLabsUser` assumed role attempted the `cloudtrail:LookupEvents` operation, which was rejected because of an explicit deny associated with `RegionPinningPolicy`. The investigation also established that the account contained 26 IAM roles but no IAM users or user groups, demonstrating the importance of understanding role-based access and IAM policy enforcement when investigating AWS security events.
