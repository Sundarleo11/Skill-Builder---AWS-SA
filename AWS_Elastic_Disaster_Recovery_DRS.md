# AWS Elastic Disaster Recovery (AWS DRS) – SAP-C02 Exam Revision README

> **Target exam:** AWS Certified Solutions Architect – Professional (SAP-C02)
>
> **Course:** AWS Elastic Disaster Recovery Getting Started
>
> **Duration:** 1 hour
>
> **Level:** Fundamental
>
> **Primary focus:** Disaster recovery, continuous replication, recovery objectives, recovery infrastructure, failover/failback, testing, monitoring, cost optimization, and SAP-C02 architecture scenarios.

---

# 1. Why AWS Elastic Disaster Recovery Matters for SAP-C02

AWS Elastic Disaster Recovery (AWS DRS) is a managed disaster recovery service designed to help recover applications running on:

- On-premises infrastructure
- Physical servers
- Virtual machines
- Other supported cloud environments

The protected source servers continuously replicate data to AWS staging infrastructure.

The basic architecture is:

```text
Source Server
(On-Premises / Cloud)
        |
        | Continuous Replication
        v
AWS DRS Staging Area
        |
        | Recovery / Launch
        v
EC2 Recovery Instances
        |
        v
Application Recovery
```

### SAP-C02 exam clue

If the question describes:

> Protecting on-premises or cloud-based servers by continuously replicating them to AWS and recovering them as EC2 instances during a disaster.

Think:

**AWS Elastic Disaster Recovery (AWS DRS).**

---

# 2. What AWS DRS Solves

AWS DRS is primarily for **application/server disaster recovery**.

It can help minimize:

- Downtime
- Data loss
- Recovery complexity
- Disaster recovery infrastructure cost

Instead of maintaining a complete duplicate production environment all the time, DRS uses a staging environment and launches recovery resources when needed.

---

# 3. Core Architecture

Understand these components.

```text
                 SOURCE ENVIRONMENT
              ┌─────────────────────┐
              │ Application Server   │
              │ Database Server     │
              │ File Server         │
              └──────────┬──────────┘
                         │
                    DRS Agent
                         │
                  Continuous
                   Replication
                         │
                         v
             ┌────────────────────────┐
             │ AWS DRS Staging Area   │
             │ Replication Resources  │
             └───────────┬────────────┘
                         │
                   Recovery Launch
                         │
                         v
             ┌────────────────────────┐
             │ EC2 Recovery Instances │
             │ Application Recovery   │
             └────────────────────────┘
```

---

# 4. Source Server

The source server is the system being protected.

Examples:

- Physical server
- VMware VM
- Hyper-V VM
- On-premises application server
- Supported cloud-based server

The DRS agent runs on the source server and sends changed data to AWS.

---

# 5. AWS DRS Agent

The agent enables replication from the source environment to AWS.

Concept:

```text
Source Server
      |
      v
DRS Agent
      |
      v
Replication
      |
      v
AWS
```

The agent captures changes and transfers them to the AWS staging environment.

---

# 6. Continuous Data Replication

A key concept.

AWS DRS continuously replicates source-server data to AWS.

This allows recovery from a recent point in time rather than relying only on periodic backups.

```text
Source
  |
  | Change
  v
DRS Agent
  |
  v
AWS Staging
```

### Important

DRS is primarily a **continuous replication and recovery service**, not simply a backup service.

---

# 7. Staging Area

AWS DRS maintains a staging environment in AWS.

The staging environment is designed to receive replicated data and support recovery.

The goal is to avoid maintaining fully active production-sized recovery infrastructure at all times.

Concept:

```text
Normal Operation

Source
  |
  v
Staging Area
  |
  v
Recovery resources not fully active
```

During disaster:

```text
Staging
  |
  v
Launch Recovery Instances
  |
  v
Application Available
```

---

# 8. Why Staging Reduces Cost

Traditional DR can involve:

```text
Production Environment
        +
Full Standby Environment
```

This can be expensive.

DRS can use a smaller staging footprint and launch larger recovery resources when required.

```text
Normal State:
Low-cost staging

Disaster:
Launch required recovery capacity
```

### SAP-C02 clue

If the requirement says:

> Minimize disaster recovery cost while maintaining a recoverable copy of servers.

Consider:

**AWS Elastic Disaster Recovery.**

---

# 9. Recovery Point Objective (RPO)

RPO answers:

> How much data loss can the business tolerate?

Example:

```text
RPO = 15 minutes
```

The organization can tolerate losing approximately 15 minutes of data, depending on implementation and actual replication state.

### DRS

Continuous replication is designed to provide very low RPOs compared with traditional periodic backup approaches.

---

# 10. Recovery Time Objective (RTO)

RTO answers:

> How quickly must the application become operational after a disaster?

Example:

```text
RTO = 1 hour
```

The organization expects the application to be recovered within approximately one hour.

---

# 11. RPO vs RTO

Memorize:

```text
RPO = Data loss
RTO = Downtime
```

Or:

```text
RPO → "How much data can I lose?"

RTO → "How long can I be down?"
```

---

# 12. Disaster Recovery Strategy Comparison

Common DR strategies:

| Strategy | Typical Cost | Recovery Speed | Standby Environment |
|---|---:|---:|---|
| Backup and Restore | Lowest | Slowest | No |
| Pilot Light | Low/Medium | Faster | Minimal |
| Warm Standby | Medium/High | Fast | Partially running |
| Active/Active | Highest | Fastest | Fully running |

AWS DRS is particularly useful when organizations need server-level recovery without maintaining a full active duplicate environment.

---

# 13. DRS vs Backup

### Backup

```text
Data
 ↓
Backup
 ↓
Storage
 ↓
Restore
```

### DRS

```text
Running Server
 ↓
Continuous Replication
 ↓
AWS Staging
 ↓
Launch Recovery
```

### Exam distinction

If the requirement is:

> Recover files or database data from backup history.

Think backup services.

If the requirement is:

> Recover complete servers/applications rapidly in AWS.

Think **AWS DRS**.

---

# 14. DRS vs AWS Backup

AWS Backup is designed for centralized backup management across supported AWS resources.

AWS DRS is focused on disaster recovery of servers/applications through continuous replication and recovery.

### Simple memory aid

```text
AWS Backup
→ Backup management

AWS DRS
→ Server/application disaster recovery
```

---

# 15. DRS vs AWS Application Migration Service

AWS Application Migration Service (MGN) is primarily used to migrate servers into AWS.

AWS DRS is designed for disaster recovery.

```text
MGN → Migration
DRS → Disaster Recovery
```

### Exam trap

Do not select a migration service simply because source servers are outside AWS.

The business requirement matters.

---

# 16. DRS Recovery Instances

During recovery, DRS can launch EC2-based recovery instances in AWS.

Concept:

```text
Source Server
     ↓
Replicated Data
     ↓
AWS DRS
     ↓
EC2 Recovery Instance
     ↓
Application
```

The recovery environment can be configured to match application requirements.

---

# 17. Recovery Launch Settings

Recovery settings determine how the source server is recovered in AWS.

Important considerations include:

- Instance type
- Subnet
- Security groups
- VPC
- Private/public networking
- EBS configuration
- IP configuration
- Application dependencies

---

# 18. Recovery Instance Right-Sizing

Do not blindly choose the largest EC2 instance.

Choose an instance type based on:

- CPU
- Memory
- Network requirements
- Application workload
- Storage
- Performance requirements

### SAP-C02 principle

Use appropriate recovery capacity rather than unnecessarily expensive infrastructure.

---

# 19. Recovery Network Design

A recovered application needs appropriate network connectivity.

Consider:

```text
VPC
 |
 +-- Subnets
 |
 +-- Route Tables
 |
 +-- Security Groups
 |
 +-- Network ACLs
 |
 +-- DNS
 |
 +-- Load Balancer
 |
 +-- Application Dependencies
```

---

# 20. Multi-AZ Considerations

Disaster recovery and high availability are different.

### Multi-AZ

Protects against:

- Availability Zone failure
- Local infrastructure failure

### DRS

Protects against:

- Major server/application disruption
- On-premises infrastructure failure
- Regional/cloud environment failure scenarios depending on architecture

### Important

Do not confuse:

```text
HA → Keep service running

DR → Recover service after major disruption
```

---

# 21. Regional Disaster Recovery

DRS can replicate source servers into an AWS Region selected for recovery.

For major disasters, consider:

- Geographic separation
- Independent infrastructure
- DNS failover
- Network connectivity
- Security controls
- Data residency requirements

---

# 22. Failover

Failover means moving the workload to the recovery environment.

Concept:

```text
Production Failure
       ↓
Identify Recovery Point
       ↓
Launch Recovery Instances
       ↓
Application Validation
       ↓
Redirect Users
```

