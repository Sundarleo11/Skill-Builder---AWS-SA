# AWS Elastic Disaster Recovery – A Technical Introduction
## SAP-C02 Exam Revision README

> **Target exam:** AWS Certified Solutions Architect – Professional (SAP-C02)
>
> **Course:** AWS Elastic Disaster Recovery – A Technical Introduction
>
> **Duration:** 1 hour 30 minutes
>
> **Level:** Intermediate
>
> **Focus:** AWS DRS architecture, source-server replication, staging areas, launch settings, recovery readiness, failover, failback, recovery testing, monitoring, networking, security, RTO/RPO, and enterprise DR design.

---

# 1. SAP-C02 Exam Focus

This course goes deeper into **how AWS Elastic Disaster Recovery (AWS DRS) is implemented and operated**.

For SAP-C02, understand the complete lifecycle:

```text
Source Server
      ↓
Install / configure DRS
      ↓
Continuous replication
      ↓
AWS staging area
      ↓
Monitor replication
      ↓
Test recovery
      ↓
Disaster occurs
      ↓
Fail over to AWS
      ↓
Run application in AWS
      ↓
Source environment restored
      ↓
Fail back to source
```

The exam is likely to test **architecture decisions and operational behavior**, not just the definition of DRS.

---

# 2. What AWS Elastic Disaster Recovery Does

AWS Elastic Disaster Recovery is a managed disaster recovery service for supported servers running outside AWS or in supported cloud environments.

It continuously replicates source-server data to AWS and allows the server to be recovered on AWS infrastructure.

Core model:

```text
Source Environment
       |
       | Continuous Replication
       v
AWS DRS Staging Area
       |
       | Recovery
       v
EC2 Recovery Environment
```

---

# 3. The DRS Lifecycle

Memorize this lifecycle:

```text
1. Prepare
      ↓
2. Install DRS agent
      ↓
3. Replicate
      ↓
4. Monitor
      ↓
5. Test recovery
      ↓
6. Disaster
      ↓
7. Failover
      ↓
8. Operate in AWS
      ↓
9. Restore source
      ↓
10. Reverse replication / synchronize
      ↓
11. Failback
```

---

# 4. Source Server

The source server is the production server being protected.

It can represent workloads such as:

- Application servers
- Database servers
- File servers
- Web servers
- Business-critical enterprise systems

The DRS agent is installed on supported source servers.

---

# 5. DRS Agent

The DRS agent is responsible for enabling replication from the source server to AWS.

Concept:

```text
Source Server
      ↓
DRS Agent
      ↓
Replication
      ↓
AWS
```

The agent captures changes and sends data to the AWS staging environment.

---

# 6. Continuous Replication

The central DRS concept is continuous replication.

```text
Source
  ↓
Changed data
  ↓
DRS Agent
  ↓
AWS staging
```

This provides a recent recovery state without relying only on scheduled backup jobs.

### SAP-C02 clue

If the scenario says:

> Continuously replicate servers to AWS and recover them quickly after a disaster.

Think:

**AWS Elastic Disaster Recovery.**

---

# 7. Staging Area

The staging area receives replicated data and maintains the resources necessary to support recovery.

Normal state:

```text
Production
    ↓
Continuous Replication
    ↓
AWS Staging
```

Disaster state:

```text
AWS Staging
    ↓
Launch Recovery Instances
    ↓
Application Recovery
```

The staging architecture helps reduce the cost of maintaining a fully running duplicate production environment.

---

# 8. Staging Area vs Recovery Environment

Do not confuse them.

### Staging

```text
Replication destination
+
Recovery preparation
```

### Recovery environment

```text
EC2 instances
+
Application
+
Required networking
+
Required dependencies
```

The recovery environment is launched/configured when recovery is required.

---

# 9. Recovery Instance

During recovery, DRS launches EC2-based recovery instances.

Example:

```text
On-Premises App Server
       ↓
Continuous Replication
       ↓
AWS DRS
       ↓
EC2 Recovery Instance
       ↓
Application
```

The recovery instance should be configured to meet the workload's:

- CPU
- Memory
- Storage
- Network
- OS
- Application requirements

---

# 10. Launch Settings

