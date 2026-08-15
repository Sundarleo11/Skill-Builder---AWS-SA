# AWS Multi-Tenant SaaS + ABAC + IAM
## SAP-C02 Exam Revision README

> **Target exam:** AWS Certified Solutions Architect – Professional (SAP-C02)
>
> **Topic:** Secure and Scalable Multi-Tenant SaaS Solutions using ABAC and IAM
>
> **Focus:** Tenant isolation, data segregation, identity management, ABAC policies, SaaS authorization, monitoring, and compliance

---

# 1. Exam Focus

This topic is highly relevant to SAP-C02 because it combines:

- Multi-tenant SaaS architecture
- AWS IAM
- Attribute-Based Access Control (ABAC)
- Tenant isolation
- Data segregation
- Fine-grained authorization
- Identity attributes
- Resource tags
- Principal tags
- Scalable access control
- Monitoring and compliance

### Core exam mindset

> **Every request must be authorized in the context of the tenant and resource being accessed.**

A scalable SaaS architecture should prevent:

```text
Tenant A
   ↓
Tenant B Data
   ✗ DENY
```

while allowing:

```text
Tenant A
   ↓
Tenant A Data
   ✓ ALLOW
```

---

# 2. What Is Multi-Tenant SaaS?

A multi-tenant SaaS application serves multiple customers (tenants) using shared application infrastructure.

Conceptually:

```text
                    SaaS Application
                          │
          ┌───────────────┼───────────────┐
          │               │               │
       Tenant A        Tenant B        Tenant C
          │               │               │
       Data A           Data B           Data C
```

The infrastructure may be shared, but access to tenant data must remain isolated.

### Main security objective

```text
Tenant A → Tenant A resources ✓
Tenant B → Tenant B resources ✓
Tenant A → Tenant B resources ✗
Tenant B → Tenant A resources ✗
```

---

# 3. Tenant Isolation

Tenant isolation means preventing one tenant from accessing another tenant's resources or data.

Common isolation layers:

```text
Identity Layer
      ↓
Application Layer
      ↓
Authorization Layer
      ↓
Data Layer
      ↓
Storage Layer
```

Do not assume that one security mechanism is enough.

### Defense in depth

```text
IAM / ABAC
   +
Application authorization
   +
Database/data isolation
   +
Encryption
   +
Monitoring
```

---

# 4. Tenant Identity

Every authenticated request should be associated with a tenant.

Conceptually:

```text
User
 ↓
Identity
 ↓
tenantId = tenant-123
 ↓
Authorization
 ↓
Tenant-specific resource
```

Example:

```text
User:
  userId = 1001
  tenantId = tenant-123
  department = finance
```

These attributes can be used to make authorization decisions.

---

# 5. ABAC — Attribute-Based Access Control

ABAC makes authorization decisions using attributes rather than only fixed roles.

Concept:

```text
Principal Attributes
        +
Resource Attributes
        +
Policy Conditions
        ↓
Authorization Decision
```

Example:

```text
Principal:
tenantId = 123

Resource:
tenantId = 123

        ↓

ALLOW
```

If:

```text
Principal:
tenantId = 123

Resource:
tenantId = 456

        ↓

DENY
```

---

# 6. RBAC vs ABAC

## RBAC

Role-Based Access Control:

```text
User
 ↓
Role
 ↓
Permissions
```

Example:

```text
Tenant123Admin
Tenant123User
Tenant456Admin
Tenant456User
```

With thousands of tenants, this can create significant management complexity.

## ABAC

Attribute-Based Access Control:

```text
User
 ↓
Attributes
 ↓
Policy
 ↓
Resource Attributes
```

Example:

```text
tenantId = 123
```

The same policy can potentially be reused for many tenants.

---

# 7. Why ABAC Scales Better

Imagine a SaaS platform with:

```text
10 tenants
```

RBAC may be manageable.

But imagine:

```text
10,000 tenants
```

Creating tenant-specific roles can become difficult:

```text
Tenant001Role
Tenant002Role
Tenant003Role
...
Tenant10000Role
```

ABAC can use tenant attributes instead:

```text
Principal Tag:
tenantId = 123

Resource Tag:
tenantId = 123

        ↓

Policy evaluates the attributes
        ↓
ALLOW
```

### SAP-C02 takeaway

For large-scale, dynamic, multi-tenant authorization:

**Think ABAC.**

---

# 8. IAM Tags and ABAC

AWS IAM supports policy conditions that can use tags and attributes.

Important concepts include:

```text
Principal Tags
Resource Tags
Request Context
Policy Conditions
```

Common condition keys to recognize:

```text
aws:PrincipalTag/<TagKey>
aws:ResourceTag/<TagKey>
```

Example concept:

```text
Principal:
tenantId = 123

Resource:
tenantId = 123

Policy:
PrincipalTag/tenantId == ResourceTag/tenantId

Result:
ALLOW
```

---

# 9. Principal Tags

A principal tag describes an identity.

Example:

```text
tenantId = tenant-123
```

Concept:

```text
User / Role
     ↓
Principal Tag
     ↓
tenantId=123
```

The tag can then be referenced in policy conditions.

### Exam clue

If the question says:

> Access should be based on attributes associated with the user or role.

Think:

**Principal tags / ABAC**

---

# 10. Resource Tags

Resources can also contain attributes using tags.

Example:

```text
S3 object/resource
    ↓
tenantId = tenant-123
```

Concept:

```text
Principal:
tenantId=123

Resource:
tenantId=123

       ↓

MATCH
       ↓

ALLOW
```

If the values do not match:

```text
tenantId=123
       ≠
tenantId=456

       ↓

DENY
```

---

# 11. ABAC Policy Concept

A simplified policy pattern:

```json
{
  "Effect": "Allow",
  "Action": "service:Action",
  "Resource": "*",
  "Condition": {
    "StringEquals": {
      "aws:ResourceTag/tenantId": "${aws:PrincipalTag/tenantId}"
    }
  }
}
```

### Important

This is a conceptual pattern for understanding ABAC.

The exact condition keys and supported resource tagging behavior depend on the AWS service and API operation.

Always verify service-specific IAM support when implementing the architecture.

---

# 12. Tenant-Aware Authorization

A SaaS application should carry tenant context through the request.

Conceptually:

```text
User Login
    ↓
Identity
    ↓
Tenant Context
    ↓
Application Request
    ↓
Authorization
    ↓
Tenant Resource
```

Example:

```text
GET /customers/123/orders
```

The application must verify that:

```text
customer 123
     ↓
belongs to
     ↓
authenticated user's tenant
```

Do not trust a tenant ID supplied by the client without authorization validation.

---

# 13. Application Layer vs IAM Layer

A critical SAP-C02 concept:

**IAM is not a replacement for application-level tenant authorization.**

For example:

```text
IAM
 ↓
Can this principal call the service?
```

Application authorization:

```text
Application
 ↓
Can this user access Tenant A's record #123?
```

A secure architecture can use both:

```text
IAM / ABAC
     +
Application Authorization
     +
Data Isolation
```

---

# 14. Data Segregation Strategies

Multi-tenant SaaS can use different data isolation models.

## Model 1 — Shared database / shared tables

```text
Tenant A ─┐
Tenant B ─┼→ Shared Database
Tenant C ─┘
```

Every record must have tenant context.

Example:

```text
tenant_id
customer_id
order_id
```

Authorization must prevent cross-tenant queries.

### Risk

A missing tenant filter can cause data leakage.

---

## Model 2 — Separate schema per tenant

```text
Database
 ├── Tenant A Schema
 ├── Tenant B Schema
 └── Tenant C Schema
```

Provides stronger logical separation.

---

## Model 3 — Separate database per tenant

```text
Tenant A → DB A
Tenant B → DB B
Tenant C → DB C
```

Provides stronger isolation but increases operational complexity and cost.

---

# 15. Choosing a Tenant Isolation Model

### Shared resources

Advantages:

- Lower cost
- Easier operational scaling
- Higher infrastructure utilization

Risks:

- More complex authorization
- Greater impact if isolation logic fails

### Dedicated resources

Advantages:

- Stronger isolation
- Easier tenant-specific controls

Risks:

- Higher cost
- More infrastructure to manage
- More operational complexity

