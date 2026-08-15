# AWS Control Tower – Enterprise-Scale Governance
## SAP-C02 Exam Revision README

> **Target exam:** AWS Certified Solutions Architect – Professional (SAP-C02)
>
> **Course:** Tailoring Your Cloud Foundation: Customizing Control Tower for Your Business Needs
>
> **Course duration:** 2 hours
>
> **Level:** Advanced
>
> **Focus:** AWS Control Tower, AWS Organizations, Landing Zones, Account Factory, AFT, CfCT, guardrails, multi-account governance, automation, customization, scalability, and troubleshooting.

---

# 1. Exam Focus

This topic is highly relevant to SAP-C02 because enterprise AWS architectures commonly require:

- Multiple AWS accounts
- Centralized governance
- Organizational Units (OUs)
- Account provisioning
- Security guardrails
- Centralized logging
- Compliance controls
- Automated account customization
- Terraform-based provisioning
- Governance at large scale
- Standardized account baselines

The key exam question is often:

> **How do you centrally govern and automate hundreds of AWS accounts while allowing individual teams to operate independently?**

Think:

```text
AWS Organizations
        +
AWS Control Tower
        +
Guardrails / Controls
        +
Account Factory
        +
AFT / CfCT
        +
Centralized Security & Logging
```

---

# 2. AWS Control Tower

AWS Control Tower helps establish and govern a multi-account AWS environment.

Concept:

```text
                    AWS Control Tower
                           │
              ┌────────────┼────────────┐
              │            │            │
          Governance    Account      Controls
                        Provisioning
              │            │            │
              └────────────┼────────────┘
                           ↓
                  AWS Organizations
                           ↓
                    Multiple Accounts
```

Control Tower helps automate:

- Landing zone setup
- Account provisioning
- Governance
- Guardrails / controls
- Compliance monitoring
- Account baselines

---

# 3. Why Multi-Account Architecture?

A single AWS account is often insufficient for large enterprises.

Multiple accounts provide:

- Security isolation
- Billing separation
- Team isolation
- Environment separation
- Compliance boundaries
- Reduced blast radius
- Easier governance
- Independent service quotas

Example:

```text
AWS Organization
      │
      ├── Security OU
      │     ├── Log Archive
      │     └── Audit
      │
      ├── Infrastructure OU
      │
      ├── Production OU
      │     ├── App Account
      │     └── Data Account
      │
      └── NonProduction OU
            ├── Dev Account
            └── Test Account
```

---

# 4. AWS Organizations

AWS Organizations provides centralized management of multiple AWS accounts.

Key concepts:

```text
Organization
    ↓
Root
    ↓
Organizational Units
    ↓
AWS Accounts
```

Organizations provides capabilities such as:

- Account management
- OUs
- SCPs
- Consolidated billing
- Centralized governance

---

# 5. Organizational Units (OUs)

OUs logically group accounts.

Example:

```text
Root
 │
 ├── Security OU
 │
 ├── Infrastructure OU
 │
 ├── Production OU
 │
 └── Development OU
```

Accounts inherit applicable organizational controls based on their position in the hierarchy.

### SAP-C02 clue

If different groups of accounts require different governance policies:

**Use OUs.**

---

# 6. AWS Control Tower Landing Zone

A landing zone is a well-architected, governed multi-account AWS environment.

It establishes foundational capabilities such as:

- Account structure
- Identity
- Governance
- Logging
- Security
- Compliance

Concept:

```text
Landing Zone
      ↓
AWS Organization
      ↓
OUs
      ↓
Governed Accounts
```

---

# 7. Control Tower Controls

Controls establish governance rules for managed accounts/OUs.

They can help enforce or detect requirements such as:

- Preventing unwanted configurations
- Detecting noncompliant resources
- Restricting risky operations
- Maintaining governance standards

### Important

Control Tower controls are implemented using AWS services and governance mechanisms; understand their behavior rather than treating every control as identical.

---

# 8. Preventive vs Detective Controls

This is an important exam concept.

## Preventive

Attempts to prevent a prohibited action.

Concept:

```text
User attempts action
        ↓
Control
        ↓
BLOCK
```

Commonly associated with:

```text
SCP
```

## Detective

Allows activity but detects/report noncompliance.

Concept:

```text
Configuration changes
        ↓
Detection
        ↓
Finding / Alert
```