Recovery launch settings define how recovered servers should be launched.

Typical considerations include:

- AWS Region
- VPC
- Subnet
- Security group
- Instance type
- EBS configuration
- IP addressing
- Network interfaces
- Other recovery configuration

### SAP-C02 principle

A recovery design is incomplete if it only considers data replication but not the target infrastructure.

---

# 11. Recovery Templates / Configuration

Use standardized recovery configuration for predictable launches.

The goal is:

```text
Source Server
      ↓
Known Recovery Configuration
      ↓
Predictable EC2 Recovery
```

This reduces manual intervention during a disaster.

---

# 12. Recovery Readiness

A critical operational concept.

A business should know whether its servers are actually ready for recovery.

Monitor:

- Replication health
- Replication lag
- Source-server connectivity
- Recovery configuration
- Errors
- Recovery status

### Golden rule

> **Replication running ≠ recovery plan validated.**

---

# 13. RPO

Recovery Point Objective answers:

> How much data loss is acceptable?

Example:

```text
RPO = 5 minutes
```

The business may tolerate approximately five minutes of data loss depending on actual replication and recovery state.

---

# 14. RTO

Recovery Time Objective answers:

> How long can the application be unavailable?

Example:

```text
RTO = 30 minutes
```

The recovery process should be designed to meet this business requirement.

---

# 15. RPO vs RTO

Memorize:

```text
RPO → Data Loss

RTO → Downtime
```

Easy trick:

```text
P = Point
→ Recovery Point
→ Data loss

T = Time
→ Recovery Time
→ Downtime
```

---

# 16. Failover

Failover means switching application operations from the source environment to AWS.

Typical process:

```text
Disaster
   ↓
Choose appropriate recovery point
   ↓
Launch recovery instances
   ↓
Validate applications
   ↓
Redirect traffic
   ↓
Operate in AWS
```

---

# 17. Failover Is Not Automatically DNS Switching

Failover can require multiple actions.

For example:

```text
DRS Recovery
+
Application Validation
+
Database Validation
+
DNS / Routing Change
+
User Notification
```

The exact process depends on the architecture.

---

# 18. Recovery Point Selection During Failover

The newest replicated state may not always be the correct recovery point.

Example:

```text
10:00 → Healthy
10:10 → Healthy
10:20 → Data corruption
10:30 → Replicated corruption
```

Recovering from 10:10 may be safer than recovering from 10:30.

### SAP-C02 clue

If the scenario includes:

> Corruption occurred before the disaster was detected.

Think about **recovery point selection**, not blindly using the newest state.

---

# 19. Failback

After the original environment is repaired, applications may need to return to the source environment.

Concept:

```text
Production
   ↓
Disaster
   ↓
Failover
   ↓
AWS Recovery
   ↓
Source Repaired
   ↓
Reverse / synchronize data
   ↓
Failback
   ↓
Original Production
```

---

# 20. Failover vs Failback

```text
FAILOVER
Source → AWS

FAILBACK
AWS → Source
```

This is one of the most important DRS concepts for SAP-C02.

---

# 21. Reverse Replication

After failover, data changes may be occurring in AWS.

When returning to the source environment, the architecture must account for those changes.

Conceptually:

```text
Before disaster:

Source → AWS

After failover:

AWS → Source
```

This synchronization step helps avoid losing changes made while operating in AWS.

---

# 22. Failback Planning

Before failing back, verify:

```text
□ Source environment is healthy
□ Network connectivity is restored
□ Required infrastructure is available
□ Data is synchronized
□ Application dependencies are ready
□ DNS/routing plan is ready
□ Recovery point is understood
□ Business owners approve failback
```

---

# 23. Recovery Testing

Testing is mandatory for a reliable DR strategy.

A test should validate:

- Recovery launch
- Application startup
- Data availability
- Network connectivity
- Security
- DNS
- Dependencies
- Performance
- RTO
- RPO

---

# 24. Why DR Testing Matters

Without testing:

```text
Replication appears healthy
        ↓
Disaster occurs
        ↓
Recovery fails
```

With regular testing:

```text
Replication
   ↓
Recovery test
   ↓
Issues discovered
   ↓
Fix
   ↓
Repeat
```

