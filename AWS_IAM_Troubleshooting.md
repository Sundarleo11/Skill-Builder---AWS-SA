# AWS IAM Troubleshooting
## SAP-C02 Exam Revision README

> **Target exam:** AWS Certified Solutions Architect – Professional (SAP-C02)
>
> **Course:** AWS Identity and Access Management (IAM) - Troubleshooting
>
> **Course duration:** 1 hour
>
> **Level:** Intermediate
>
> **Focus:** IAM access problems, policy evaluation, troubleshooting methodology, diagnostic information, CloudTrail, Access Analyzer, STS, cross-account access, and effective support-case investigation.

---

# 1. Exam Focus

For SAP-C02, IAM troubleshooting is less about memorizing commands and more about identifying **why an authorization request was denied**.

When an AWS API call fails, systematically determine:

```text
Who is making the request?
        ↓
Which account?
        ↓
Which role/user/session?
        ↓
Which resource?
        ↓
Which action?
        ↓
Which policies apply?
        ↓
Is there an explicit Deny?
        ↓
Are there account/organization boundaries?
        ↓
Is the resource policy involved?
        ↓
What does CloudTrail show?
```

### Golden principle

> **Do not troubleshoot IAM by guessing. Identify the principal, action, resource, applicable policies, and explicit denies.**

---

# 2. IAM Troubleshooting Mental Model

When access is denied:

```text
Request
  ↓
Authentication
  ↓
Authorization
  ↓
Policy Evaluation
  ↓
Resource Access
```

First determine whether the problem is:

### Authentication

```text
Who are you?
```

Examples:

- Invalid credentials
- Expired credentials
- Invalid session token
- Wrong profile
- Wrong role

### Authorization

```text
Are you allowed to perform this action?
```

Examples:

- IAM policy denies action
- SCP denies action
- Permission boundary limits permissions
- Resource policy denies access
- KMS key policy denies access

---

# 3. Authentication vs Authorization

| Problem | Meaning |
|---|---|
| Invalid access key | Authentication |
| Expired temporary credentials | Authentication |
| Invalid session token | Authentication |
| AccessDenied | Usually authorization |
| Explicit policy Deny | Authorization |
| Missing permission | Authorization |

### SAP-C02 shortcut

If the error is:

```text
AccessDenied
```

start investigating **authorization and policy evaluation**.

If the credentials themselves are invalid:

```text
Authentication / credential problem
```

---

# 4. IAM Policy Evaluation

A simplified model:

```text
Explicit Deny
      ↓
    WINS
      ↓
Explicit Allow
      ↓
Default Deny
```

### Most important rule

> **Explicit Deny overrides Allow.**

Example:

```text
IAM Policy:
Allow s3:GetObject

SCP:
Deny s3:GetObject

Result:
DENY
```

---

# 5. What to Check When Access Is Denied

Use this checklist:

```text
1. Confirm the identity
2. Confirm the AWS account
3. Confirm the action
4. Confirm the resource ARN
5. Check identity-based policies
6. Check resource-based policies
7. Check SCPs
8. Check permission boundaries
9. Check session policies
10. Check KMS key policy if encryption is involved
11. Check conditions
12. Check CloudTrail
13. Use IAM Access Analyzer / policy validation tools
```

---

# 6. Confirm the Calling Identity

One of the first troubleshooting steps is to determine which AWS identity is actually making the request.

Conceptually:

```text
AWS Request
     ↓
Calling Principal
     ↓
User / Role / Federated Session
```

Common causes of confusion:

- Wrong AWS CLI profile
- Wrong account
- Wrong assumed role
- Expired session
- Environment credentials overriding profile credentials
- Application using a different role than expected

### Exam mindset

Before changing policies:

**Verify the identity first.**

---

# 7. IAM Roles and Troubleshooting

A role has two major policy areas:

```text
Trust Policy
      ↓
Who can assume the role?

Permissions Policy
      ↓
What can the role do?
```

If `AssumeRole` fails:

```text
Check Trust Policy
```

If role assumption succeeds but an AWS API call fails:

```text
Check Permissions
```

### Important distinction

```text
Cannot assume role
        ↓
Trust relationship problem

Assumed role but action denied
        ↓
Permissions / authorization problem
```

---

# 8. Trust Policy Troubleshooting

Suppose:

```text
Account A
   ↓
AssumeRole
   ↓
Account B Role
```

If the request fails at `AssumeRole`, inspect the target role's trust policy.

Check:

- Principal
- AWS account
- IAM role/user
- Federated principal
- AWS service principal
- Conditions
- External ID where applicable

### Common mistake

Checking only the role's permissions policy when the actual problem is the **trust policy**.

---

# 9. Cross-Account Troubleshooting

Cross-account access can involve multiple authorization layers.

Example:

```text
Account A
Principal
   ↓
STS AssumeRole
   ↓
Account B
Role
   ↓
Resource
```

Check:

```text
1. Can Account A assume the role?
2. Does Account B trust Account A?
3. Does the assumed role have the required permissions?
4. Does the resource policy allow/deny access?
5. Is there an SCP?
6. Is there a permission boundary?
7. Is a KMS key involved?
```

---

# 10. Identity-Based Policies

Policies attached to:

- IAM users
- IAM groups
- IAM roles

Example:

```text
IAM Role
   ↓
Policy
   ↓
Allow s3:GetObject
```

If the principal is denied:

**Check whether the required action and resource are actually allowed.**

---

# 11. Resource-Based Policies

Some AWS resources have their own policies.

Examples:

- S3 bucket
- SQS queue
- SNS topic
- KMS key
- Lambda function

Architecture:

```text
Principal
   ↓
Resource Policy
   ↓
Resource
```

### Troubleshooting

If identity permissions look correct but access still fails:

**Check the resource policy.**

---

# 12. SCP Troubleshooting

Service Control Policies are AWS Organizations guardrails.

Remember:

> **SCPs do not grant permissions.**

They limit maximum permissions.

Example:

```text
IAM Policy:
Allow ec2:RunInstances

SCP:
Deny ec2:RunInstances

Result:
DENY
```

### SAP-C02 troubleshooting clue

If an IAM administrator says:

> "The IAM policy clearly allows it, but the action is still denied."

Check:

**SCP**

---

# 13. Permission Boundary Troubleshooting

A permission boundary limits the maximum permissions of an IAM user or role.

Example:

```text
IAM Policy:
Allow S3 + EC2 + DynamoDB

Permission Boundary:
Allow S3 only

Effective:
S3 only
```

If an IAM policy appears correct but the action remains unavailable:

**Check the permission boundary.**

---

# 14. Session Policies

Temporary sessions can have additional restrictions.

Conceptually:

```text
Role Permissions
        ∩
Session Policy
        ↓
Effective Session Permissions
```

A role may have permission to perform an action, but a session policy can further restrict that session.

### SAP-C02 clue

If a role's policy appears correct but the assumed-role session still cannot perform the action:

**Check session policy / session context.**

---

# 15. Policy Conditions

Conditions are a common source of unexpected AccessDenied errors.

Examples:

```text
aws:SourceIp
aws:PrincipalOrgID
aws:RequestedRegion
aws:SecureTransport
aws:MultiFactorAuthPresent
```

Example:

```text
Allow access only from corporate IP
```

A user outside the allowed IP range may receive:

```text
AccessDenied
```

### Troubleshooting

Check every `Condition` block carefully.

---

# 16. Resource ARN Problems

A policy can contain the correct action but the wrong resource.

Example:

```text
Action:
s3:GetObject

Resource:
arn:aws:s3:::bucket-name
```

But object-level operations typically target objects:

```text
arn:aws:s3:::bucket-name/*
```

### Troubleshooting

Verify:

```text
Action
+
Resource ARN
+
Resource type
```

---

# 17. Wildcards

Be careful with:

```text
Action: *
Resource: *
```

during troubleshooting.

Do not solve every IAM problem by temporarily giving AdministratorAccess.

Instead identify the missing permission.

### SAP-C02 mindset

The best solution is:

**minimum required permission**, not maximum permission.

---

# 18. IAM Policy Simulator

The IAM Policy Simulator can help test policy behavior.

Concept:

```text
Principal
   ↓
Policy Evaluation
   ↓
Action + Resource
   ↓
Allow / Deny
```

Useful for:

- Testing policies
- Identifying missing permissions
- Understanding conditions
- Validating authorization behavior

### Important

Policy simulation is a troubleshooting aid, not a replacement for understanding the complete AWS authorization model.

---

# 19. IAM Access Analyzer