Commonly implemented using mechanisms such as:

```text
AWS Config
```

### Memorize

```text
Preventive → Stop
Detective  → Detect
```

---

# 9. SCP and Control Tower

Service Control Policies are organization-level guardrails.

Important:

> **SCPs do not grant permissions.**

They define maximum available permissions.

Example:

```text
IAM Policy
Allow ec2:RunInstances

SCP
Deny ec2:RunInstances

Result
DENY
```

### SAP-C02 clue

If an enterprise wants to prevent an action across many accounts:

**SCP / preventive governance control**

---

# 10. Account Factory

Account Factory provides a standardized way to provision new AWS accounts in a governed environment.

Concept:

```text
New Account Request
        ↓
Account Factory
        ↓
Standardized Account
        ↓
OU Placement
        ↓
Governance
```

Benefits:

- Standard account configuration
- Consistent governance
- Repeatable provisioning
- Reduced manual setup
- Faster onboarding

---

# 11. Account Factory for Terraform (AFT)

AFT extends account provisioning using Terraform-based automation.

Concept:

```text
Terraform
    ↓
AFT
    ↓
Account Provisioning
    ↓
AWS Control Tower
    ↓
Governed Account
```

AFT is useful when an organization already uses Terraform and wants account provisioning integrated with Infrastructure as Code.

---

# 12. Why AFT?

Without automation:

```text
New Account
   ↓
Manual Setup
   ↓
Manual Security Configuration
   ↓
Manual Logging
   ↓
Manual Tags
   ↓
Manual Baseline
```

At 500+ accounts, this becomes difficult.

AFT enables:

```text
Account Request
      ↓
Terraform Automation
      ↓
Standardized Account
      ↓
Baseline
      ↓
Governance
```

### SAP-C02 clue

If the scenario emphasizes:

- Terraform
- Infrastructure as Code
- Automated account provisioning
- Large number of accounts

Think:

**AFT**

---

# 13. AFT Components – Conceptual View

AFT generally follows a Git/Terraform-driven model.

Conceptually:

```text
Account Requests
      ↓
AFT Repository
      ↓
Terraform
      ↓
Account Provisioning
      ↓
Customization
      ↓
Governed AWS Account
```

AFT separates account provisioning from account-specific customization.

---

# 14. AFT Account Customization

AFT can be used to apply standardized configurations to accounts.

Examples:

- IAM configuration
- Tags
- Networking configuration
- Security baseline
- Logging configuration
- AWS service configuration
- Account-specific settings

Concept:

```text
New Account
    ↓
AFT
    ↓
Baseline
    ↓
Account Customization
```

---

# 15. Customizations for Control Tower (CfCT)

CfCT provides a mechanism to customize governed accounts and OUs using infrastructure/configuration templates.

Concept:

```text
Control Tower
      ↓
Governed Accounts
      ↓
CfCT
      ↓
Custom Resources / Configurations
```

Use CfCT when the requirement is to apply customizations across accounts/OUs managed by Control Tower.

---

# 16. AFT vs CfCT

This distinction is important.

| AFT | CfCT |
|---|---|
| Terraform-based | Control Tower customization framework |
| Strong fit for Terraform workflows | Strong fit for Control Tower customizations |
| Account provisioning + customization | Deploy custom resources/configuration to governed accounts/OUs |
| Git-driven automation | Template/configuration-driven customization |
| Useful for account factory workflows | Useful for extending Control Tower governance |

### Exam shortcut

```text
Terraform-based account provisioning
        → AFT

Control Tower account/OU customization
        → CfCT
```

---

# 17. Account Lifecycle

A scalable account lifecycle can look like:

```text
Request
   ↓
Approval
   ↓
Account Factory / AFT
   ↓
OU Placement
   ↓
Baseline
   ↓
Security Controls
   ↓
Logging
   ↓
Application Team
   ↓
Ongoing Governance
```

At enterprise scale, automation reduces configuration drift and operational overhead.

---

# 18. Account Vending

Account vending means creating new AWS accounts in a standardized, repeatable manner.

Typical requirements:

- Naming
- OU placement
- Tags
- Networking
- Logging
- Security
- IAM
- Budgets
- Guardrails

Concept:

```text
Account Request
       ↓
Automated Account Vending
       ↓
Governed Account
```

---

# 19. Centralized Logging