---

# 25. Non-Disruptive Testing

DR testing should ideally validate the recovery environment without unnecessarily disrupting production.

This helps organizations verify:

```text
Can we actually recover?
```

rather than merely:

```text
Is replication running?
```

---

# 26. Recovery Testing Checklist

```text
□ Launch recovery instances
□ Check OS
□ Check storage
□ Check application services
□ Check database
□ Check authentication
□ Check DNS
□ Check network connectivity
□ Check security groups
□ Check application dependencies
□ Measure startup time
□ Measure application availability
□ Validate RTO
□ Validate RPO
□ Document issues
```

---

# 27. Recovery Readiness During Normal Operations

Disaster readiness is not a one-time configuration.

During normal business operations:

```text
Replicate
   ↓
Monitor
   ↓
Test
   ↓
Improve
   ↓
Replicate
   ↓
Monitor
```

This is an ongoing operational process.

---

# 28. Monitoring Replication

Important metrics and signals include:

- Replication status
- Replication lag
- Source connectivity
- Errors
- Recovery readiness
- Agent health
- Staging resources

---

# 29. Replication Lag

Replication lag shows how far the AWS copy is behind the source.

Example:

```text
Source:
12:00:00

AWS:
11:59:55

Lag:
5 seconds
```

Large lag can increase effective data-loss exposure.

---

# 30. Troubleshooting Replication

If replication is unhealthy, investigate:

```text
Source server
      ↓
DRS agent
      ↓
Network
      ↓
AWS DRS
      ↓
Staging resources
```

Possible issues:

- Agent problems
- Network connectivity
- Firewall rules
- Insufficient bandwidth
- Source resource pressure
- Configuration issues

---

# 31. Network Connectivity

Replication requires connectivity between the source environment and AWS.

Consider:

- Internet
- VPN
- AWS Direct Connect
- Firewall
- Proxy
- DNS
- Routing
- Bandwidth

The architecture must support the required replication traffic.

---

# 32. Bandwidth Planning

High-write workloads can generate significant replication traffic.

Consider:

```text
Write Rate
×
Number of Servers
×
Data Change Volume
```

Insufficient bandwidth can increase replication lag.

---

# 33. Recovery VPC

Design a dedicated recovery network where appropriate.

Typical structure:

```text
VPC
 |
 +-- Public Subnets
 |
 +-- Private Subnets
 |
 +-- Route Tables
 |
 +-- Security Groups
 |
 +-- Network ACLs
 |
 +-- NAT / Internet Connectivity
 |
 +-- Load Balancer
```

Use the network architecture required by the recovered application.

---

# 34. Security During Recovery

The recovery environment must maintain production-grade security.

Consider:

- IAM
- Least privilege
- Security groups
- Network ACLs
- KMS encryption
- Secrets
- Logging
- Monitoring
- Private subnets

Do not weaken security merely because the environment is temporary.

---

# 35. IAM

Follow least privilege.

Separate permissions for:

- DRS administration
- Recovery operations
- Monitoring
- Automation

Avoid broad administrator permissions when narrower permissions are sufficient.

---

# 36. Encryption

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

Use AWS KMS where customer-managed encryption keys are required.

---

# 37. Recovery Dependencies

Application recovery must consider all dependencies.

Examples:

- DNS
- Active Directory
- Database
- File systems
- Authentication
- External APIs
- Message queues
- License services
- Network services

A server can recover successfully while the application remains unavailable because a dependency is missing.

---

# 38. Dependency Mapping

Example:

```text
Users
  ↓
DNS
  ↓
Load Balancer
  ↓
Web Tier
  ↓
Application Tier
  ↓
Database
  ↓
External Services
```

Build and test the recovery sequence around the actual dependency graph.

---

# 39. Recovery Order

The recovery order depends on the application.

A typical example:

```text
Networking
   ↓
Identity / DNS
   ↓
Database
   ↓
Application
   ↓
Web
```

Do not assume every architecture uses this exact order.

---

# 40. DNS and Traffic Redirection

After recovery, users may need to reach AWS instead of the failed source environment.

Route 53 can be used for DNS and routing strategies.

Concept:

```text
Users
  ↓
Route 53
  ↓
Primary OR Recovery
```