### SAP-C02 mindset

The correct answer depends on:

- Security requirements
- Compliance
- Cost
- Scale
- Performance
- Operational complexity
- Tenant requirements

---

# 16. Tenant Identity Architecture

A typical SaaS identity flow:

```text
Customer
   ↓
Identity Provider
   ↓
Authentication
   ↓
Tenant Identity
   ↓
Application
   ↓
Authorization
   ↓
AWS Resources
```

The application must maintain a reliable mapping between:

```text
User → Tenant
```

---

# 17. IAM Roles for SaaS Workloads

Applications should use IAM roles instead of hard-coded access keys.

Example:

```text
SaaS Application
      ↓
IAM Role
      ↓
Temporary Credentials
      ↓
AWS Services
```

Avoid:

```text
AWS Access Key
      ↓
Hard-coded in source code
```

---

# 18. Cross-Account SaaS Architecture

A SaaS provider may operate multiple AWS accounts.

Example:

```text
Management Account
       │
       ├── Shared Services
       │
       ├── Application Account
       │
       └── Data Account
```

Cross-account access can use:

```text
IAM Role
   +
STS AssumeRole
```

Example:

```text
Application Account
       ↓
STS AssumeRole
       ↓
Data Account Role
       ↓
Data Resources
```

The target role needs an appropriate trust policy.

---

# 19. AWS Organizations and SCP

For a SaaS organization with multiple AWS accounts, SCPs can provide organization-wide guardrails.

Example:

```text
AWS Organization
       ↓
SCP
       ↓
All Member Accounts
```

SCPs can restrict actions such as:

- Disabling security services
- Leaving approved regions
- Certain destructive operations
- Modifying security controls

### Remember

**SCPs do not grant permissions.**

They define maximum permissions.

---

# 20. Least Privilege

Multi-tenant systems require strict least privilege.

Avoid:

```text
Action: *
Resource: *
```

Prefer:

```text
Specific Action
+
Specific Resource
+
Tenant-specific Conditions
```

Concept:

```text
Who?
 ↓
What?
 ↓
Which tenant?
 ↓
Which resource?
 ↓
Which action?
```

---

# 21. Session Context

Authorization decisions can also depend on request/session context.

Useful context may include:

```text
Principal
Principal tags
Resource tags
Source IP
MFA status
Organization ID
Requested region
Secure transport
```

For SAP-C02, understand that IAM policy evaluation can use context keys to create dynamic controls.

---

# 22. Preventing Tenant ID Manipulation

A common application security problem:

```text
User A
 ↓
Changes tenantId in request
 ↓
Requests Tenant B data
```

Bad design:

```text
GET /data?tenantId=tenant-B
```

and blindly trusting the supplied value.

Better:

```text
Authenticated Identity
        ↓
Trusted Tenant Context
        ↓
Authorization Check
        ↓
Requested Resource
```

The application must validate that the requested resource belongs to the authenticated tenant.

---

# 23. Monitoring Tenant Access

A SaaS platform should monitor:

- Authentication events
- Authorization failures
- Cross-tenant access attempts
- Administrative actions
- Data access
- IAM changes
- Policy changes
- Suspicious activity

Useful AWS services include:

### CloudTrail

Records AWS API activity.

```text
Principal
   ↓
AWS API Call
   ↓
CloudTrail
```

### CloudWatch

Useful for logs, metrics, and alarms.

### GuardDuty

Detects suspicious or malicious activity.

### Security Hub

Centralizes security findings.

---

# 24. Compliance and Auditability

For compliance, maintain visibility into:

```text
WHO
 ↓
Accessed WHAT
 ↓
WHEN
 ↓
FROM WHERE
 ↓
WHAT ACTION
```

CloudTrail is an important source for AWS API activity auditing.

Application-level audit logs may also be necessary for business-level tenant activity.

---

# 25. Encryption and Tenant Data

Tenant isolation should also consider encryption.

Typical architecture:

```text
Application
   ↓
Encrypted Data
   ↓
AWS KMS
```

Depending on requirements, a SaaS platform may use:

- Shared encryption keys
- Separate keys
- Tenant-specific encryption strategies

