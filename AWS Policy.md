# AWS Organizations Policies - Decision Guide

> Quick reference for choosing the right AWS Organizations policy.

| Policy | Primary Purpose | Use When | Applies To | Can Grant Permissions? | Typical Example |
|----------|----------------|-----------|------------|-------------------------|-----------------|
| Service Control Policy (SCP) | Restrict AWS API actions | You want to limit what accounts can do | AWS Account / OU | ❌ No | Deny deleting CloudTrail, deny leaving AWS Organization |
| Resource Control Policy (RCP) | Protect supported AWS resources | You want to prevent resource deletion or modification | Supported AWS Resources | ❌ No | Prevent deleting S3 buckets or EBS snapshots |
| Tag Policy | Standardize tags | You want consistent resource tagging | Tagged Resources | ❌ No | Force CostCenter and Environment tags |
| Backup Policy | Centralize backups | You want automatic backups across accounts | AWS Backup Supported Resources | ❌ No | Daily backup for all production databases |
| Declarative Policy (EC2) | Enforce EC2 configuration | You want standardized EC2 settings | EC2 Resources | ❌ No | Allow only IMDSv2 or approved AMIs |
| Security Hub Policy | Manage Security Hub centrally | You want the same security standards everywhere | Security Hub | ❌ No | Enable CIS benchmarks for every account |
| AI Services Opt-Out Policy | Disable AI service data usage | Organization doesn't allow AI service data collection | AI Services | ❌ No | Opt out of Amazon Rekognition data retention |
| Chatbot Policy | Control AWS Chatbot | Restrict Chatbot configuration | AWS Chatbot | ❌ No | Allow only approved Slack workspaces |

---

# Decision Tree

## Need to restrict AWS service usage?

✅ Use **Service Control Policy (SCP)**

Examples

- Deny EC2
- Deny IAM User creation
- Deny CloudTrail deletion
- Allow only us-east-1 region

---

## Need to protect AWS resources?

✅ Use **Resource Control Policy (RCP)**

Examples

- Prevent S3 bucket deletion
- Prevent EBS snapshot deletion
- Prevent deleting critical resources

---

## Need everyone to use the same tags?

✅ Use **Tag Policy**

Examples

Required tags

```
Environment
Owner
Application
CostCenter
```

---

## Need automatic backups?

✅ Use **Backup Policy**

Examples

- Daily backups
- Weekly backups
- Retention 35 days

---

## Need all EC2 instances configured the same way?

✅ Use **Declarative Policy**

Examples

- IMDSv2 only
- Approved AMIs
- Secure EC2 configuration

---

## Need centralized security standards?

✅ Use **Security Hub Policy**

Examples

- Enable CIS Benchmark
- Enable AWS Foundational Security Best Practices
- Auto-enable new accounts

---

# Quick Comparison

| Requirement | Best Policy |
|-------------|-------------|
| Restrict AWS services | SCP |
| Protect resources | RCP |
| Standardize tags | Tag Policy |
| Centralize backups | Backup Policy |
| Standardize EC2 configuration | Declarative Policy |
| Centralize Security Hub | Security Hub Policy |
| Disable AI service data collection | AI Services Opt-Out Policy |

---

# Real-World Examples

## Example 1

Requirement

Developers must NOT delete CloudTrail.

Policy

✅ SCP

---

## Example 2

Requirement

Nobody should delete Production S3 buckets.

Policy

✅ RCP

---

## Example 3

Requirement

Every EC2 instance must have

- Owner
- CostCenter
- Environment

Policy

✅ Tag Policy

---

## Example 4

Requirement

All production databases backed up every night.

Policy

✅ Backup Policy

---

## Example 5

Requirement

Every EC2 instance must use IMDSv2.

Policy

✅ Declarative Policy

---

## Example 6

Requirement

Enable CIS Security Standard across every AWS account.

Policy

✅ Security Hub Policy

---

# Exam Tips (SAP-C02)

