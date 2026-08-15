# AWS PartnerCast – IAM Security Best Practices
## SAP-C02 Exam Revision README

> **Target exam:** AWS Certified Solutions Architect – Professional (SAP-C02)
>
> **Source topic:** AWS PartnerCast – Security Series Kick-off – IAM Security Best Practices – Technical
>
> **Course duration:** 1h 30m
>
> **Level:** Fundamental

---

## 1. Exam Focus

For SAP-C02, IAM questions commonly test secure access design across:

- Multiple AWS accounts
- AWS Organizations
- Human users
- Applications and workloads
- Cross-account access
- Temporary credentials
- Least privilege
- Centralized identity
- Policy evaluation
- Security guardrails

### Golden principle

**Grant only the permissions required, to the required principal, for the required resource, for the required period of time.**

---

# 2. IAM Users

IAM users represent identities with long-term AWS credentials.

### Best practice

Avoid creating IAM users for applications.

Prefer:

```text
Application
    ↓
IAM Role
    ↓
Temporary Credentials
    ↓
AWS Services
```

For workforce users, prefer:

```text
Human
   ↓
IAM Identity Center
   ↓
Permission Set
   ↓
AWS Account
```

### SAP-C02 clue

If hundreds of employees need access to multiple AWS accounts:

**IAM Identity Center + Permission Sets**

---

# 3. IAM Roles

IAM roles provide temporary credentials.

A role has two important components:

```text
Trust Policy
     ↓
Who can assume the role?

Permissions Policy
     ↓
What can the role do?
```

### Example

```text
EC2
 ↓
IAM Role / Instance Profile
 ↓
Temporary Credentials
 ↓
S3
```

Do not embed long-term AWS access keys in application code.

---

# 4. Trust Policy vs Permissions Policy

## Trust Policy

Answers:

> Who can assume this role?

Example:

```json
{
  "Effect": "Allow",
  "Principal": {
    "Service": "ec2.amazonaws.com"
  },
  "Action": "sts:AssumeRole"
}
```

## Permissions Policy

Answers:

> What can the role do?

Example:

```json
{
  "Effect": "Allow",
  "Action": "s3:GetObject",
  "Resource": "arn:aws:s3:::my-bucket/*"
}
```

### Memorize

```text
Trust Policy       → WHO?
Permissions Policy → WHAT?
```

---

# 5. Least Privilege

Give only the permissions actually required.

### Bad

```json
{
  "Effect": "Allow",
  "Action": "*",
  "Resource": "*"
}
```

### Better

```json
{
  "Effect": "Allow",
  "Action": [
    "s3:GetObject"
  ],
  "Resource": "arn:aws:s3:::company-data/reports/*"
}
```

### SAP-C02

If an application only needs to read objects from one S3 prefix, do not give:

```text
s3:*
```

Give only:

```text
s3:GetObject
```

for the required resource.

---

# 6. Temporary Credentials

Prefer temporary credentials instead of long-term access keys.

Common sources:

- IAM roles
- AWS STS
- IAM Identity Center
- EC2 instance roles
- ECS task roles
- Lambda execution roles
- EKS workload identity mechanisms

Architecture:

```text
Workload
   ↓
IAM Role
   ↓
STS Temporary Credentials
   ↓
AWS API
```

### Benefits

- Credentials expire
- Reduced credential leakage risk
- No permanent application keys
- Better security posture

---

# 7. AWS STS

AWS Security Token Service provides temporary credentials.

Important operations include:

```text
AssumeRole
GetSessionToken
AssumeRoleWithSAML
AssumeRoleWithWebIdentity
```

## Cross-account example

```text
Account A
Developer
   ↓
STS AssumeRole
   ↓
Account B
IAM Role
   ↓
AWS Resources
```

The target role must have an appropriate trust policy.

---

# 8. Cross-Account Access

Example:

```text
Account A = Development
Account B = Production
```

A user or workload in Account A needs access to Account B.

Typical architecture:

```text
Principal in Account A
        ↓
AssumeRole
        ↓
Role in Account B
        ↓
Production Resources
```

### Exam rule

For cross-account access, think:

**IAM Role + STS AssumeRole**

---

# 9. Resource-Based Policies

Resource-based policies are attached to resources.

Examples:

- S3 bucket policies
- SQS queue policies
- SNS topic policies
- KMS key policies
- Lambda resource policies

Concept:

