# Modernize a Monolith to Amazon ECS and AWS Fargate Using Application Discovery
## SAP-C02 Exam Revision README

> **Course:** Modernize a monolith to ECS and Fargate using Application Discovery  
> **Duration:** 50 minutes | **Level:** Intermediate  
> **Exam target:** AWS Certified Solutions Architect – Professional (SAP-C02)

> **Important current-service note:** AWS Application Discovery Service stopped accepting new customers on November 7, 2025. Existing customers can continue ongoing projects, while AWS recommends AWS Transform for new discovery projects. This README preserves the course concepts because they remain highly relevant to SAP-C02 migration/discovery questions. citeturn0search0turn0search2

---

# 1. Core Idea

The course focuses on moving a **monolithic application** from an existing environment into:

```text
On-Premises Monolith
        ↓
Application Discovery
        ↓
Dependency / Utilization Analysis
        ↓
Containerization
        ↓
Amazon ECR
        ↓
Amazon ECS
        ↓
AWS Fargate
```

The key scenario is:

> **Modernize/replatform the monolith to containers without changing the application code.**

This is important because **containerizing a monolith is not the same as breaking it into microservices**.

---

# 2. Migration Strategy

The course's "no code changes" approach is primarily a **replatform/containerization** pattern.

```text
Existing VM / Server
        ↓
Package application
        ↓
Container image
        ↓
ECR
        ↓
ECS + Fargate
```

You change the **deployment platform**, not necessarily the application's internal architecture.

### Rehost

```text
Server → EC2
```

### Replatform

```text
Application → Containers → ECS/Fargate
```

### Refactor

```text
Monolith
   ↓
Redesign
   ↓
Microservices
```

**SAP-C02 distinction:**

> No-code-change container migration → think **replatform/containerization**, not refactoring.

AWS Prescriptive Guidance identifies replatforming to Amazon ECS as a migration pattern and distinguishes it from re-architecting/refactoring. citeturn0search12

---

# 3. What Application Discovery Does

Application Discovery Service collects information about existing workloads to help plan migrations.

It can collect:

- Server configuration
- Server utilization
- Network connections
- Application dependencies
- Database information
- Workload behavior

The information helps answer:

```text
What is running?
What does it depend on?
How busy is it?
What communicates with what?
What should migrate together?
```

AWS documentation describes Application Discovery Service as a migration-planning service that gathers configuration, usage, and behavior data and integrates with AWS Migration Hub. citeturn0search2turn0search13

---

# 4. Why Discovery Comes First

Do not blindly containerize a monolith.

First understand:

```text
Monolith
   |
   ├── Database
   ├── File system
   ├── External API
   ├── DNS
   ├── Authentication
   ├── Other servers
   └── Network dependencies
```

Discovery identifies these dependencies.

### SAP-C02 principle

> **Discover → assess → plan → migrate → validate → optimize.**

---

# 5. Dependency Mapping

One of the most important concepts.

Example:

```text
                    ┌→ Database
                    |
Monolith ───────────┼→ Redis
                    |
                    ├→ LDAP
                    |
                    └→ External API
```

If you migrate only the monolith and forget a critical dependency:

```text
Container starts
      ↓
Application fails
      ↓
Dependency unavailable
```

Dependency mapping reduces this risk.

---

# 6. Agent-Based vs Agentless Discovery

Application Discovery has historically supported collection through agents and agentless collection.

### Agent-based

```text
Server
 ↓
Discovery Agent
 ↓
AWS
```

Useful when detailed OS-level/runtime information is required.

### Agentless

```text
Environment
 ↓
Agentless Collector
 ↓
AWS
```

Useful when installing software on every server is undesirable.

AWS documents Discovery Agent installation for Linux and Windows, while current AWS guidance also highlights agentless collection capabilities. citeturn0search11turn0search15

---

# 7. Current AWS Direction

For **new** discovery projects:

```text
AWS Application Discovery Service
             ↓
       AWS Transform
```

AWS states that Application Discovery Service is no longer open to new customers and recommends AWS Transform as the next-generation alternative with discovery and assessment capabilities. citeturn0search0

### Exam note

If an SAP-C02 question is based on current AWS services, pay attention to the wording and service availability.

If the question is explicitly testing an older migration architecture/course, understand the original Application Discovery concepts.

---

# 8. Discovery Data

Typical discovery information:

```text
Server
 ├── CPU
 ├── Memory
 ├── Disk
 ├── Network
 ├── Processes
 └── Connections
```

This helps determine:

- Resource sizing
- Application dependencies
- Migration groups
- Capacity requirements
- Migration order

---

# 9. Migration Waves

Do not migrate a huge application portfolio randomly.

Group workloads into migration waves.

Example:

```text
Wave 1
 ├── Low-risk application

Wave 2
 ├── Internal applications

Wave 3
 ├── Customer-facing workloads

Wave 4
 └── Critical production systems
```

A dependency-aware migration wave is safer than simply migrating servers alphabetically.

---

# 10. Monolith Containerization

Target:

```text
                 ALB
                  ↓
             ECS Service
          ┌──────┼──────┐
          ↓      ↓      ↓
       Fargate Fargate Fargate
        Task    Task    Task
           \     |     /
             Monolith
```

The application can remain one logical application.

### Important

```text
Containerized monolith ≠ microservices
```

You can modernize infrastructure first and refactor the application later.

---

# 11. Why ECS?

Amazon ECS provides container orchestration.

It manages:

- Container scheduling
- Desired task count
- Service deployments
- Health checks
- Scaling integration
- Networking integration

---

# 12. Why Fargate?

AWS Fargate is the serverless compute engine for containers.

With:

```text
ECS + Fargate
```

you don't manage the underlying container servers/EC2 capacity.

Think:

```text
ECS
 ↓
"Manage my containers"

Fargate
 ↓
"Run them without managing servers"
```

AWS describes Fargate as a technology for running containers without provisioning or managing EC2 servers/clusters. citeturn0search6turn0search10

---

# 13. ECS EC2 vs Fargate

| Requirement | ECS EC2 | ECS Fargate |
|---|---|---|
| Manage underlying servers | Yes | No |
| Maximum host control | Higher | Lower |
| Operational overhead | Higher | Lower |
| Serverless container experience | No | Yes |
| Custom host configuration | Better | Limited |
| Simple migration | Possible | Often attractive |

### SAP-C02 clue

If the requirement says:

> "Minimize infrastructure management."

Think:

```text
ECS + Fargate
```

---

# 14. Container Image

The monolith must become a container image.

Typical flow:

```text
Application
 ↓
Dockerfile
 ↓
Container image
 ↓
Amazon ECR
 ↓
ECS task definition
 ↓
Fargate
```

ECR is the container image registry.

---

# 15. ECS Task Definition

The task definition describes how the container should run.

Think:

```text
Task Definition
 ├── Image
 ├── CPU
 ├── Memory
 ├── Ports
 ├── Environment
 ├── IAM roles
 ├── Logging
 └── Storage
```

---

# 16. ECS Service

A service maintains the desired number of running tasks.

```text
Desired count = 3

ECS Service
 ├── Task 1
 ├── Task 2
 └── Task 3
```

If a task fails:

```text
Task dies
 ↓
ECS replaces it
```

---

# 17. Application Load Balancer

Typical architecture:

```text
Internet
   ↓
Route 53
   ↓
ALB
   ↓
ECS Service
   ↓
Fargate Tasks
```

Use ALB when HTTP/HTTPS application routing is required.

---

# 18. High Availability

Do not put every task in one Availability Zone.

Prefer:

```text
              ALB
          /                  /                  AZ-A         AZ-B
        ↓             ↓
    Fargate         Fargate
      Task            Task
```

For higher resilience:

```text
AZ-A
AZ-B
AZ-C
```

as appropriate for the workload and Region.

---

# 19. ECS Networking

Fargate tasks use:

```text
awsvpc
```

networking.

Each task receives its own network interface/IP networking context.

AWS ECS documentation shows Fargate task definitions using `awsvpc` network mode. citeturn0search1

---

# 20. Private Subnets

Typical production architecture:

```text
Internet
   ↓
ALB
   ↓
Private Subnets
   ↓
Fargate Tasks
```

Fargate application tasks generally should not require public IP addresses when the ALB is the internet-facing entry point.

---

# 21. Security Groups

Example:

```text
ALB SG
 ↓ TCP 443
ECS SG
 ↓ application port
Database SG
```

Rules should be narrowly scoped.

Do not:

```text
0.0.0.0/0 → Database
```

unless there is an exceptional, explicitly justified requirement.