| Question asks... | Answer |
|------------------|--------|
| Limit what an account can do | SCP |
| Protect AWS resources | RCP |
| Enforce tagging | Tag Policy |
| Centralize backups | Backup Policy |
| Standardize EC2 configuration | Declarative Policy |
| Centralize Security Hub | Security Hub Policy |

---

# Easy Memory Trick

```
SCP  -> Stop People
RCP  -> Protect Resources
Tag  -> Standardize Tags
Backup -> Protect Data
Declarative -> Configure EC2
Security Hub -> Security Standards
```

---

---

# Scenario → Choose This Policy (SAP-C02 Quick Reference)

| If the question says... | Choose | Why |
|-------------------------|--------|-----|
| Prevent developers from using specific AWS services | ✅ SCP | Restricts permissions at the account or OU level. |
| Block deleting CloudTrail, Config, or IAM resources | ✅ SCP | Prevents critical AWS service actions. |
| Restrict AWS Regions (for example, allow only us-east-1) | ✅ SCP | Controls which AWS Regions can be used. |
| Prevent users from leaving the AWS Organization | ✅ SCP | Restricts `organizations:LeaveOrganization`. |
| Prevent deletion of production S3 buckets or EBS snapshots | ✅ RCP | Protects supported AWS resources from modification or deletion. |
| Enforce mandatory tags like Environment, Owner, or CostCenter | ✅ Tag Policy | Standardizes tags across the organization. |
| Automatically back up production databases and EBS volumes | ✅ Backup Policy | Centrally manages AWS Backup plans. |
| Ensure all EC2 instances use IMDSv2 or approved settings | ✅ Declarative Policy | Enforces organization-wide EC2 configuration. |
| Enable Security Hub standards for every AWS account | ✅ Security Hub Policy | Centrally manages Security Hub configuration. |
| Disable AI service data retention across the organization | ✅ AI Services Opt-Out Policy | Controls AI service data usage. |
| Manage AWS Chatbot settings centrally | ✅ Chatbot Policy | Standardizes AWS Chatbot configuration. |

---

# SAP-C02 Decision Flow

```
Need to restrict AWS service permissions?
        │
        ├── YES → Service Control Policy (SCP)
        │
        └── NO
             │
             ├── Need to protect AWS resources?
             │      └── YES → Resource Control Policy (RCP)
             │
             ├── Need consistent tags?
             │      └── YES → Tag Policy
             │
             ├── Need centralized backups?
             │      └── YES → Backup Policy
             │
             ├── Need standardized EC2 settings?
             │      └── YES → Declarative Policy
             │
             ├── Need centralized Security Hub?
             │      └── YES → Security Hub Policy
             │
             └── Other organization-wide management settings
                    └── Use the appropriate Management Policy
```

---

# SAP-C02 Keywords Cheat Sheet

| Keyword in the Question | Think |
|--------------------------|-------|
| Restrict, Deny, Prevent service access | ✅ SCP |
| Protect resource, Prevent deletion | ✅ RCP |
| Mandatory tags, Tag compliance | ✅ Tag Policy |
| Backup, Retention, Recovery | ✅ Backup Policy |
| EC2 configuration, IMDSv2, AMI standard | ✅ Declarative Policy |
| Security standards, CIS, Security Hub | ✅ Security Hub Policy |
| Organization-wide governance | ✅ AWS Organizations Policies |

---

# Final Exam Tip

When solving SAP-C02 scenario questions, identify **what AWS is trying to control**:

- **Who can perform an action?** → **SCP**
- **What can happen to a resource?** → **RCP**
- **How resources should be tagged?** → **Tag Policy**
- **How backups should be managed?** → **Backup Policy**
- **How EC2 should be configured?** → **Declarative Policy**
- **How Security Hub should be managed?** → **Security Hub Policy**

> **Remember:** Most SAP-C02 questions are scenario-based. First identify the governance requirement, then map it to the correct AWS Organizations policy.



# Golden Rule

SCP = Controls **who can use which AWS services**.

RCP = Controls **what can happen to supported AWS resources**.

Management Policies = Controls **how AWS services are configured and managed**.