---

# 23. Failback

Failback means returning operations to the original production environment after it is restored.

Concept:

```text
Production Failure
       ↓
Failover to AWS
       ↓
Operate in AWS
       ↓
Repair Original Environment
       ↓
Reverse Replication / Recovery
       ↓
Failback
```

---

# 24. Failover vs Failback

```text
Failover
Production → Recovery Environment

Failback
Recovery Environment → Original Production
```

Memorize this distinction.

---

# 25. Recovery Point Selection

A disaster may require recovery from a specific point in time.

Reasons include:

- Corrupted data
- Accidental deletion
- Application issue
- Ransomware
- Logical corruption

A recovery solution should consider whether the latest replicated state is safe to use.

---

# 26. Point-in-Time Recovery

Point-in-time recovery allows an organization to choose an appropriate recovery point rather than blindly using the most recent state.

Example:

```text
10:00 → Good
10:15 → Good
10:30 → Corruption
10:45 → Corruption

Recovery
   ↓
10:15
```

This is particularly important when the latest replicated state contains corrupted data.

---

# 27. Recovery Testing

A DR plan is not complete until it is tested.

Test:

- Replication
- Recovery launch
- Application startup
- Dependencies
- Networking
- DNS
- Security
- Data integrity
- Performance
- RTO
- RPO

---

# 28. Non-Disruptive DR Testing

A good DR architecture should allow recovery testing without unnecessarily disrupting production.

Testing validates:

```text
Can we actually recover?
```

rather than merely assuming replication means recovery will work.

---

# 29. DR Runbook

Create a documented process:

```text
1. Detect disaster
2. Assess impact
3. Select recovery point
4. Launch recovery instances
5. Validate application
6. Validate dependencies
7. Update DNS/routing
8. Notify users
9. Monitor
10. Begin restoration
11. Fail back
12. Resume normal replication
```

---

# 30. Monitoring

Monitor DRS continuously.

Important signals:

- Replication status
- Replication lag
- Source server health
- Recovery readiness
- Errors
- Recovery events
- Network connectivity

---

# 31. Replication Lag

Replication lag indicates how far the AWS copy is behind the source.

Concept:

```text
Source Time = 12:00
AWS Replicated Time = 11:59:50

Lag ≈ 10 seconds
```

High lag may indicate:

- Network problems
- Source load
- Replication issues
- AWS-side constraints
- Configuration problems

---

# 32. Network Requirements

Replication depends on network connectivity between source environments and AWS.

Consider:

- Internet connectivity
- VPN
- AWS Direct Connect
- Firewall rules
- Proxy requirements
- DNS
- Security policies
- Bandwidth

---

# 33. Bandwidth Planning

Large workloads generate significant replication traffic.

Factors:

```text
Disk write rate
×
Number of servers
×
Replication frequency/continuous change rate
```

A workload with high write volume needs appropriate network capacity.

---

# 34. Storage Considerations

Replicated data ultimately needs storage in AWS.

Consider:

- EBS
- Staging resources
- Recovery instance storage
- Data growth
- IOPS
- Throughput
- Snapshot/storage lifecycle

---

# 35. Security

A DR environment must be secured just like production.

Consider:

- IAM
- Least privilege
- Security groups
- Encryption
- KMS
- Network segmentation
- Logging
- Monitoring
- Secrets management

---

# 36. IAM Least Privilege

Use only the permissions required for:

- DRS administration
- Replication
- Recovery
- Monitoring
- Automation

Avoid:

```text
AdministratorAccess
```

unless genuinely required.

---

# 37. Encryption

Protect replicated and recovered data.

Consider:

```text
Source
 ↓
Encrypted transfer
 ↓
AWS storage
 ↓
Encrypted recovery volumes
```

Use AWS KMS where encryption key management is required.

---

# 38. Security Groups

Recovery instances should have security groups appropriate for the recovered application.

Example:

```text
Internet
  ↓
ALB
  ↓
Web
  ↓
Application
  ↓
Database
```

Do not expose recovered servers directly to the internet unless required.

---

# 39. DNS During Disaster

DNS is often critical to redirect users to the recovery environment.

Possible architecture:

```text
Users
  ↓
Route 53
  ↓
Health Check / Routing
  ↓
Primary
   OR
Recovery
```

For automatic or controlled traffic switching, select the Route 53 routing strategy that matches the business requirement.

---

# 40. Dependency Recovery

Recovering one server is not enough if the application depends on:

- Database
- DNS
- Active Directory
- File systems
- Authentication
- External APIs
- Message queues
- License servers
- Network services

Create a dependency map.

```text
Web
 ↓
Application
 ↓
Database
 ↓
External Dependencies
```

---

# 41. Recovery Order

Dependencies should be recovered in the correct order.

Example:

```text
Network
 ↓
Identity / DNS
 ↓
Database
 ↓
Application
 ↓
Web
```

The exact order depends on the architecture.

---

# 42. Application Consistency

Server recovery does not automatically guarantee application-level consistency.

Consider:

- Databases
- Transactions
- Distributed applications
- Queues
- Shared files
- External dependencies

Test the recovered application end-to-end.

---

# 43. Automation

Automation reduces human error during disasters.

Possible tools:

- AWS DRS
- AWS Systems Manager
- AWS Lambda
- Step Functions
- CloudFormation
- Terraform
- Route 53
- EventBridge

Example:

```text
DR Event
   ↓
Automation
   ↓
Launch Recovery
   ↓
Configure
   ↓
Validate
   ↓
Update Routing
```

---

# 44. Cost Optimization

DR cost can include:

- Replication infrastructure
- Storage
- EBS
- Network transfer
- Recovery instances
- Testing
- Monitoring
- Additional AWS services

A key DRS benefit is avoiding the cost of keeping a full production-sized standby environment running continuously.

---

# 45. Pilot Light vs DRS

Pilot light:

```text
Minimal critical infrastructure
running continuously
```

DRS:

```text
Continuous server replication
+
Staging environment
+
Recovery launch
```

The appropriate solution depends on workload, RTO/RPO, architecture, and operational requirements.

---

# 46. Backup + DRS

They can complement each other.

```text
DRS
→ Fast server/application recovery

Backup
→ Long-term retention
→ Historical recovery
→ Compliance
```

For strong resilience, an organization may use both.

---

# 47. Ransomware Considerations

DRS alone should not be treated as the only protection against ransomware.

Consider:

- Immutable backups
- Separate recovery copies
- Least privilege
- MFA
- Monitoring
- Malware detection
- Recovery-point selection
- Isolation
- Regular testing

If replicated data is corrupted before detection, the latest recovery state may also be affected.

---

# 48. DRS and Business Continuity

Disaster recovery is part of a larger business continuity strategy.

Consider:

```text
Technology Recovery
+
People
+
Processes
+
Communication
+
Facilities
+
Third-party dependencies
```

---

# 49. SAP-C02 Scenario Mapping

| Scenario | Likely Direction |
|---|---|
| Recover on-premises servers in AWS | AWS DRS |
| Continuous server replication | AWS DRS |
| Recover application as EC2 instances | AWS DRS |
| Minimize DR infrastructure cost | DRS staging/recovery model |
| Migrate servers permanently to AWS | AWS Application Migration Service |
| Centralized AWS backup management | AWS Backup |
| Long-term backup retention | Backup solution |
| Low RPO server DR | DRS |
| Recover after source-server failure | DRS |
| Redirect users after recovery | Route 53 / routing architecture |
| Protect DR environment | IAM, KMS, security groups, logging |
| Validate recovery capability | DR testing |
| Return to original environment | Failback |
| Recover from earlier good state | Appropriate recovery point / backup strategy |
| Major regional disaster | Multi-Region DR architecture |
| Reduce operational recovery effort | Automation |

---

# 50. Common SAP-C02 Exam Traps

## Trap 1 — DRS is just a backup service

Wrong.

DRS focuses on continuous replication and rapid server/application recovery.

---

## Trap 2 — DRS is only for on-premises servers

Wrong.

It can protect supported servers running in other environments as well.

---

## Trap 3 — Replication automatically means the application is recoverable

Wrong.

You must validate:

- Networking
- Dependencies
- Application startup
- DNS
- Security
- Data consistency
- Recovery procedures

---

## Trap 4 — Multi-AZ equals disaster recovery

Wrong.

Multi-AZ primarily provides high availability within a Region.

DR protects against larger disruptions.

---

## Trap 5 — Failover and failback are the same

Wrong.

```text
Failover → Move to recovery environment

Failback → Return to original environment
```

---

## Trap 6 — Latest recovery point is always correct

Wrong.

If corruption occurred, you may need an earlier recovery point.

---

## Trap 7 — Full standby environment is always required

Wrong.

DRS uses a staging/recovery model that can reduce ongoing infrastructure cost.

