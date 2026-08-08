# Investigation Notes

## Lab Summary

In this investigation , we examined the IAM Dashboard, IAM Users, IAM User Groups, available IAM policies, and the specific `AccessDeniedException` returned for the `cloudtrail:LookupEvents` operation.

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

---

## Investigation Scenario

An AWS identity has been granted more permissions than it actually needs. As a SOC analyst, our task is to identify the excessive permissions, determine what resources or actions they allow, understand the security impact, and recommend a safer permission model.

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

---

### Evidence 3 – IAM User Groups

Collected:

`IAM → User groups`

Finding:

The IAM User Groups page showed:

`IAM user groups (0)`

and:

`No resources to display`


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

---

### Evidence 5 – CloudTrail Access Failure

Collected:

`AccessDeniedException`
 The current identity was not authorized to perform:

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

## MITRE ATT&CK Mapping

| Tactic | Technique | ID |
|---|---|---|
| Privilege Escalation | Valid Accounts | T1078 |
| Defense Evasion | Impair Defenses | T1562 |
| Discovery | Cloud Service Dashboard Discovery | T1526 |

The primary focus of this lab was IAM authorization analysis rather than direct ATT&CK technique execution. The mapping provides contextual relevance for SOC investigations involving cloud identities and access controls.

---