The routing strategy should match the business requirement.

---

# 41. Multi-AZ vs DRS

### Multi-AZ

Provides high availability by distributing workloads across Availability Zones.

```text
AZ-A
  ↕
AZ-B
```

### DRS

Provides disaster recovery through continuous replication and recovery.

```text
Source
  ↓
AWS DRS
  ↓
Recovery
```

### Memorize

```text
HA → Keep running

DR → Recover after disruption
```

---

# 42. Multi-Region DR

For regional disaster scenarios, consider:

- Separate AWS Region
- DNS failover
- Cross-Region architecture
- Data replication
- Security controls
- Connectivity
- Regional service availability

DRS is one component; the overall architecture must satisfy the complete regional recovery requirement.

---

# 43. DRS vs AWS Backup

| AWS DRS | AWS Backup |
|---|---|
| Disaster recovery | Backup management |
| Continuous replication | Backup copies |
| Server recovery | Resource/data restore |
| Rapid application recovery | Historical retention |
| Recovery into EC2 | Restore supported AWS resources |

They can complement each other.

---

# 44. DRS vs AWS Application Migration Service

| DRS | Application Migration Service |
|---|---|
| Disaster recovery | Migration |
| Maintain DR readiness | Move workloads to AWS |
| Failover / failback | Migration cutover |
| Continuous replication | Replication for migration |
| Recovery operations | Migration operations |

### Exam clue

```text
"Move permanently to AWS"
→ Migration

"Recover after disaster"
→ DRS
```

---

# 45. DRS vs Traditional Backup-and-Restore

Traditional:

```text
Backup
 ↓
Storage
 ↓
Restore
 ↓
Configure
 ↓
Start Application
```

DRS:

```text
Continuous Replication
 ↓
AWS Staging
 ↓
Launch Recovery
 ↓
Application Available
```

DRS is generally better suited to requirements demanding rapid server recovery and low RPO.

---

# 46. Cost Optimization

DRS cost considerations include:

- Staging resources
- Replicated storage
- EBS
- Network transfer
- Recovery instances
- Testing
- Monitoring

The architecture can reduce costs compared with maintaining a complete active standby environment.

---

# 47. Recovery Instance Right-Sizing

Do not provision the most expensive recovery instance by default.

Evaluate:

- CPU
- Memory
- Network
- Storage
- Application performance
- RTO

The recovery instance must meet the application's recovery SLA.

---

# 48. Automation

Automate repetitive recovery tasks where appropriate.

Possible services:

- AWS DRS
- AWS Systems Manager
- AWS Lambda
- AWS Step Functions
- AWS CloudFormation
- Terraform
- Amazon EventBridge
- Route 53

Example:

```text
Disaster
   ↓
DRS Recovery
   ↓
Automation
   ↓
Configuration
   ↓
Validation
   ↓
DNS Update
   ↓
Application Available
```

---

# 49. DR Runbook

A good runbook should define:

```text
1. Disaster detection
2. Decision authority
3. Recovery point selection
4. Failover procedure
5. Recovery order
6. Application validation
7. DNS/routing changes
8. User communication
9. Monitoring
10. Failback criteria
11. Failback procedure
12. Post-incident review
```

---

# 50. Operational Readiness

During normal operations, continuously verify:

```text
Replication healthy?
      ↓
Recovery configuration correct?
      ↓
Network still available?
      ↓
Security still valid?
      ↓
Application dependencies mapped?
      ↓
DR test successful?
      ↓
RTO/RPO still achievable?
```

Business requirements can change, so DR configuration must evolve too.

---

# 51. Ransomware and Logical Corruption

Continuous replication is not a replacement for all backup protections.

If corrupted data is replicated:

```text
Corruption
 ↓
Replication
 ↓
AWS
```

The recovery environment may contain the same corruption.

Consider:

- Backup retention
- Immutable backups
- Multiple recovery points
- Least privilege
- MFA
- Monitoring
- Isolation
- Security controls

---

# 52. Business Continuity

DR is part of business continuity.

Consider:

```text
Technology
+
People
+
Processes
+
Facilities
+
Communication
+
Third Parties
```