---

# 22. IAM Roles

Separate roles.

### Task execution role

Used for ECS/Fargate operations such as pulling images/logging as required by the task.

### Task role

Used by the application inside the container to access AWS services.

Example:

```text
Container
   ↓
Task Role
   ↓
S3 / DynamoDB / Secrets Manager
```

**SAP-C02 trap:**

> Do not give the task role unnecessary permissions.

---

# 23. Secrets

Do not hard-code:

```text
DB_PASSWORD=secret
```

Use:

```text
Secrets Manager
       ↓
ECS task
```

or appropriate parameter/configuration services.

---

# 24. Configuration

Externalize environment-specific configuration.

```text
Application image
        +
External configuration
        ↓
Same image
        ↓
Dev / Test / Prod
```

This improves deployment consistency.

---

# 25. Persistent Storage

A monolith may depend on local disk.

Container local storage is not equivalent to durable shared application storage.

Ask:

```text
Does the application write files?
        ↓
Are files required after task replacement?
        ↓
Does multiple-task sharing exist?
```

Possible choices include:

- Amazon EFS
- Amazon S3
- Database/object storage
- Ephemeral task storage where persistence is not required

---

# 26. Local File System Trap

Original VM:

```text
/app/uploads
```

Containerized:

```text
Task A → local file
Task B → cannot see it
```

If tasks must share persistent files:

```text
Fargate Tasks
   ↓
Amazon EFS
```

If files are objects:

```text
Application
 ↓
S3
```

---

# 27. Database Dependency

The monolith may depend on:

```text
On-premises DB
```

You don't necessarily need to migrate the database at exactly the same time.

Possible architecture:

```text
Fargate
   ↓
VPN / Direct Connect
   ↓
On-prem DB
```

This can enable phased migration.

---

# 28. Hybrid Migration

A common transitional architecture:

```text
AWS
 ┌─────────────────────┐
 │ ECS/Fargate         │
 │      ↓              │
 │ Application         │
 └─────────┬───────────┘
           │
       VPN / DX
           │
 ┌─────────▼───────────┐
 │ On-premises DB      │
 └─────────────────────┘
```

This can reduce migration risk by separating application migration from database migration.

---

# 29. Direct Connect vs VPN

### Site-to-Site VPN

Think:

```text
Fast to establish
Encrypted over internet
Flexible
```

### Direct Connect

Think:

```text
Dedicated private connectivity
More consistent network characteristics
Useful for enterprise hybrid connectivity
```

Choose according to:

- Bandwidth
- Latency
- Availability
- Security
- Cost
- Operational requirements

---

# 30. Service Discovery

If the monolith later becomes multiple services:

```text
Service A
 ↓
Service discovery
 ↓
Service B
```

AWS Cloud Map can provide service discovery for ECS.

ECS also has Service Connect capabilities for service-to-service communication and discovery. citeturn0search1turn0search7

---

# 31. Monolith First, Microservices Later

A useful modernization path:

```text
Phase 1
VM Monolith
    ↓
Containerized Monolith

Phase 2
ECS/Fargate

Phase 3
Externalize dependencies

Phase 4
Identify bounded contexts

Phase 5
Extract services gradually
```

This reduces the risk of performing a huge rewrite during migration.

---

# 32. Strangler Pattern

When gradually replacing a monolith:

```text
                    ┌→ New Service A
Client → ALB/Router ┼→ New Service B
                    └→ Old Monolith
```

Over time:

```text
Old Monolith
 ↓
Smaller
 ↓
Smaller
 ↓
Retired
```

This is the **Strangler Fig pattern**.

---

# 33. Blue/Green Deployment

Useful for safer releases.

```text
ALB
 ├── Blue → Current version
 └── Green → New version
```

Test Green:

```text
Green healthy?
   ↓
Shift traffic
   ↓
Blue drained
```

AWS ECS supports deployment strategies that can be used to reduce deployment risk. AWS examples also describe blue/green approaches for ECS/Fargate workloads. citeturn0search8

---

# 34. Rolling Deployment

Example:

```text
Old: 4 tasks

Replace gradually:

Old → Old → New → New
```

Lower infrastructure duplication than blue/green, but rollback characteristics differ.

---

# 35. Auto Scaling

ECS service can scale task count.

```text
Low traffic
 ↓
2 tasks

High traffic
 ↓
6 tasks
```