The correct choice depends on:

- Compliance
- Security requirements
- Number of tenants
- Operational complexity
- Cost

---

# 26. KMS Considerations

KMS authorization can involve:

```text
IAM Policy
     +
KMS Key Policy
```

For cross-account encryption access, carefully evaluate:

- IAM permissions
- Key policy
- Grants where applicable
- Account boundaries
- Encryption/decryption permissions

### Exam warning

Do not assume:

```text
IAM Allow
```

automatically means:

```text
KMS access
```

---

# 27. ABAC vs Separate IAM Policies

## Separate policies

```text
Tenant A
  ↓
Policy A

Tenant B
  ↓
Policy B

Tenant C
  ↓
Policy C
```

Management becomes difficult at very large scale.

## ABAC

```text
Tenant Attribute
      ↓
Reusable Policy
      ↓
Resource Attribute
```

This can reduce policy-management complexity.

---

# 28. When ABAC Is a Good Fit

ABAC is especially useful when:

- Many tenants exist
- Attributes are consistent
- Access rules are dynamic
- Resources are tagged reliably
- Policies can be reused
- Tenant onboarding must scale
- Avoiding large numbers of role/policy combinations is important

---

# 29. When ABAC Needs Caution

ABAC is not automatically the best answer.

Consider additional controls when:

- The service does not support the required condition keys
- Resources cannot be tagged appropriately
- Strong physical/dedicated isolation is required
- Compliance requires dedicated resources
- Authorization logic is primarily application-specific
- Complex data relationships cannot be represented by IAM conditions

### Key point

**ABAC is an authorization mechanism, not a complete tenant-isolation architecture.**

---

# 30. Common SAP-C02 Traps

## Trap 1 — One IAM role per tenant

This may work at small scale but can become difficult to manage for thousands of tenants.

If the scenario emphasizes:

- Large number of tenants
- Dynamic access
- Scalable authorization

Think:

**ABAC**

---

## Trap 2 — Trusting tenantId from the request

Never blindly trust:

```text
tenantId=123
```

from the client.

Validate tenant ownership using trusted identity context.

---

## Trap 3 — IAM alone provides complete SaaS isolation

Not necessarily.

Use defense in depth:

```text
Identity
+
IAM
+
Application Authorization
+
Data Isolation
+
Encryption
+
Monitoring
```

---

## Trap 4 — ABAC automatically works with every AWS resource

Wrong.

Check whether the service and specific resource/API operation support the required tags and condition keys.

---

## Trap 5 — SCP grants tenant permissions

Wrong.

SCPs are organization-level guardrails.

---

## Trap 6 — Hard-coded AWS credentials

Wrong.

Use IAM roles and temporary credentials.

---

# 31. High-Yield SAP-C02 Mapping

| Requirement | Think |
|---|---|
| Thousands of SaaS tenants | ABAC |
| Access based on user attributes | Principal Tags / ABAC |
| Access based on resource attributes | Resource Tags / ABAC |
| Tenant A must not access Tenant B | Tenant-aware authorization |
| Centralized employee access | IAM Identity Center |
| Application access to AWS | IAM Role |
| Cross-account access | STS AssumeRole |
| Organization-wide guardrail | SCP |
| AWS API auditing | CloudTrail |
| Suspicious AWS activity | GuardDuty |
| Central security findings | Security Hub |
| Encrypt tenant data | AWS KMS |
| Minimize permissions | Least Privilege |
| Dynamic reusable authorization | ABAC |
| Identify unintended external access | IAM Access Analyzer |

---

# 32. SAP-C02 Decision Tree

```text
Is this a multi-tenant SaaS system?
              │
             YES
              ↓
Does the scenario require scalable,
dynamic authorization?
              │
             YES
              ↓
             ABAC
              │
              ↓
Are identity attributes available?
              │
             YES
              ↓
      Principal Attributes
              │
              ↓
Are resources tagged with tenant context?
              │
             YES
              ↓
      Resource Attributes
              │
              ↓
       IAM Policy Conditions
              │
              ↓
      Tenant-aware Authorization
              │
              ↓
      Application Data Isolation
              │
              ↓
      Monitoring + Audit
```

---