IAM Access Analyzer helps analyze access and policy configurations.

Useful capabilities include:

- Finding unintended external access
- Validating IAM policies
- Analyzing resource access
- Helping identify policy issues

### SAP-C02 clue

If the question asks to identify:

> Resources accessible by external principals

Think:

**IAM Access Analyzer**

---

# 20. CloudTrail for IAM Troubleshooting

AWS CloudTrail is one of the most important troubleshooting tools.

It records AWS API activity.

Concept:

```text
Principal
   ↓
AWS API Request
   ↓
CloudTrail Event
```

CloudTrail can help determine:

- Who made the request
- Which API action was called
- When it happened
- Source IP
- AWS account
- Region
- Error code
- Error message
- Request parameters
- User agent

---

# 21. CloudTrail AccessDenied Investigation

Suppose:

```text
Application
   ↓
S3
   ↓
AccessDenied
```

Use CloudTrail to investigate the API call.

Look for:

```text
eventName
userIdentity
awsRegion
sourceIPAddress
errorCode
errorMessage
requestParameters
```

This can reveal:

```text
WHO?
WHAT?
WHEN?
WHERE?
RESULT?
```

---

# 22. CloudTrail + IAM Troubleshooting Flow

```text
AccessDenied
     ↓
CloudTrail
     ↓
Identify Principal
     ↓
Identify API Action
     ↓
Identify Resource
     ↓
Inspect Policies
     ↓
Check Explicit Deny
     ↓
Check SCP / Boundary / Session
     ↓
Check Conditions
     ↓
Fix Least-Privilege Policy
```

---

# 23. KMS Troubleshooting

KMS introduces additional authorization considerations.

If an encrypted resource operation fails:

```text
Check IAM permissions
        +
Check KMS key policy
        +
Check grants where applicable
        +
Check encryption context / conditions where applicable
```

Example:

```text
S3 GetObject
      ↓
Encrypted with KMS
      ↓
Need KMS permissions
```

A user may have S3 permission but still fail because the KMS authorization is missing.

---

# 24. S3 Troubleshooting

Common causes of S3 AccessDenied:

```text
IAM policy
Bucket policy
SCP
Permission boundary
Object ownership
KMS permissions
Bucket encryption
Conditions
Wrong bucket/object ARN
```

### Troubleshooting sequence

```text
1. Verify bucket
2. Verify object
3. Verify principal
4. Check IAM policy
5. Check bucket policy
6. Check SCP
7. Check permissions boundary
8. Check KMS if SSE-KMS is used
9. Check conditions
10. Check CloudTrail
```

---

# 25. S3 Bucket vs Object ARN

Important distinction:

```text
Bucket:
arn:aws:s3:::my-bucket
```

Object:

```text
arn:aws:s3:::my-bucket/*
```

Different API operations may require different resource types.

### Exam trap

A policy can look correct but still fail because the resource ARN doesn't match the API operation.

---

# 26. EC2 IAM Troubleshooting

If EC2 cannot access an AWS service:

Check:

```text
EC2 Instance Profile
        ↓
IAM Role
        ↓
Permissions Policy
        ↓
Target Resource
```

Common mistakes:

- No instance profile
- Wrong role attached
- Missing permission
- Resource policy denies
- SCP restriction
- Wrong region/resource ARN

Do not place permanent access keys in the EC2 instance when an IAM role is appropriate.

---

# 27. Lambda IAM Troubleshooting

Lambda uses an execution role.

Architecture:

```text
Lambda
  ↓
Execution Role
  ↓
AWS Service
```

If Lambda cannot access DynamoDB/S3/etc.:

Check:

```text
1. Execution role
2. IAM permissions
3. Resource policy
4. SCP
5. Permission boundary
6. KMS permissions
7. Resource ARN
8. Conditions
9. CloudTrail
```

---

# 28. ECS IAM Troubleshooting

Distinguish:

### Task Role

Used by the application running inside the container.

```text
Container Application
       ↓
Task Role
       ↓
AWS API
```

### Task Execution Role

Used by ECS for tasks such as:

- Pulling images
- Sending logs
- Other ECS infrastructure operations

### SAP-C02 trap

Do not confuse:

```text
Task Role
```

with:

```text
Task Execution Role
```

---

# 29. EKS IAM Troubleshooting