Possible scaling signals:

- CPU utilization
- Memory utilization
- ALB request count per target
- Application-specific metrics

---

# 36. Target Tracking

Example:

```text
Target CPU = 60%
```

If utilization rises:

```text
Scale out
```

If utilization falls:

```text
Scale in
```

Use suitable CloudWatch metrics.

---

# 37. Health Checks

ALB:

```text
/health
```

checks whether the application is healthy.

If a task fails:

```text
Unhealthy
 ↓
Remove from traffic
 ↓
ECS replaces task
```

A health endpoint should test the right level of application health without creating unnecessary dependency failures.

---

# 38. Logging

Container logs should be centralized.

Typical:

```text
Fargate
 ↓
CloudWatch Logs
```

Benefits:

- Centralized troubleshooting
- Search
- Metrics/alarms integration
- Operational visibility

---

# 39. Monitoring

Monitor:

- CPU
- Memory
- Task count
- ALB latency
- Request count
- HTTP errors
- Container restarts
- Application errors

Use CloudWatch and appropriate container observability features.

---

# 40. CI/CD

A modern deployment pipeline:

```text
Source
 ↓
Build
 ↓
Test
 ↓
Docker image
 ↓
ECR
 ↓
ECS deployment
 ↓
Health check
 ↓
Production
```

Possible AWS services include:

- CodePipeline
- CodeBuild
- ECR
- ECS
- CodeDeploy where appropriate

---

# 41. Infrastructure as Code

Use:

```text
CloudFormation / CDK / Terraform
```

for repeatable infrastructure.

Avoid manually creating every production resource.

---

# 42. Migration Validation

Before production cutover:

```text
Container
 ↓
Functional testing
 ↓
Performance testing
 ↓
Dependency validation
 ↓
Security validation
 ↓
Failover testing
 ↓
Traffic migration
```

---

# 43. Cutover Strategy

A safer cutover:

```text
Old environment
       +
New ECS environment
       ↓
Test
       ↓
Small traffic percentage
       ↓
Monitor
       ↓
Increase traffic
       ↓
100%
```

This reduces migration risk.

---

# 44. Rollback

Always have:

```text
New environment
      ↓
Problem
      ↓
Route traffic back
      ↓
Old environment
```

Do not decommission the old environment immediately after cutover if rollback is required.

---

# 45. No-Code-Change Constraint

If the requirement says:

> "Migrate with no application code changes."

Avoid answers requiring:

- Microservice decomposition
- Major application rewrite
- New communication protocols
- Significant application redesign

Prefer:

```text
Containerize
+
ECS
+
Fargate
+
External infrastructure changes
```

---

# 46. What Changes Without Application Code?

You can often change:

```text
Compute platform
Networking
Load balancing
Deployment
Scaling
Logging
Secrets
Configuration
```

while keeping:

```text
Business logic
```

unchanged.

---

# 47. What Might Still Need Adaptation?

"No code changes" does not mean "no migration work."

Potential issues:

- Hard-coded IP addresses
- Local filesystem assumptions
- Host-level dependencies
- Privileged operations
- OS-specific dependencies
- Licensing tied to host identity
- Static configuration
- Incompatible architecture

These must be assessed during discovery.

---

# 48. Application Compatibility Checklist

Before containerization:

```text
☐ Application starts correctly
☐ All dependencies identified
☐ Ports identified
☐ Environment variables identified
☐ Configuration externalized
☐ Filesystem requirements understood
☐ Database connectivity confirmed
☐ External APIs identified
☐ Authentication dependencies identified
☐ License dependencies checked
☐ Logging works
☐ Health check available
```

---

# 49. Migration Architecture

```text
                Users
                  |
               Route 53
                  |
                 ALB
                  |
          ┌───────┴───────┐
          ↓               ↓
      Fargate           Fargate
       Task              Task
          \               /
           \             /
            ECS Service
                 |
        ┌────────┼────────┐
        ↓        ↓        ↓
       S3       DB     Secrets
                         Manager
```

Optional hybrid:

```text
Fargate
   ↓
VPN / Direct Connect
   ↓
On-prem DB
```

---

# 50. Migration Decision Tree

```text
Need application code changes?
          |
      ┌───┴───┐
      NO      YES
      |        |
Containerize   Consider
      |       refactor/
      ↓       replatform
ECS/Fargate
```