---

## Trap 8 — DR is complete once replication is enabled

Wrong.

Regular DR testing is essential.

---

# 51. High-Yield Comparison

| Service / Concept | Primary Purpose |
|---|---|
| AWS Elastic Disaster Recovery | Server/application disaster recovery |
| AWS Backup | Centralized backup management |
| AWS Application Migration Service | Server migration to AWS |
| Multi-AZ | High availability |
| Multi-Region | Geographic resilience / DR |
| Route 53 | DNS and traffic routing |
| AWS KMS | Encryption key management |
| Systems Manager | Operational management/automation |

---

# 52. RTO/RPO Strategy

```text
Business Requirement
        ↓
Determine RPO
        ↓
Determine RTO
        ↓
Choose DR strategy
        ↓
Design replication
        ↓
Design recovery
        ↓
Test
        ↓
Measure actual RTO/RPO
```

---

# 53. DR Design Checklist

```text
□ Identify critical applications
□ Identify dependencies
□ Define RPO
□ Define RTO
□ Install/configure DRS
□ Verify replication
□ Monitor replication lag
□ Design AWS recovery VPC
□ Configure security groups
□ Configure IAM
□ Configure encryption
□ Define recovery order
□ Configure DNS/routing
□ Document runbook
□ Test recovery
□ Measure RTO/RPO
□ Test failback
□ Review costs
□ Repeat testing regularly
```

---

# 54. 30-Second Revision

```text
AWS DRS
    ↓
Continuous server replication
    ↓
AWS staging area
    ↓
Recovery instances
    ↓
Application recovery
```

Remember:

```text
RPO → How much data can I lose?
RTO → How much downtime can I tolerate?

DRS → Disaster Recovery
MGN → Migration
Backup → Backup/retention
Multi-AZ → High Availability
Multi-Region → Geographic resilience
```

---

# 55. SAP-C02 Golden Rules

1. **Use AWS DRS when the requirement is server/application disaster recovery through continuous replication.**
2. **Do not confuse DRS with AWS Backup.**
3. **Do not confuse DRS with AWS Application Migration Service.**
4. **RPO measures acceptable data loss.**
5. **RTO measures acceptable downtime.**
6. **Continuous replication helps achieve low RPO.**
7. **Recovery instances are launched in AWS when needed.**
8. **A staging/recovery architecture can reduce the cost of maintaining a full standby environment.**
9. **Failover means moving to the recovery environment.**
10. **Failback means returning to the original environment.**
11. **Always test recovery instead of assuming replication guarantees recovery.**
12. **Recover application dependencies in the correct order.**
13. **Secure the recovery environment with least-privilege IAM, encryption, network controls, and monitoring.**
14. **Use Route 53 or another appropriate routing mechanism when traffic must be redirected after recovery.**
15. **For ransomware or logical corruption, consider recovery-point selection and immutable backups in addition to DRS.**
16. **Multi-AZ is primarily HA; DR addresses larger failure scenarios.**
17. **Define RTO/RPO before selecting the DR architecture.**
18. **Optimize DR for both business requirements and cost.**
19. **Automate recovery procedures where possible.**
20. **A DR plan is only reliable when it is regularly tested and measured.**

---

# 56. Final SAP-C02 Mental Model

When you see a disaster recovery scenario:

```text
What failed?
     ↓
Server?
Application?
AZ?
Region?
Entire on-premises environment?
     ↓
What RPO is required?
     ↓
What RTO is required?
     ↓
Need continuous server replication?
     ↓
AWS DRS
     ↓
Where will recovery run?
     ↓
VPC + EC2 + Storage + Security
     ↓
How will users reach it?
     ↓
DNS / Route 53 / Routing
     ↓
How will dependencies recover?
     ↓
Test
     ↓
Failover
     ↓
Operate
     ↓
Failback
```

## Ultimate DRS formula

```text
AWS DRS
=
Continuous Replication
+
AWS Staging
+
Recovery Instances
+
Low RPO
+
Rapid Recovery
+
Cost Optimization
+
Testing
+
Failover / Failback
```

> **SAP-C02 takeaway:** When an enterprise needs to recover existing servers and applications from on-premises or another supported environment into AWS with continuous replication and minimal downtime/data loss, AWS Elastic Disaster Recovery is a strong candidate. Always validate the required **RTO/RPO, dependencies, recovery networking, security, testing, failover/failback, and cost** before selecting the final architecture.