EKS workloads may use IAM-based workload identity mechanisms.

Concept:

```text
Pod
 ↓
Workload Identity
 ↓
IAM Role
 ↓
AWS Service
```

If a pod gets AccessDenied:

Check:

- Correct IAM role
- Trust relationship
- Service account / workload identity configuration
- IAM permissions
- Conditions
- Resource policy
- SCP
- KMS permissions if applicable

---

# 30. STS Troubleshooting

For temporary credentials, verify:

```text
Access Key
Secret Key
Session Token
Expiration
Role ARN
Trust Policy
```

A temporary credential set requires the session token where applicable.

### Common failure

Using temporary credentials without the required session token can result in authentication failure.

---

# 31. Expired Credentials

Temporary credentials have an expiration time.

If an application suddenly starts returning authentication errors:

```text
Check credential expiration
```

Potential causes:

- Expired STS session
- Cached credentials
- Incorrect credential refresh
- Wrong system time
- Wrong profile

---

# 32. Clock Skew

AWS request authentication is time-sensitive.

If the client system clock is significantly incorrect, authentication/signature errors can occur.

### Troubleshooting

Check:

```text
System time
NTP synchronization
```

### Exam clue

If authentication/signature errors occur across otherwise valid credentials:

**Check clock synchronization.**

---

# 33. Region Mismatch

Some policies include region restrictions.

Example:

```text
aws:RequestedRegion
```

If the request is made in an unauthorized region:

```text
AccessDenied
```

Also verify that the application is calling the intended region.

---

# 34. MFA Conditions

A policy can require MFA.

Example:

```json
"Condition": {
  "Bool": {
    "aws:MultiFactorAuthPresent": "true"
  }
}
```

If the session does not satisfy the condition:

```text
AccessDenied
```

### Troubleshooting

Check:

```text
Is MFA required?
Was MFA actually used?
Is the request using a session that contains the expected MFA context?
```

---

# 35. Explicit Deny Investigation

When troubleshooting, actively search for explicit denies.

Possible locations:

```text
Identity Policy
Resource Policy
SCP
Permission Boundary
Session Policy
VPC endpoint policy
KMS Key Policy
```

### Golden rule

```text
ALLOW + DENY = DENY
```

---

# 36. VPC Endpoint Policies

AWS service access through VPC endpoints can introduce another policy layer.

Example:

```text
Private Subnet
    ↓
VPC Endpoint
    ↓
S3
```

If the endpoint policy restricts an action/resource, it can affect access.

### SAP-C02 clue

If access works from outside the VPC but fails through a VPC endpoint:

**Investigate the endpoint policy and network path.**

---

# 37. Service-Specific Authorization

Not every AWS service behaves exactly the same way.

When troubleshooting:

```text
Check service-specific authorization behavior
```

Examples:

- S3
- KMS
- SQS
- SNS
- Lambda
- ECR
- Secrets Manager

The exact resource policy, condition keys, and authorization model can vary.

---

# 38. Troubleshooting by Error Type

| Error / Symptom | First Things to Check |
|---|---|
| InvalidClientTokenId | Credentials / session / account |
| SignatureDoesNotMatch | Credentials / request signing / clock |
| ExpiredToken | Temporary credential expiration |
| AccessDenied | IAM authorization layers |
| Cannot AssumeRole | Trust policy |
| S3 AccessDenied | IAM + bucket policy + KMS + SCP |
| KMS AccessDenied | IAM + key policy + conditions |
| Lambda AccessDenied | Execution role + permissions |
| EC2 cannot access AWS service | Instance profile + role |
| ECS application denied | Task role |
| VPC-based access denied | Endpoint policy + IAM |

---

# 39. Troubleshooting Checklist

Use this every time you encounter an IAM authorization issue:

- [ ] Identify the AWS account.
- [ ] Identify the calling principal.
- [ ] Confirm the active IAM role/user/session.
- [ ] Confirm the API action.
- [ ] Confirm the target resource.
- [ ] Verify the resource ARN.
- [ ] Check identity-based policies.
- [ ] Check resource-based policies.
- [ ] Check trust policy if AssumeRole is involved.
- [ ] Check SCPs.
- [ ] Check permission boundaries.
- [ ] Check session policies.
- [ ] Check policy conditions.
- [ ] Check MFA requirements.
- [ ] Check region restrictions.
- [ ] Check VPC endpoint policies if applicable.
- [ ] Check KMS policies for encrypted resources.
- [ ] Check credential expiration.
- [ ] Check system clock for signature issues.
- [ ] Review CloudTrail.
- [ ] Use IAM Access Analyzer / policy validation tools.
- [ ] Apply the least-privilege fix.