Then:

```text
Need minimum infrastructure management?
          |
         YES
          ↓
       Fargate
```

Then:

```text
Need HTTP/HTTPS ingress?
          |
         YES
          ↓
          ALB
```

Then:

```text
Need persistent shared files?
          |
         YES
          ↓
     EFS / S3
```

Then:

```text
Database still on-prem?
          |
         YES
          ↓
VPN / Direct Connect
```

---

# 51. Application Discovery → ECS/Fargate Flow

```text
1. Discover
      ↓
2. Identify dependencies
      ↓
3. Measure utilization
      ↓
4. Group migration wave
      ↓
5. Build container
      ↓
6. Store image in ECR
      ↓
7. Create ECS task definition
      ↓
8. Deploy Fargate service
      ↓
9. Configure ALB/network/security
      ↓
10. Validate
      ↓
11. Shift traffic
      ↓
12. Monitor
      ↓
13. Decommission old environment
```

---

# 52. Common SAP-C02 Exam Traps

## Trap 1: "Containerize" means microservices

Wrong.

A monolith can run inside one or more containers.

---

## Trap 2: Use EC2 because containers need servers

Not necessarily.

Fargate removes the need to manage container hosts.

---

## Trap 3: Migrate before discovery

Risky.

First understand dependencies and utilization.

---

## Trap 4: Put Fargate tasks in public subnets unnecessarily

Usually poor security architecture.

Prefer private tasks behind an ALB when appropriate.

---

## Trap 5: Store passwords in task definitions

Wrong.

Use Secrets Manager/appropriate secure configuration.

---

## Trap 6: Use local container storage for permanent data

Usually wrong.

Use durable storage such as EFS, S3, or a database depending on the data.

---

## Trap 7: Give containers public IPs by default

Not required when private tasks receive traffic through an ALB.

---

## Trap 8: Migrate the application and database simultaneously

Not always necessary.

A phased hybrid architecture may reduce risk.

---

## Trap 9: Ignore network dependencies

Discovery exists specifically to uncover dependencies.

---

## Trap 10: Scale vertically only

Container platforms are well suited to horizontal scaling when the application supports it.

---

## Trap 11: No health checks

Unhealthy tasks should be detected and removed from traffic.

---

## Trap 12: Immediately terminate the old environment

Keep rollback capability until the migration is validated.

---

# 53. SAP-C02 Scenario Recognition

### Scenario:

> A company wants to move a monolithic application to containers with no application code changes.

Think:

```text
Containerize
→ ECR
→ ECS
→ Fargate
```

---

### Scenario:

> The company first needs to understand server utilization and application dependencies.

Think:

```text
Application Discovery
```

For new AWS discovery projects today, AWS recommends AWS Transform instead. citeturn0search0

---

### Scenario:

> The company wants to minimize infrastructure management.

Think:

```text
ECS + Fargate
```

---

### Scenario:

> Multiple Fargate tasks need shared persistent files.

Think:

```text
Amazon EFS
```

---

### Scenario:

> Public HTTP traffic must reach multiple Fargate tasks.

Think:

```text
ALB
→ ECS Service
→ Fargate Tasks
```

---

### Scenario:

> Application remains unchanged, but database migration will happen later.

Think:

```text
Fargate
→ VPN / Direct Connect
→ On-prem database
```

---

### Scenario:

> New version must be tested before all traffic is moved.

Think:

```text
Blue/Green
or controlled traffic shifting
```

---

# 54. Fargate vs Lambda

### Fargate

Think:

```text
Containerized application
Long-running process
Existing application
Custom runtime
```

### Lambda

Think:

```text
Event-driven
Short-duration functions
Serverless functions
```

If the goal is to move an existing monolith with minimal/no code changes:

```text
Fargate
```

is generally a much more natural fit than rewriting the application into Lambda functions.

---

# 55. Fargate vs EC2

### Choose Fargate when:

- Minimal infrastructure management
- Containerized workload
- Flexible task scaling
- No host management requirement

### Choose ECS on EC2 when:

- Need host-level control
- Specialized host configuration
- Certain capacity/cost requirements
- Need capabilities unavailable/suitable on Fargate

---

# 56. Migration 7R Context

Remember the common migration strategies:

```text
Retire
Retain
Rehost
Relocate
Repurchase
Replatform
Refactor/Re-architect
```