```text
Principal
   ↓
Resource Policy
   ↓
AWS Resource
```

---

# 10. Identity-Based vs Resource-Based Policies

| Policy | Attached To | Main Purpose |
|---|---|---|
| Identity-based policy | User / Group / Role | Define what identity can do |
| Resource-based policy | AWS Resource | Define who can access resource |

### Exam shortcut

```text
Policy attached to identity
        → Identity-based

Policy attached to resource
        → Resource-based
```

---

# 11. IAM Policy Evaluation

Remember:

```text
Explicit Deny
      ↓
    WINS
      ↓
Explicit Allow
      ↓
Default Deny
```

### Golden rule

**An explicit Deny overrides an Allow.**

Example:

```text
IAM Policy → Allow s3:GetObject
SCP        → Deny s3:GetObject

Result → DENY
```

---

# 12. AWS Organizations and SCP

Service Control Policies (SCPs) are organization-level guardrails.

### Critical point

**SCPs do NOT grant permissions.**

They define the maximum available permissions for accounts/OUs.

Conceptually:

```text
SCP
 ↓
Maximum Allowed Permissions
 ↓
IAM Policies
 ↓
Effective Permissions
```

### Example

Organization wants to prevent all member accounts from disabling CloudTrail.

Use:

**SCP**

---

# 13. SCP vs IAM Policy

| Feature | IAM Policy | SCP |
|---|---|---|
| Grants permissions | Yes | No |
| Limits permissions | Yes | Yes |
| Attached to IAM principal | Yes | No |
| Attached to OU/account | No | Yes |
| Organization guardrail | No | Yes |

### Memorize

**SCP = organization/account guardrail**

---

# 14. Permission Boundaries

A permissions boundary defines the maximum permissions an IAM user or role can have.

Concept:

```text
IAM Permissions
       ∩
Permission Boundary
       ↓
Effective Permissions
```

Example:

```text
IAM Policy:
S3 + EC2 + DynamoDB + IAM

Permission Boundary:
S3 + DynamoDB

Effective:
S3 + DynamoDB
```

### Important

A permission boundary does not itself grant permissions.

It limits what the identity's other policies can grant.

---

# 15. SCP vs Permission Boundary

| Requirement | Use |
|---|---|
| Limit permissions across AWS accounts/OUs | SCP |
| Limit permissions of an IAM user/role | Permission Boundary |

### Memorize

```text
Organization → SCP
Principal → Permission Boundary
```

---

# 16. IAM Identity Center

IAM Identity Center is preferred for centralized workforce access.

Architecture:

```text
Corporate Identity Provider
          ↓
IAM Identity Center
          ↓
Permission Sets
          ↓
Multiple AWS Accounts
```

Benefits:

- Centralized access
- SSO
- Temporary credentials
- Centralized permission management
- Multi-account access

### SAP-C02 scenario

If employees need centralized access to many AWS accounts:

**IAM Identity Center**

---

# 17. Federation

Federation allows users to authenticate through an external identity provider.

Examples:

- SAML 2.0
- OIDC

Concept:

```text
Corporate IdP
      ↓
Federation
      ↓
AWS
      ↓
Temporary Credentials
```

Do not create a separate long-term IAM user for every corporate employee when centralized federation is appropriate.

---

# 18. Workload Identity

Applications should use AWS-native identities.

### EC2

```text
EC2 Instance
     ↓
IAM Role / Instance Profile
```

### Lambda

```text
Lambda
   ↓
Execution Role
```

### ECS

```text
ECS Task
   ↓
Task Role
```

### EKS

Use appropriate workload identity mechanisms such as:

- EKS Pod Identity
- IAM roles for service accounts (IRSA)

### Avoid

```text
Source Code
   ↓
Hard-coded Access Key
```

---

# 19. IAM Access Analyzer

IAM Access Analyzer helps identify unintended access.

It can help identify resources accessible outside intended trust boundaries.

Example:

```text
S3 Bucket
   ↓
External AWS Account Access
   ↓
IAM Access Analyzer
   ↓
Finding
```

### SAP-C02 clue

If the requirement is:

> Identify resources that are unintentionally accessible from outside the organization.

Think:

**IAM Access Analyzer**

It can also help validate IAM policies.

---

# 20. MFA

Use MFA for privileged access.

Especially protect:

- Root user
- Administrators
- Highly privileged identities

Policies can require MFA for sensitive operations.

Example condition:

```json
"Condition": {
  "Bool": {
    "aws:MultiFactorAuthPresent": "true"
  }
}
```

---

# 21. Root User Security

The root user has extremely powerful privileges.

### Best practices

- Do not use root for normal administration
- Enable MFA
- Do not create root access keys
- Secure root credentials
- Use IAM or IAM Identity Center for normal administration

### SAP-C02 trap

If root access keys exist and are not needed:

**Delete them.**

---

# 22. S3 Security + IAM

S3 questions frequently combine identity and resource policies.

Possible access models:

```text
IAM Role
   ↓
IAM Policy
   ↓
S3
```

or:

```text
Principal
   ↓
S3 Bucket Policy
   ↓
S3 Bucket
```

Security practices:

- S3 Block Public Access
- Least privilege
- Bucket policies
- IAM roles
- Encryption
- Monitoring
- IAM Access Analyzer

---

# 23. KMS and IAM

KMS access can involve both:

```text
IAM Policy
     +
KMS Key Policy
```

Do not assume that an IAM policy alone is always sufficient for KMS access.

Think:

```text
Principal
   ↓
IAM Permissions
   +
KMS Key Policy
   ↓
KMS Key
```

---

# 24. IAM Conditions

Conditions make permissions more restrictive.

Useful condition keys include:

```text
aws:SourceIp
aws:PrincipalOrgID
aws:MultiFactorAuthPresent
aws:RequestedRegion
aws:SecureTransport
```

### Example

Require HTTPS:

```text
aws:SecureTransport = false
        ↓
Explicit Deny
```

---

# 25. aws:PrincipalOrgID

Useful when access should be restricted to principals belonging to your AWS Organization.

Concept:

```text
Request
   ↓
Principal belongs to organization?
   ↓
YES → Allow
NO  → Deny
```

This can avoid maintaining large lists of individual AWS account IDs in some resource policies.

---

# 26. Defense in Depth

IAM should be part of a layered security strategy.

Example:

```text
AWS Organizations
       ↓
SCP
       ↓
IAM Policies
       ↓
Resource Policies
       ↓
Network Controls
       ↓
Encryption
       ↓
Monitoring
```

Other important AWS security services/concepts:

- AWS CloudTrail
- AWS Config
- Amazon GuardDuty
- AWS Security Hub
- AWS KMS
- VPC endpoints
- Security Groups
- Network ACLs

---

# 27. Credential Security

Avoid:

```text
Hard-coded credentials
```

Prefer:

```text
IAM Role
    ↓
Temporary Credentials
```

If credentials are accidentally exposed:

1. Revoke or rotate the credentials
2. Investigate activity using CloudTrail
3. Identify unauthorized activity
4. Remove unnecessary permissions
5. Review IAM policies
6. Continue monitoring for suspicious activity

---

# 28. Common SAP-C02 Traps

### Trap 1 — Application access keys

**Question:** Give an application an AWS access key.

**Usually wrong.**

Prefer:

**IAM role + temporary credentials**

---

### Trap 2 — AdministratorAccess

**Question:** Application needs multiple AWS services.

**Wrong approach:** Give AdministratorAccess.

**Correct approach:** Least privilege.

---

### Trap 3 — SCP grants permissions

**Wrong.**

SCPs limit maximum permissions.

---

### Trap 4 — Permission boundary grants permissions

**Wrong.**

Permission boundaries limit maximum permissions.

---

### Trap 5 — IAM users in every account

For centralized workforce access, this is usually not the preferred architecture.

Prefer:

**IAM Identity Center**

---

### Trap 6 — IAM Allow always succeeds

Not necessarily.

Check:

- Explicit Deny
- SCP
- Permission Boundary
- Resource Policy
- Session Policy
- KMS Key Policy
- Other applicable authorization controls

---

# 29. SAP-C02 IAM Decision Tree

```text
Who needs access?
        │
        ├── Human
        │     ↓
        │  IAM Identity Center
        │
        └── Workload
              ↓
           IAM Role
              ↓
       Temporary Credentials
              │
              ↓
       Cross-account?
          │          │
         YES         NO
          ↓           ↓
      STS AssumeRole  Continue
          │
          ↓
Organization-wide restriction?
          │
         YES
          ↓
         SCP
          │
          ↓
Need to restrict maximum
permissions of a user/role?
          │
         YES
          ↓
Permission Boundary
          │
          ↓
Resource-level access?
          │
         YES
          ↓
Resource Policy
```