---

# 40. SAP-C02 Troubleshooting Decision Tree

```text
AWS request failed
        │
        ↓
Authentication or Authorization?
        │
   ┌────┴────┐
   │         │
Auth        Authz
   │         │
   ↓         ↓
Credentials  Identify Principal
Token        Identify Action
Expiration   Identify Resource
Clock        │
             ↓
        Check IAM Policy
             │
             ↓
        Explicit Deny?
          │       │
         YES      NO
          ↓       ↓
        DENY   Check Resource Policy
                  │
                  ↓
              Check SCP
                  │
                  ↓
          Permission Boundary?
                  │
                  ↓
             Session Policy?
                  │
                  ↓
              Conditions?
                  │
                  ↓
          KMS / Endpoint Policy?
                  │
                  ↓
              CloudTrail
                  │
                  ↓
          Fix Least Privilege
```

---

# 41. High-Yield SAP-C02 Mapping

| Scenario | Think |
|---|---|
| Can't assume IAM role | Trust policy |
| Role assumed but API denied | Permissions |
| IAM allows but still denied | SCP / boundary / resource policy / explicit deny |
| Need identify actual caller | CloudTrail / caller identity |
| Unexpected external resource access | IAM Access Analyzer |
| Temporary credentials fail | Expiration / session token / trust |
| S3 AccessDenied | IAM + bucket policy + KMS + SCP |
| KMS AccessDenied | Key policy + IAM + conditions |
| Lambda cannot access service | Execution role |
| EC2 cannot access service | Instance profile / IAM role |
| ECS application cannot access AWS | Task role |
| Access through VPC endpoint denied | Endpoint policy |
| Region-specific denial | `aws:RequestedRegion` |
| MFA-required operation denied | MFA condition |
| Signature error | Credentials / signing / clock |
| Need audit of AWS API request | CloudTrail |

---

# 42. Common SAP-C02 Exam Traps

## Trap 1 — Add AdministratorAccess

Usually not the correct long-term solution.

Find the exact missing permission.

---

## Trap 2 — IAM policy is Allow, so it must work

Wrong.

Check all applicable authorization layers.

---

## Trap 3 — Trust policy controls what the role can do

Wrong.

Trust policy controls:

**Who can assume the role.**

---

## Trap 4 — SCP grants permissions

Wrong.

SCPs limit permissions.

---

## Trap 5 — Permission boundary grants permissions

Wrong.

It limits maximum permissions.

---

## Trap 6 — AccessDenied means credentials are invalid

Not necessarily.

`AccessDenied` usually points toward authorization.

---

## Trap 7 — Check only IAM policy

Wrong.

Depending on the architecture, also check:

```text
Resource Policy
SCP
Boundary
Session Policy
KMS
VPC Endpoint
Conditions
```

---

## Trap 8 — Ignore CloudTrail

CloudTrail is extremely useful for determining what AWS API call actually occurred and which principal made it.

---

# 43. IAM Troubleshooting Architecture

```text
                    AWS Request
                         │
                         ↓
                  Authentication
                         │
                         ↓
                     Principal
                         │
                         ↓
                Policy Evaluation
                         │
       ┌─────────────────┼─────────────────┐
       │                 │                 │
 IAM Policy       Resource Policy       SCP
       │                 │                 │
       └─────────────────┼─────────────────┘
                         │
              Permission Boundary
                         │
                  Session Policy
                         │
                    Conditions
                         │
              ┌──────────┴──────────┐
              │                     │
             KMS              VPC Endpoint
              │                     │
              └──────────┬──────────┘
                         ↓
                 Allow / Explicit Deny
                         ↓
                    AWS Resource
                         │
                         ↓
                     CloudTrail
```

---

# 44. Support Case Information

If an IAM problem cannot be resolved and requires AWS Support, gather useful diagnostic information.

Useful information can include:

```text
AWS Account ID
Region
Timestamp
API operation
Resource ARN
Calling principal
Request ID
Error code
Error message
CloudTrail event
Relevant policy configuration
Steps to reproduce
```