A technically correct recovery architecture can still fail if the organization does not have the operational process to activate it.

---

# 53. SAP-C02 Scenario Mapping

| Exam Requirement | Think |
|---|---|
| Continuously replicate servers to AWS | AWS DRS |
| Recover on-premises application in AWS | AWS DRS |
| Maintain DR readiness | AWS DRS + monitoring/testing |
| Low RPO | Continuous replication |
| Rapid recovery | DRS recovery launch |
| Recover complete server | DRS |
| Move server permanently to AWS | Application Migration Service |
| Central backup management | AWS Backup |
| Test recovery without production impact | DRS recovery testing |
| Return workload to source | Failback |
| Move workload to AWS after disaster | Failover |
| Protect against AZ failure | Multi-AZ |
| Protect against regional failure | Multi-Region DR architecture |
| Redirect users | Route 53 / routing |
| Protect recovery environment | IAM + KMS + network security |
| Reduce DR infrastructure cost | Staging/recovery model |
| Data corruption before disaster | Earlier recovery point / backup strategy |
| Automate recovery | DRS + Systems Manager/Lambda/Step Functions |

---

# 54. Common SAP-C02 Exam Traps

## Trap 1 — DRS is a backup service

**Wrong.**

DRS is primarily a continuous replication and disaster recovery service.

---

## Trap 2 — DRS eliminates the need for DR testing

**Wrong.**

Testing is essential.

---

## Trap 3 — Latest replicated state is always the best

**Wrong.**

Consider corruption and recovery-point selection.

---

## Trap 4 — Failover means permanent migration

**Wrong.**

Failover is a disaster recovery operation. Failback can return the workload to the source.

---

## Trap 5 — Failback happens automatically

**Not necessarily.**

It requires planning, synchronization, validation, and routing changes as appropriate.

---

## Trap 6 — Multi-AZ replaces DRS

**Wrong.**

Multi-AZ provides HA; DRS provides disaster recovery for supported server workloads.

---

## Trap 7 — Replication means the application is ready

**Wrong.**

You must test:

- Application
- Database
- Networking
- DNS
- Identity
- Dependencies
- Security

---

## Trap 8 — Full production standby is always required

**Wrong.**

DRS's staging/recovery approach can reduce ongoing infrastructure costs.

---

## Trap 9 — DRS is only for on-premises workloads

**Wrong.**

It supports supported source environments beyond traditional on-premises deployments.

---

## Trap 10 — Bigger recovery instances are automatically better

**Wrong.**

Right-size based on the recovery SLA and workload.

---

# 55. High-Yield DRS Architecture

```text
             SOURCE ENVIRONMENT
        ┌─────────────────────────┐
        │                         │
        │  Application Servers    │
        │  Database Servers       │
        │  File Servers           │
        │                         │
        └───────────┬─────────────┘
                    │
                 DRS Agent
                    │
          Continuous Replication
                    │
                    v
        ┌─────────────────────────┐
        │ AWS DRS Staging Area    │
        │                         │
        │ Replicated Data         │
        │ Recovery Resources      │
        └───────────┬─────────────┘
                    │
              Failover / Test
                    │
                    v
        ┌─────────────────────────┐
        │ AWS Recovery VPC        │
        │                         │
        │ EC2 Recovery Instances │
        │ App / DB / Web          │
        └───────────┬─────────────┘
                    │
                    v
                 Route 53
                    │
                    v
                  Users
```

---

# 56. DRS Decision Tree

```text
Need disaster recovery?
        ↓
Are existing servers the workload?
        ↓
Need continuous replication?
        ↓
Need recovery into AWS?
        ↓
AWS Elastic Disaster Recovery
        ↓
Define RPO/RTO
        ↓
Configure staging
        ↓
Configure recovery settings
        ↓
Monitor replication
        ↓
Test recovery
        ↓
Disaster
        ↓
Select recovery point
        ↓
Failover
        ↓
Operate in AWS
        ↓
Restore source
        ↓
Reverse/synchronize
        ↓
Failback
```

---

# 57. DR Readiness Checklist