For this course:

```text
Monolith
 ↓
Containerize
 ↓
ECS/Fargate
```

Think primarily:

> **Replatform**

AWS migration guidance distinguishes replatforming from rehosting and refactoring. citeturn0search12

---

# 57. Golden Rules for SAP-C02

1. Discovery comes before migration.
2. Identify application dependencies before cutover.
3. Use utilization data for capacity planning.
4. Containerized monolith ≠ microservices.
5. No-code-change container migration is generally replatforming.
6. ECR stores container images.
7. ECS orchestrates containers.
8. Fargate runs containers without managing servers.
9. ECS services maintain desired task count.
10. ALB is a common HTTP/HTTPS entry point.
11. Use private subnets for application tasks when appropriate.
12. Use security groups to restrict traffic.
13. Use IAM task roles with least privilege.
14. Keep secrets out of images and task definitions.
15. Use Secrets Manager/secure configuration services.
16. Don't rely on container local storage for durable shared data.
17. Use EFS for appropriate shared file-system requirements.
18. Use S3 for object storage.
19. Hybrid connectivity can allow phased database migration.
20. VPN is simpler/faster to establish; Direct Connect provides dedicated connectivity.
21. Use multi-AZ deployment for resilience.
22. Use ECS service auto scaling for variable workloads.
23. Use health checks to remove unhealthy tasks.
24. Centralize container logs.
25. Monitor CPU, memory, latency, errors, and task health.
26. Use CI/CD for repeatable deployments.
27. Use Infrastructure as Code.
28. Blue/green can reduce deployment/cutover risk.
29. Keep rollback capability during migration.
30. Don't decompose the monolith when the requirement says no code changes.
31. Use service discovery when multiple services need dynamic discovery.
32. AWS Cloud Map can provide ECS service discovery.
33. ECS Service Connect can simplify service-to-service discovery/communication.
34. Don't expose databases publicly.
35. Don't give containers unnecessary AWS permissions.
36. Validate dependencies before moving production traffic.
37. Migrate in waves where appropriate.
38. Use discovery data to group related applications.
39. Separate application migration from database migration when this reduces risk.
40. Always design the rollback path.

---

# 58. 30-Second Revision

```text
APPLICATION DISCOVERY
        ↓
Understand:
 ├── Servers
 ├── Dependencies
 ├── Utilization
 └── Network
        ↓
CONTAINERIZE MONOLITH
        ↓
       ECR
        ↓
       ECS
        ↓
     FARGATE
        ↓
       ALB
        ↓
   Users / Clients
```

Remember:

```text
Discovery = Understand
ECR       = Store image
ECS       = Orchestrate
Fargate   = Run containers
ALB       = Distribute HTTP/HTTPS traffic
IAM       = Control AWS access
Secrets   = Protect credentials
EFS       = Shared durable filesystem
S3        = Object storage
CloudWatch= Observe
```

---

# 59. Ultimate SAP-C02 Mental Model

When you see a migration scenario:

```text
1. What exists today?
          ↓
2. What are the dependencies?
          ↓
3. What is the migration strategy?
          ↓
4. Are code changes allowed?
          ↓
5. Does the application fit containers?
          ↓
6. ECS EC2 or Fargate?
          ↓
7. How will traffic reach the application?
          ↓
8. Where will persistent data live?
          ↓
9. How will secrets/configuration be managed?
          ↓
10. How will the application scale?
          ↓
11. How will failures be detected?
          ↓
12. How will deployment/rollback work?
          ↓
13. How will the old environment be retired?
```

---

# 60. Final Exam Formula

```text
DISCOVER
   +
DEPENDENCY MAP
   +
REPLATFORM
   +
ECR
   +
ECS
   +
FARGATE
   +
PRIVATE NETWORKING
   +
ALB
   +
LEAST PRIVILEGE
   +
DURABLE STORAGE
   +
AUTOSCALING
   +
OBSERVABILITY
   +
CONTROLLED CUTOVER
   +
ROLLBACK
   =
STRONG SAP-C02 MIGRATION ARCHITECTURE
```

> **One-line memory aid:**  
> **Discover first → containerize the monolith → store image in ECR → orchestrate with ECS → run on Fargate → expose through ALB → secure with IAM/SG/Secrets → externalize durable data → scale/observe → migrate traffic gradually → keep rollback.**