Enterprise environments should centralize logs.

Concept:

```text
Account A ─┐
Account B ─┤
Account C ─┼──→ Central Log Archive
Account D ─┘
```

Benefits:

- Security investigation
- Compliance
- Auditing
- Incident response
- Reduced tampering risk

Common AWS services include:

- CloudTrail
- AWS Config
- CloudWatch
- S3
- Security Hub

---

# 20. Log Archive Account

A dedicated log archive account can centralize security and audit logs.

Concept:

```text
Production ─┐
Development ┤
Security ───┼──→ Log Archive Account
Audit ──────┘
```

Advantages:

- Centralized retention
- Reduced tampering risk
- Easier compliance
- Easier investigation

### SAP-C02 clue

If the requirement says:

> Centralize logs from all AWS accounts into a dedicated account.

Think:

**Centralized logging / Log Archive account**

---

# 21. Audit / Security Account

A separate security or audit account can provide centralized visibility.

Example:

```text
Security Account
      │
      ├── Security Hub
      ├── GuardDuty
      ├── Config
      └── Audit tooling
```

This reduces the risk of security monitoring being controlled by application teams.

---

# 22. GuardDuty in Multi-Account Environments

Amazon GuardDuty can be centrally managed across an organization.

Concept:

```text
Member Accounts
      ↓
GuardDuty
      ↓
Delegated Administrator
      ↓
Security Account
```

Benefits:

- Centralized threat detection
- Organization-wide visibility
- Consistent security monitoring

---

# 23. Security Hub in Multi-Account Environments

AWS Security Hub can centralize security findings.

Concept:

```text
AWS Accounts
     ↓
Security Findings
     ↓
Security Hub
     ↓
Central Security Account
```

Useful for:

- Security posture
- Findings aggregation
- Compliance visibility
- Centralized operations

---

# 24. Control Tower at 500+ Accounts

At large scale, focus on:

- Automation
- Standardization
- OU design
- Control placement
- Account lifecycle
- Avoiding manual changes
- Terraform pipelines
- Centralized monitoring
- Drift management
- Service quotas
- Operational scalability

### Golden principle

> **Do not solve an enterprise-scale governance problem with manual account-by-account configuration.**

---

# 25. OU Design

A good OU hierarchy reflects governance requirements.

Example:

```text
Root
│
├── Security
│
├── Infrastructure
│
├── Workloads
│    ├── Production
│    ├── Development
│    └── Testing
│
└── Sandbox
```

Avoid creating OUs purely based on organizational politics if governance requirements can be expressed more effectively by lifecycle/security needs.

---

# 26. SCP Inheritance

SCPs can be attached at different levels.

Concept:

```text
Root
 ↓
OU
 ↓
Account
```

Applicable policies combine to restrict permissions.

Remember:

```text
More restrictive SCP
        ↓
Less available permissions
```

### Exam clue

If a child account cannot perform an action even though its IAM policy allows it:

Check the **parent OU/root SCP hierarchy**.

---

# 27. Control Placement

Controls should be applied at the appropriate organizational scope.

Example:

```text
All Production Accounts
        ↓
Production OU
        ↓
Production-specific controls
```

Rather than manually applying the same governance to every account.

### Scale principle

```text
OU-level governance
       >
Account-by-account manual governance
```

---

# 28. Customization Strategy

A scalable enterprise should separate:

```text
Baseline
   +
Organization-wide Controls
   +
OU-specific Controls
   +
Account-specific Customization
```

Example:

```text
All Accounts
   ↓
Security Baseline

Production OU
   ↓
Production Controls

Specific Account
   ↓
Application-specific Configuration
```

---

# 29. Drift Management

Configuration drift occurs when an account differs from the approved baseline.

Example:

```text
Approved Baseline
       ↓
Account Configuration
       ↓
Manual Change
       ↓
DRIFT
```

Risks:

- Security gaps
- Compliance failures
- Unexpected behavior
- Difficult troubleshooting

Automation helps reduce drift.

---

# 30. Infrastructure as Code

For large environments:

```text
Git
 ↓
Code Review
 ↓
CI/CD
 ↓
Terraform
 ↓
AWS
```

Benefits:

- Repeatability
- Version control
- Reviewability
- Auditing
- Automation
- Consistency

### SAP-C02 clue