```text
□ Critical servers identified
□ RPO defined
□ RTO defined
□ DRS agent installed
□ Replication healthy
□ Replication lag monitored
□ AWS staging configured
□ Recovery VPC configured
□ Security groups configured
□ IAM configured
□ Encryption configured
□ Recovery instance types defined
□ Application dependencies mapped
□ DNS/routing plan defined
□ Recovery order documented
□ DR runbook created
□ Recovery tested
□ Failover tested
□ Failback tested
□ Actual RTO measured
□ Actual RPO measured
□ Costs reviewed
□ Regular DR testing scheduled
```

---

# 58. 30-Second Revision

```text
DRS
 ↓
Agent on source
 ↓
Continuous replication
 ↓
AWS staging
 ↓
Monitor readiness
 ↓
Test recovery
 ↓
Disaster
 ↓
Select recovery point
 ↓
Failover to AWS
 ↓
Run application
 ↓
Restore source
 ↓
Reverse/synchronize
 ↓
Failback
```

### Memorize:

```text
RPO → Data loss
RTO → Downtime

Failover → Source → AWS
Failback → AWS → Source

DRS → Disaster Recovery
MGN → Migration
Backup → Backup/Retention
Multi-AZ → High Availability
```

---

# 59. SAP-C02 Golden Rules

1. **AWS DRS is for disaster recovery of supported server workloads.**
2. **Continuous replication is the core DRS mechanism.**
3. **The DRS agent enables source-server replication.**
4. **Replicated data is maintained in the AWS staging environment.**
5. **Recovery instances are launched in AWS when recovery is required.**
6. **Define RTO and RPO before designing the DR solution.**
7. **RPO = acceptable data loss.**
8. **RTO = acceptable downtime.**
9. **Failover moves the workload to AWS recovery infrastructure.**
10. **Failback returns the workload to the original source environment.**
11. **Reverse/synchronize data before failback so changes made during AWS operation are not lost.**
12. **Do not assume the latest recovery point is always safe.**
13. **Regular recovery testing is essential.**
14. **Replication health does not prove application recovery readiness.**
15. **Test dependencies, DNS, networking, identity, databases, and application startup.**
16. **Secure the recovery environment using least privilege, encryption, and network controls.**
17. **Use appropriate recovery instance sizes rather than unnecessarily expensive instances.**
18. **Use AWS Backup for backup/retention requirements in addition to DRS when appropriate.**
19. **Use AWS Application Migration Service when the primary goal is migration rather than disaster recovery.**
20. **Multi-AZ is primarily HA; DRS addresses server/application disaster recovery.**
21. **Use Route 53 or another appropriate routing mechanism to redirect users after recovery when required.**
22. **Consider immutable backups and multiple recovery points for ransomware/corruption scenarios.**
23. **Automate repetitive recovery tasks to reduce human error.**
24. **Measure actual RTO/RPO during DR exercises.**
25. **A DR solution is only valuable if the organization can successfully execute it under pressure.**

---

# 60. Final SAP-C02 Mental Model

When you see a DRS scenario, ask:

```text
What workload needs protection?
        ↓
Existing server/application?
        ↓
Need continuous replication?
        ↓
Need AWS as recovery environment?
        ↓
AWS DRS
        ↓
What is the RPO?
        ↓
What is the RTO?
        ↓
How much staging infrastructure is required?
        ↓
How will recovery instances launch?
        ↓
What are the dependencies?
        ↓
How will users be redirected?
        ↓
How is recovery tested?
        ↓
How will failback work?
        ↓
How is the recovery environment secured?
        ↓
Does the design meet:
RPO + RTO + Security + Cost?
```

## Ultimate DRS Formula

```text
AWS DRS
=
Source Agent
+
Continuous Replication
+
Staging Area
+
Recovery Configuration
+
Recovery Testing
+
Failover
+
AWS Operation
+
Reverse Synchronization
+
Failback
```

> **SAP-C02 takeaway:** AWS Elastic Disaster Recovery is not just about copying server data to AWS. A professional-grade DR architecture requires **continuous replication, recovery readiness, defined RPO/RTO, tested recovery configurations, dependency planning, secure AWS networking, failover procedures, reverse synchronization, and failback**. The strongest SAP-C02 answer is the design that reliably meets the business recovery objectives while minimizing unnecessary operational complexity and cost.