---

# 30. High-Yield Exam Mapping

| Requirement | Think |
|---|---|
| Human access to multiple accounts | IAM Identity Center |
| EC2 → S3 | IAM Role |
| Lambda → DynamoDB | Lambda Execution Role |
| ECS → AWS service | ECS Task Role |
| Cross-account access | STS AssumeRole |
| Organization-wide restriction | SCP |
| Maximum permissions for IAM role | Permission Boundary |
| Temporary credentials | STS |
| External identity federation | IAM Identity Center / Federation |
| Detect unintended external access | IAM Access Analyzer |
| Protect root account | MFA + no root access keys |
| Only organization principals | `aws:PrincipalOrgID` |
| Require HTTPS | `aws:SecureTransport` |
| Minimize permissions | Least Privilege |
| Resource-specific access | Resource Policy |

---

# 31. Must-Memorize Concepts

Before the SAP-C02 exam, be able to explain these without notes:

```text
IAM User
IAM Role
Trust Policy
IAM Policy
Resource-Based Policy
SCP
Permission Boundary
Session Policy
STS
IAM Identity Center
Federation
MFA
IAM Access Analyzer
Principal
Explicit Deny
Least Privilege
Temporary Credentials
aws:PrincipalOrgID
aws:SecureTransport
```

---

# 32. Quick Memory Map

```text
                 AWS IAM SECURITY
                        │
        ┌───────────────┼───────────────┐
        │               │               │
      HUMAN          WORKLOAD        ORGANIZATION
        │               │               │
 IAM Identity        IAM Role           SCP
   Center              │
        │              STS
 Permission             │
   Sets          Temporary Credentials
        │
        └───────────────┬────────────────┘
                        │
                  AUTHORIZATION
                        │
          ┌─────────────┼─────────────┐
          │             │             │
      IAM Policy   Resource Policy   Permission
                                      Boundary
          │             │             │
          └─────────────┼─────────────┘
                        │
                 Explicit Deny
                        │
                       WINS
```

---

# 33. SAP-C02 Golden Rules

1. **Human → IAM Identity Center**
2. **Application → IAM Role**
3. **Cross-account → STS AssumeRole**
4. **Organization/account guardrail → SCP**
5. **Principal maximum permissions → Permission Boundary**
6. **Resource access → Resource-based Policy**
7. **Temporary access → STS**
8. **Never hard-code AWS credentials**
9. **Explicit Deny overrides Allow**
10. **Always prefer least privilege**
11. **Protect root with MFA and avoid root access keys**
12. **Use IAM Access Analyzer to identify unintended external access**
13. **Use conditions to make policies more restrictive**
14. **Use AWS Organizations and SCPs for centralized security guardrails**
15. **Use workload-specific IAM roles instead of long-term credentials**

---

# 34. Exam Practice Checklist

- [ ] I can explain IAM roles.
- [ ] I know the difference between trust and permissions policies.
- [ ] I understand temporary credentials.
- [ ] I understand STS AssumeRole.
- [ ] I can design cross-account access.
- [ ] I know what SCPs do and do not do.
- [ ] I understand permission boundaries.
- [ ] I understand resource-based policies.
- [ ] I know IAM Identity Center.
- [ ] I understand federation.
- [ ] I know workload identity patterns.
- [ ] I understand explicit Deny.
- [ ] I know how IAM policy evaluation works.
- [ ] I understand IAM Access Analyzer.
- [ ] I know root-user security best practices.
- [ ] I understand `aws:PrincipalOrgID`.
- [ ] I understand `aws:SecureTransport`.
- [ ] I can apply least privilege to scenario questions.

---

## Final 30-Second Revision

```text
Human?
→ IAM Identity Center

Application?
→ IAM Role

Cross-account?
→ STS AssumeRole

Organization guardrail?
→ SCP

Limit a role/user's maximum permissions?
→ Permission Boundary

Resource-specific access?
→ Resource Policy

Need temporary credentials?
→ STS

Unintended external access?
→ IAM Access Analyzer

Security?
→ Least Privilege + Explicit Deny + MFA

Never:
→ Hard-code long-term AWS credentials
```

> **SAP-C02 mindset:** Don't memorize IAM services in isolation. Read the scenario, identify **who needs access, what they need to access, where the resource lives, whether access is cross-account, and what security restriction is required**. Then choose the least-privileged AWS architecture.