If a scenario asks for repeatable, auditable, scalable account configuration:

**Infrastructure as Code**

---

# 31. GitOps-Style Governance

A common enterprise pattern:

```text
Git Repository
      ↓
Pull Request
      ↓
Review
      ↓
CI/CD
      ↓
Terraform / Configuration
      ↓
AWS Accounts
```

Benefits:

- Controlled changes
- Traceability
- Automated deployment
- Rollback/version history

---

# 32. Control Tower Customization Architecture

Conceptual flow:

```text
Git Repository
      │
      ↓
Customization Definitions
      │
      ↓
Control Tower
      │
      ├── OU A
      ├── OU B
      └── OU C
           │
           ↓
     Governed Accounts
```

Customization can establish:

- Security configurations
- Logging
- IAM resources
- Service configurations
- Networking
- Operational tooling

---

# 33. AFT Architecture – Conceptual

```text
Account Request
       ↓
AFT
       ↓
Terraform
       ↓
AWS Account
       ↓
Account Customization
       ↓
Governance
```

AFT is especially useful when Terraform is the organization's preferred IaC tool.

---

# 34. AFT vs Manual Account Factory

### Manual

```text
Request
 ↓
Human setup
 ↓
Human configuration
 ↓
Human validation
```

### AFT

```text
Request
 ↓
Git / Terraform
 ↓
Automation
 ↓
Standardized account
```

AFT provides better repeatability at enterprise scale.

---

# 35. CfCT vs Manual Customization

### Manual

```text
Account 1 → Configure
Account 2 → Configure
Account 3 → Configure
...
Account 500 → Configure
```

### CfCT

```text
Customization Definition
          ↓
Control Tower
          ↓
Multiple Accounts / OUs
```

The goal is consistent, repeatable customization.

---

# 36. Troubleshooting Account Provisioning

If account provisioning fails:

Check:

```text
1. Account request
2. Account Factory / AFT configuration
3. AWS Organizations
4. OU placement
5. IAM permissions
6. Service quotas
7. Terraform state/configuration
8. Control Tower events
9. CloudTrail
10. Customization deployment
```

---

# 37. Troubleshooting AFT

Potential problem areas:

```text
Git repository
   ↓
Terraform configuration
   ↓
Pipeline
   ↓
AFT workflow
   ↓
AWS Organizations
   ↓
Control Tower
```

Investigate:

- Terraform errors
- Invalid account metadata
- Incorrect OU
- Invalid parameters
- Pipeline failures
- IAM permissions
- Service quotas
- Account provisioning status

---

# 38. Troubleshooting CfCT

If customization fails:

Check:

```text
Customization definition
       ↓
Template/configuration
       ↓
Target OU/account
       ↓
Required IAM permissions
       ↓
AWS service availability
       ↓
Deployment status
       ↓
CloudFormation / CloudTrail logs
```

### Exam mindset

Do not immediately recreate the entire landing zone.

Identify the failed customization component first.

---

# 39. Service Quotas at Scale

At 500+ accounts, quotas become an architectural consideration.

Examples can include:

- API rate limits
- AWS Organizations quotas
- Service-specific limits
- CloudFormation limits
- Concurrent operations
- Account creation rates

### SAP-C02 clue

If an architecture works at 20 accounts but fails at hundreds:

Think:

**Service quotas + API throttling + automation scalability.**

---

# 40. Performance and Scale

Enterprise-scale governance should minimize:

- Sequential operations
- Manual workflows
- Repeated API calls
- Account-by-account configuration
- Unnecessary deployments

Prefer:

```text
Parallelizable automation
+
OU-level governance
+
Reusable modules
+
Centralized controls
+
Event-driven workflows
```

---

# 41. Centralized vs Distributed Governance

## Centralized

```text
Security Account
      ↓
Central Governance
      ↓
Member Accounts
```

Benefits:

- Consistency
- Central visibility
- Easier compliance

## Distributed

Teams manage their own controls.

Benefits:

- Flexibility
- Team autonomy

### Best enterprise model

Usually a balance:

```text
Central Guardrails
        +
Team Autonomy
```

---

# 42. Control Tower vs AWS Organizations

| AWS Organizations | AWS Control Tower |
|---|---|
| Account management | Managed landing zone/governance |
| OUs | OUs |
| SCPs | Controls/guardrails |
| Consolidated billing | Account provisioning workflows |
| Organization hierarchy | Governance automation |
| Foundational service | Higher-level governance framework |