### Important

Do not expose sensitive credentials in a support case.

Never send:

```text
Secret Access Keys
Passwords
Session credentials
Private keys
```

---

# 45. Practical Troubleshooting Example

### Scenario

An EC2 application receives:

```text
AccessDenied
```

when reading an S3 object.

### Step 1

Identify the EC2 instance profile.

```text
EC2
 ↓
Instance Profile
 ↓
IAM Role
```

### Step 2

Check role permissions.

Does it allow:

```text
s3:GetObject
```

for the correct:

```text
arn:aws:s3:::bucket/*
```

?

### Step 3

Check bucket policy.

```text
Bucket Policy
 ↓
Allow / Deny?
```

### Step 4

Check:

```text
SCP
Permission Boundary
Conditions
```

### Step 5

If the object uses SSE-KMS:

```text
Check KMS permissions
+
KMS key policy
```

### Step 6

Review CloudTrail.

```text
Who?
What API?
Which resource?
When?
What error?
```

### Result

Fix only the missing authorization requirement.

---

# 46. Practical Cross-Account Example

### Scenario

Account A application assumes a role in Account B but receives `AccessDenied`.

### Troubleshoot

```text
Account A
   ↓
STS AssumeRole
   ↓
Account B Role
```

First determine:

```text
Did AssumeRole itself fail?
```

If YES:

```text
Check Account B role trust policy
```

If AssumeRole succeeds:

```text
Check Account B role permissions
```

Then check:

```text
SCP
Permission Boundary
Session Policy
Resource Policy
KMS Policy
Conditions
```

---

# 47. 30-Second Revision

```text
AccessDenied
     ↓
Who?
     ↓
What action?
     ↓
Which resource?
     ↓
IAM policy?
     ↓
Resource policy?
     ↓
Explicit Deny?
     ↓
SCP?
     ↓
Permission Boundary?
     ↓
Session Policy?
     ↓
Conditions?
     ↓
KMS?
     ↓
VPC Endpoint?
     ↓
CloudTrail
     ↓
Least-Privilege Fix
```

---

# 48. Must-Memorize SAP-C02 Rules

1. **Trust policy = who can assume the role.**
2. **Permissions policy = what the role can do.**
3. **Explicit Deny overrides Allow.**
4. **SCPs limit permissions; they do not grant permissions.**
5. **Permission boundaries limit principal permissions.**
6. **Resource policies can affect access.**
7. **Session policies can further restrict temporary sessions.**
8. **AccessDenied usually requires authorization troubleshooting.**
9. **Invalid/expired credentials require authentication troubleshooting.**
10. **CloudTrail is a key source of AWS API diagnostic information.**
11. **IAM Access Analyzer helps identify unintended access and validate policies.**
12. **KMS authorization can require both IAM permissions and key-policy authorization.**
13. **VPC endpoint policies can introduce another authorization layer.**
14. **Verify the actual principal before changing policies.**
15. **Verify the resource ARN and action.**
16. **Check policy conditions carefully.**
17. **Do not solve IAM problems by permanently granting AdministratorAccess.**
18. **Use temporary credentials and IAM roles for workloads.**
19. **For cross-account access, distinguish AssumeRole/trust failures from target-resource permission failures.**
20. **Use least privilege as the final fix.**

---

# 49. Final SAP-C02 Mindset

When an AWS IAM scenario says:

> "The policy allows the action, but the request is still denied."

Do NOT immediately change the IAM policy.

Think:

```text
Explicit Deny?
       ↓
SCP?
       ↓
Permission Boundary?
       ↓
Session Policy?
       ↓
Resource Policy?
       ↓
Condition?
       ↓
KMS?
       ↓
VPC Endpoint?
       ↓
Wrong Principal?
       ↓
Wrong Resource ARN?
       ↓
Wrong Region?
```

### The most important troubleshooting formula

```text
Principal
+
Action
+
Resource
+
Applicable Policies
+
Conditions
+
Context
=
Authorization Decision
```

> **SAP-C02 key takeaway:** IAM troubleshooting is fundamentally about finding the exact authorization layer causing the denial. Verify the caller first, trace the request, inspect every applicable policy boundary, use CloudTrail and IAM analysis tools for evidence, and make the smallest least-privilege change necessary.