# 33. Architecture Pattern

A simplified secure SaaS architecture:

```text
                   Users
                     │
                     ↓
             Identity Provider
                     │
                     ↓
               SaaS Application
                     │
          ┌──────────┴──────────┐
          │                     │
     Tenant Context        Authorization
          │                     │
          └──────────┬──────────┘
                     ↓
                    IAM
                     │
                    ABAC
                     │
        ┌────────────┼────────────┐
        ↓            ↓            ↓
     Tenant A     Tenant B     Tenant C
      Data         Data         Data
        │            │            │
        └────────────┼────────────┘
                     ↓
                Audit / Logs
                     │
          ┌──────────┼──────────┐
          ↓          ↓          ↓
      CloudTrail  CloudWatch  Security Hub
                     │
                  GuardDuty
```

---

# 34. Must-Know Terms

Before SAP-C02, understand these terms:

```text
Multi-Tenant SaaS
Tenant Isolation
Data Segregation
ABAC
RBAC
Principal Tag
Resource Tag
IAM Role
Trust Policy
IAM Policy
Resource-Based Policy
SCP
Permission Boundary
STS
IAM Identity Center
Federation
Least Privilege
CloudTrail
GuardDuty
Security Hub
KMS
Tenant Context
Fine-Grained Authorization
```

---

# 35. Quick ABAC Example

### User

```text
PrincipalTag:
tenantId = tenant-001
```

### Resource

```text
ResourceTag:
tenantId = tenant-001
```

### Policy logic

```text
If:

Principal tenantId
       ==
Resource tenantId

Then:
       ALLOW
```

For:

```text
tenant-001 != tenant-002
```

Result:

```text
DENY
```

---

# 36. Final 30-Second Revision

```text
Multi-tenant SaaS
        ↓
Tenant Context
        ↓
ABAC
        ↓
Principal Attributes
        +
Resource Attributes
        ↓
Reusable IAM Policies
        ↓
Tenant Isolation
        ↓
Application + Data Isolation
        ↓
CloudTrail / Monitoring
```

### Remember

**Large number of tenants + dynamic authorization → ABAC**

**User attribute → Principal Tag**

**Resource attribute → Resource Tag**

**Tenant isolation → IAM + application + data-layer controls**

**Application → IAM Role**

**Cross-account → STS AssumeRole**

**Organization guardrail → SCP**

**AWS API audit → CloudTrail**

**Suspicious activity → GuardDuty**

**Encryption → KMS**

---

# 37. SAP-C02 Golden Rules

1. **ABAC is useful for scalable, attribute-driven authorization.**
2. **Use tenant attributes to create reusable authorization rules.**
3. **Do not trust a client-supplied tenant ID without validation.**
4. **Tenant isolation requires defense in depth.**
5. **IAM does not replace application-level authorization.**
6. **Use IAM roles instead of hard-coded access keys.**
7. **Use temporary credentials whenever possible.**
8. **Use SCPs as organization-level guardrails, not permission grants.**
9. **Use CloudTrail for AWS API auditing.**
10. **Use least privilege everywhere.**
11. **Verify that the target AWS service supports the required ABAC condition keys/tags.**
12. **For very large tenant counts, avoid unnecessary tenant-specific role/policy sprawl.**
13. **Consider compliance, cost, and operational complexity when choosing shared vs dedicated tenant resources.**
14. **Use encryption and appropriate KMS controls for sensitive tenant data.**

---

## Final SAP-C02 Mindset

When you see a question about a **large multi-tenant SaaS platform**, ask:

```text
1. Who is the user?
2. Which tenant does the user belong to?
3. What resource is being requested?
4. Does the resource belong to the same tenant?
5. Can ABAC express the authorization rule?
6. Does the AWS service support the required conditions?
7. Is application-level authorization also required?
8. What data-isolation model is appropriate?
9. How will access be audited?
10. What is the least-privileged scalable architecture?
```

> **Key takeaway:** For SAP-C02, don't think of ABAC as simply "IAM tags." Think of it as a **scalable authorization strategy** that uses trusted attributes to enforce fine-grained access while working together with application authorization, tenant-aware data isolation, encryption, and monitoring.