### Memorize

**Organizations = foundation**

**Control Tower = governed multi-account operating model**

---

# 43. Control Tower vs AFT vs CfCT

| Technology | Main Purpose |
|---|---|
| AWS Organizations | Manage accounts/OUs and organization policies |
| Control Tower | Landing zone + multi-account governance |
| Account Factory | Standard account provisioning |
| AFT | Terraform-based account provisioning/customization |
| CfCT | Customize Control Tower-managed accounts/OUs |

### Quick memory

```text
Organizations
     ↓
Control Tower
     ↓
Account Factory
     ↓
AFT / CfCT
```

---

# 44. SAP-C02 Scenario Mapping

| Scenario | Think |
|---|---|
| Centralized multi-account governance | Control Tower |
| Create standardized AWS accounts | Account Factory |
| Terraform-based account provisioning | AFT |
| Customize Control Tower accounts/OUs | CfCT |
| Group accounts by governance | OUs |
| Organization-wide permission restriction | SCP |
| Detect configuration drift/noncompliance | AWS Config / detective controls |
| Centralize AWS API logs | CloudTrail + Log Archive |
| Central security findings | Security Hub |
| Threat detection across accounts | GuardDuty |
| Enterprise IaC | Terraform / CloudFormation |
| 500+ accounts | Automation + quotas + OU-level governance |

---

# 45. Common SAP-C02 Exam Traps

## Trap 1 — Manually configure every account

At hundreds of accounts, this is usually not scalable.

Prefer:

**Automation + Control Tower + AFT/CfCT**

---

## Trap 2 — SCP grants permissions

Wrong.

SCPs limit permissions.

---

## Trap 3 — Control Tower replaces Organizations

Wrong.

Control Tower builds on AWS Organizations and other AWS services.

---

## Trap 4 — AFT and CfCT are the same

Wrong.

Think:

```text
AFT → Terraform-centric account provisioning/customization

CfCT → Control Tower customization
```

---

## Trap 5 — Detective controls prevent the action

Wrong.

Detective controls identify noncompliance after it occurs.

---

## Trap 6 — Put every account in one OU

Not always appropriate.

OUs should reflect governance and policy requirements.

---

## Trap 7 — Manual security baselines

At enterprise scale, manual baselines create drift and operational risk.

Automate them.

---

## Trap 8 — One giant centralized policy for everything

Over-centralization can reduce team autonomy and create operational bottlenecks.

Use:

```text
Central guardrails
+
Delegated ownership
+
OU-specific governance
```

---

# 46. SAP-C02 Decision Tree

```text
Need multi-account governance?
             │
            YES
             ↓
       AWS Organizations
             │
             ↓
      Need landing zone?
             │
            YES
             ↓
        Control Tower
             │
             ↓
     Need account provisioning?
             │
            YES
             ↓
       Account Factory
             │
             ↓
       Using Terraform?
          │         │
         YES        NO
          ↓          ↓
         AFT      Account Factory
          │
          ↓
Need Control Tower customizations?
          │
         YES
          ↓
        CfCT
          │
          ↓
Need organization-wide restrictions?
          │
         YES
          ↓
         SCP
          │
          ↓
Need centralized security visibility?
          │
         YES
          ↓
Security Account + CloudTrail +
GuardDuty + Security Hub
```

---

# 47. Enterprise Reference Architecture

```text
                         AWS Organization
                                │
                         AWS Control Tower
                                │
             ┌──────────────────┼──────────────────┐
             │                  │                  │
        Security OU       Infrastructure OU    Workloads OU
             │                  │                  │
       ┌─────┴─────┐            │          ┌───────┼────────┐
       │           │            │          │       │        │
   Log Archive   Audit      Shared       Prod    Dev      Test
                               Services
                                  │
                                  ↓
                         Governance Controls
                                  │
                       ┌──────────┴──────────┐
                       │                     │
                      AFT                   CfCT
                       │                     │
                Terraform Accounts     Control Tower
                                      Customizations
```

---

# 48. Enterprise Account Lifecycle

```text
Business Request
       ↓
Approval
       ↓
Account Factory / AFT
       ↓
AWS Account
       ↓
OU Placement
       ↓
Baseline
       ↓
Security Controls
       ↓
Logging
       ↓
Customizations
       ↓
Application Team
       ↓
Continuous Governance
       ↓
Drift Detection / Remediation
```

---

# 49. Governance Layers

Think in layers:

```text
Layer 1
AWS Organizations
        ↓
Layer 2
OUs
        ↓
Layer 3
SCPs / Controls
        ↓
Layer 4
Account Factory / AFT
        ↓
Layer 5
CfCT / Account Customizations
        ↓
Layer 6
Security / Logging / Monitoring
```

---

# 50. Must-Memorize Concepts

Before SAP-C02, know these clearly:

```text
AWS Organizations
AWS Control Tower
Landing Zone
Organizational Units
SCP
Controls / Guardrails
Preventive Control
Detective Control
Account Factory
Account Vending
AFT
CfCT
Multi-Account Governance
Centralized Logging
Log Archive Account
Security Account
Delegated Administration
AWS Config
CloudTrail
GuardDuty
Security Hub
Infrastructure as Code
Terraform
Configuration Drift
Service Quotas
```

---

# 51. 30-Second Revision

```text
Organizations
   ↓
Accounts + OUs
   ↓
Control Tower
   ↓
Landing Zone
   ↓
Controls / Guardrails
   ↓
Account Factory
   ↓
AFT → Terraform
   ↓
CfCT → Control Tower Customization
   ↓
Central Logging + Security
   ↓
Continuous Governance
```

### Remember

**Organizations → Accounts/OUs**

**Control Tower → Governed landing zone**

**Account Factory → Standard account provisioning**

**AFT → Terraform-based account automation**

**CfCT → Control Tower customizations**

**SCP → Maximum permission guardrail**

**Preventive → Stop**

**Detective → Detect**

**500+ accounts → Automate, standardize, monitor quotas**

---

# 52. SAP-C02 Golden Rules

1. **Use AWS Organizations as the foundation for multi-account management.**
2. **Use Control Tower when a governed multi-account landing zone is required.**
3. **Use OUs to group accounts according to governance requirements.**
4. **Use SCPs as permission guardrails; SCPs do not grant permissions.**
5. **Use Account Factory for standardized account provisioning.**
6. **Use AFT when Terraform-based account provisioning/customization is required.**
7. **Use CfCT for customizing Control Tower-managed accounts/OUs.**
8. **Automate account baselines instead of configuring hundreds of accounts manually.**
9. **Centralize security and audit logs.**
10. **Use separate security/log archive accounts where appropriate.**
11. **Use GuardDuty for threat detection and Security Hub for centralized security findings.**
12. **Use CloudTrail for AWS API auditing.**
13. **Use detective controls to identify noncompliance.**
14. **Use preventive controls to prevent prohibited actions.**
15. **Design OUs around governance requirements rather than simply organizational charts.**
16. **At 500+ accounts, consider service quotas, API throttling, parallelism, and automation pipelines.**
17. **Use Infrastructure as Code for repeatability, version control, and auditability.**
18. **Minimize configuration drift through centralized automation.**
19. **Balance centralized guardrails with delegated team autonomy.**
20. **Do not manually solve a problem that needs to work across hundreds of accounts.**

---

# 53. Final SAP-C02 Mindset

When you see:

> "The company has hundreds of AWS accounts and needs centralized governance, standardized provisioning, security controls, and automated customization."

Think:

```text
AWS Organizations
        ↓
OUs
        ↓
Control Tower
        ↓
Landing Zone
        ↓
Account Factory
        ↓
AFT / CfCT
        ↓
SCPs + Controls
        ↓
Central Security + Logging
        ↓
Continuous Governance
```

The key SAP-C02 decision is not simply:

> **"Which AWS service?"**

Instead ask:

```text
How many accounts?
       ↓
What governance is required?
       ↓
Where should the control apply?
       ↓
How will accounts be provisioned?
       ↓
How will customizations be automated?
       ↓
How will security be centralized?
       ↓
How will drift and failures be detected?
       ↓
How will the architecture scale?
```

> **Key takeaway:** At enterprise scale, AWS Control Tower provides the governance framework, AWS Organizations provides the multi-account foundation, Account Factory/AFT provide scalable account provisioning, and CfCT provides Control Tower customizations. SAP-C02 questions often test how these pieces work together rather than asking about them in isolation.
