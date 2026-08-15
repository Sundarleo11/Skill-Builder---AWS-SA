# Amazon Aurora Architecture – SAP-C02 Exam Revision README

> **Target exam:** AWS Certified Solutions Architect – Professional (SAP-C02)
>
> **Course:** AWS PartnerCast – Deep Dive into Amazon Aurora Architecture – Technical
>
> **Duration:** 35 minutes
>
> **Level:** Fundamental
>
> **Focus:** Aurora architecture, storage/compute separation, shared distributed storage, Aurora Replicas, failover, Aurora Global Database, I/O-Optimized, Serverless v2, scaling, backup/recovery, integrations, performance, and SAP-C02 architecture scenarios.

---

# 1. Why Aurora Matters for SAP-C02

Amazon Aurora is a managed relational database engine compatible with **MySQL and PostgreSQL**. Its most important architectural difference from traditional relational database deployments is the separation of **compute and storage**. Aurora DB instances connect to a shared distributed cluster volume rather than each instance maintaining its own independent copy of database storage. citeturn0search0turn0search1

Core architecture:

```text
                    APPLICATION
                         |
              ┌──────────┴──────────┐
              |                     |
          Writer DB            Reader DB
           Instance             Instance
              |                     |
              └──────────┬──────────┘
                         |
                 SHARED AURORA
                 CLUSTER VOLUME
                         |
          ┌──────────────┼──────────────┐
          ↓              ↓              ↓
        AZ-1            AZ-2           AZ-3
      Storage          Storage        Storage
```

### SAP-C02 clue

If the question emphasizes:

> High availability + shared storage + fast reader scaling + managed relational database.

Think:

**Amazon Aurora.**

---

# 2. Aurora's Core Architecture

Aurora separates:

```text
COMPUTE
   +
STORAGE
```

The compute layer consists of DB instances.

The storage layer is a distributed cluster volume.

This architecture allows Aurora to add or remove DB instances without copying the complete database contents to each new instance. citeturn0search0

---

# 3. Compute Layer

The compute layer contains Aurora DB instances.

Typical roles:

```text
Writer
  ↓
Accepts writes

Readers
  ↓
Serve read traffic
```

A cluster can contain a primary/writer instance and Aurora Replicas.

The database instances use the shared Aurora storage volume.

---

# 4. Storage Layer

Aurora uses a distributed shared storage architecture.

The cluster volume is replicated across multiple Availability Zones in the Region. AWS documentation describes copies across three AZs, independent of the number of DB instances. citeturn0search0

Important concept:

```text
DB Instance
    ↓
Shared Cluster Volume
    ↓
Multiple AZs
```

This means storage durability is not dependent on having a large number of database instances.

---

# 5. Six Copies vs Three AZs

A common SAP-C02 memory point is:

```text
3 Availability Zones
6 storage copies
```

Aurora's storage layer uses six copies distributed across three AZs in its classic architecture. AWS documentation emphasizes that the data copies span three AZs; AWS technical material describes the six-copy design. citeturn0search6turn0search9

### Exam memory

```text
3 AZs
+
6 storage copies
=
Highly durable regional storage
```

Do not confuse:

```text
6 storage copies
```

with:

```text
6 DB instances
```

They are different concepts.

---

# 6. Storage Is Independent of DB Instances

Suppose:

```text
Writer
Reader 1
Reader 2
```

are connected to the same Aurora cluster volume.

If Reader 2 is removed:

```text
Reader 2 removed
      ↓
Cluster storage remains
      ↓
Data remains intact
```

Similarly, adding a reader does not require creating a complete new physical copy of the database. citeturn0search0

---

# 7. Why Compute/Storage Separation Matters

Benefits include:

- Fast scaling of compute
- Shared data
- Less storage duplication
- High availability
- Fast replica creation
- Independent compute scaling
- Distributed storage
- Automatic storage growth

### SAP-C02 principle

> Aurora's shared storage architecture is one of the main reasons Aurora Replicas can scale reads efficiently.

---

# 8. Writer Instance

The writer is the primary DB instance responsible for write operations.

Concept:

```text
Application
    |
    v
Writer
    |
    v
Aurora Storage
```

Applications normally connect to the cluster's writer endpoint when they need write access.

---

# 9. Aurora Replicas

Aurora Replicas are DB instances connected to the same underlying cluster storage.

They can serve read workloads.

Concept:

```text
                  Writer
                    |
              Shared Storage
             /       |                 Reader   Reader   Reader
```

Aurora supports up to **15 Aurora Replicas** for read scaling. citeturn0search1

---

# 10. Read Scaling

For read-heavy applications:

```text
Application
     |
     v
Reader Endpoint
     |
 ┌───┼────┬────┐
 ↓   ↓    ↓    ↓
R1   R2   R3   R4
```

The reader endpoint can distribute connections across available Aurora Replicas.

This avoids requiring the application to maintain a list of individual readers.

---

# 11. Reader Endpoint

Use the reader endpoint when the application needs read access.

Concept:

```text
Application
     |
Reader Endpoint
     |
 ├── Reader 1
 ├── Reader 2
 └── Reader 3
```

When replicas are added or removed, the endpoint abstracts the individual instances.

### SAP-C02 clue

> "Automatically distribute read traffic across Aurora read replicas."

Think:

**Aurora reader endpoint.**

---

# 12. Writer Endpoint

The writer endpoint points to the current writer.

Concept:

```text
Application
     |
Writer Endpoint
     |
Current Writer
```

During failover, Aurora can promote a reader to become the new writer, and the writer endpoint follows the new primary.

---

# 13. Aurora Failover

If the writer fails:

```text
Writer Failure
      ↓
Aurora selects/promotes replica
      ↓
New Writer
      ↓
Writer Endpoint
      ↓
Application reconnects
```

Aurora is designed to automate failover to an available Aurora Replica when one exists. citeturn0search1

---

# 14. Multi-AZ Aurora

Aurora storage already spans multiple AZs.

For compute high availability, place Aurora Replicas in different AZs.

Example:

```text
AZ-A
  Writer
    |
    | Shared Storage
    |
AZ-B
  Reader

AZ-C
  Reader
```

If the writer instance fails, another instance can become the writer.

---

# 15. HA vs DR

Important SAP-C02 distinction:

```text
Multi-AZ Aurora
→ Regional high availability

Aurora Global Database
→ Cross-Region global reads + regional DR
```

Do not use Multi-AZ as a replacement for cross-Region disaster recovery.

---

# 16. Aurora Global Database

Aurora Global Database extends an Aurora database across multiple AWS Regions.

Architecture:

```text
              Primary Region
             ┌───────────────┐
             │ Aurora Writer │
             │ + Readers     │
             └───────┬───────┘
                     |
              Global Replication
                     |
        ┌────────────┴────────────┐
        ↓                         ↓
 Secondary Region A        Secondary Region B
     Read-only                  Read-only
```

Aurora Global Database uses storage-based replication and is designed for low-latency global reads and cross-Region disaster recovery. citeturn0search2turn0search8

---

# 17. Global Database Primary Region

The primary Region contains the writable Aurora cluster.

```text
Primary Region
     ↓
Writes
```

Secondary Regions are primarily read-only.

Current AWS documentation supports a primary Region plus up to **10 secondary Regions** for Aurora Global Database. citeturn0search8

> **Exam note:** Older AWS training material may mention fewer secondary Regions. For current AWS behavior, use the current documentation.

---

# 18. Global Database Secondary Regions

Secondary clusters provide:

- Local reads
- Geographic distribution
- Regional disaster recovery
- Lower read latency for global users

Concept:

```text
Users in India
      ↓
India/nearby secondary
      ↓
Local read

Users in Europe
      ↓
Europe secondary
      ↓
Local read
```

Writes still go to the primary cluster unless a supported write-forwarding configuration is used.

---

# 19. Global Database Replication

Aurora Global Database uses storage-layer replication rather than normal database-engine replication.

Concept:

```text
Primary Storage
      ↓
Dedicated replication infrastructure
      ↓
Secondary Storage
```

This reduces replication overhead and supports low replication latency. AWS states latency is typically under one second. citeturn0search8

---

# 20. Global Database Asynchronous Replication

Cross-Region replication is asynchronous.

Therefore:

```text
Primary Write
     ↓
Primary commits
     ↓
Secondary receives replication
```

The secondary may briefly lag behind the primary.

### SAP-C02 implication

Global Database is excellent for:

- Global reads
- Regional DR
- Low-latency geographic access

But it is not equivalent to synchronous zero-data-loss replication across Regions.

---

# 21. Global Database Write Forwarding

Aurora Global Database can support write forwarding from a secondary cluster to the primary.

Concept:

```text
Application
    ↓
Secondary Region
    ↓
Write Forwarding
    ↓
Primary Region
    ↓
Write
```

This can simplify application access patterns for certain workloads.

### Exam caution

Write forwarding does not mean the secondary has become independently writable.

The primary remains the authoritative write Region.

---

# 22. Global Database Switchover vs Failover

Current Aurora Global Database supports distinct mechanisms.

### Switchover

For planned operations:

```text
Healthy Primary
      ↓
Planned Region change
      ↓
New Primary
```

### Failover

For an outage:

```text
Primary Region failure
      ↓
Failover
      ↓
Secondary becomes primary
```

AWS documentation distinguishes planned switchovers from failovers used to recover from outages. citeturn0search8

---

# 23. Global Database for DR

If the requirement is:

> Protect an Aurora database from a complete regional outage.

Think:

**Aurora Global Database.**

Architecture:

```text
Region A
Primary
   |
   | Global replication
   |
Region B
Secondary
```

---

# 24. Aurora I/O-Optimized

Aurora has storage configuration options including:

- Aurora Standard
- Aurora I/O-Optimized

Aurora I/O-Optimized is designed for I/O-intensive workloads and does not charge separately for read/write I/O operations. citeturn0search0turn0search1

---

# 25. Aurora Standard vs I/O-Optimized

| Feature | Aurora Standard | Aurora I/O-Optimized |
|---|---|---|
| Instance charges | Yes | Yes |
| Storage charges | Yes | Yes |
| Read/write I/O charges | Yes | No |
| Best for | Low/moderate I/O | I/O-intensive |
| Price predictability | Lower | Higher |
| I/O-heavy workload | May cost more | Often better |

AWS recommends considering I/O-Optimized when I/O spending is a significant portion of Aurora database spending; current AWS guidance uses **25% or more** as a key threshold. citeturn0search0turn0search3

---

# 26. I/O-Optimized Exam Scenario

Question:

> An Aurora workload is highly I/O intensive and I/O charges represent a significant percentage of total database costs. What should the architect evaluate?

Answer direction:

**Aurora I/O-Optimized.**

Do not simply increase the DB instance size.

---

# 27. Aurora Storage Auto-Scaling

Aurora storage automatically grows with application data.

You do not need to pre-provision a fixed storage volume in the same way as traditional databases.

Concept:

```text
Data Growth
    ↓
Aurora Storage Growth
```

This simplifies capacity management. citeturn0search1

---

# 28. Storage vs Compute Scaling

Aurora separates the two.

### Storage

```text
Data grows
↓
Storage grows automatically
```

### Compute

```text
Performance requirement increases
↓
Scale DB instance / add readers
```

This is a major architectural difference.

---

# 29. Aurora Serverless v2

Aurora Serverless provides automatically scaling database capacity.

Serverless v2 can be used alongside provisioned instances in Aurora clusters. citeturn0search1turn0search3

Useful when:

- Demand varies
- Capacity needs change frequently
- Fine-grained scaling is valuable
- The application does not justify fixed compute capacity

---

# 30. Serverless vs Provisioned Aurora

| Requirement | Consider |
|---|---|
| Predictable steady workload | Provisioned |
| Highly variable workload | Serverless v2 |
| Fine-grained capacity scaling | Serverless v2 |
| Stable high utilization | Provisioned may be more economical |
| Need Aurora cluster features | Both may be appropriate |

### SAP-C02 rule

Do not choose Serverless simply because it sounds cheaper.

Evaluate:

- Traffic pattern
- Minimum capacity
- Scaling behavior
- Cost
- Performance
- Availability requirements

---

# 31. Aurora Read Scaling vs Vertical Scaling

### Vertical scaling

Increase instance size:

```text
db.r6g.large
      ↓
db.r6g.2xlarge
```

Useful when one instance needs more:

- CPU
- Memory
- Network capacity

### Read scaling

Add Aurora Replicas:

```text
Reader 1
Reader 2
Reader 3
```

Useful for read-heavy workloads.

---

# 32. Read-Heavy Workload

If:

```text
Reads = 95%
Writes = 5%
```

Adding readers can improve read throughput.

Architecture:

```text
Writer
  |
Shared Storage
 /   |   R1  R2   R3
```

Use the reader endpoint for read traffic.

---

# 33. Write-Heavy Workload

Adding read replicas does not automatically increase write capacity.

If the bottleneck is:

```text
Writes
```

consider:

- Instance scaling
- Query optimization
- Schema/index optimization
- Connection management
- Application architecture
- Workload partitioning where appropriate

### Exam trap

```text
More readers
≠
More write capacity
```

---

# 34. RDS Proxy

RDS Proxy can sit between applications and Aurora.

```text
Application
     ↓
RDS Proxy
     ↓
Aurora
```

Benefits can include:

- Connection pooling
- Reduced connection overhead
- Better database connection management
- Improved failover behavior

AWS documentation notes that RDS Proxy can help applications handle Aurora failovers more efficiently. citeturn0search1turn0search9

---

# 35. Connection Management

A common scaling problem is too many database connections.

Example:

```text
10,000 application connections
        ↓
Database
        ↓
Connection pressure
```

RDS Proxy can pool and reuse connections.

Concept:

```text
10,000 clients
      ↓
   RDS Proxy
      ↓
Controlled DB connections
      ↓
Aurora
```

---

# 36. Aurora Backups

Aurora provides automated backup capabilities.

Important concepts:

- Continuous backup
- Point-in-time recovery
- Automated backup retention
- Snapshot-based recovery

Use backups for:

- Data recovery
- Accidental deletion
- Historical restoration
- Disaster recovery support

---

# 37. Point-in-Time Recovery

PITR allows restoring an Aurora cluster to a point in time within the supported backup retention period.

Example:

```text
10:00 → Good
10:30 → Good
11:00 → Accidental deletion
11:30 → Disaster discovered

Restore
   ↓
10:55
```

This is different from simply creating a new read replica.

---

# 38. Aurora Backtrack

Where supported, Aurora Backtrack can move a database back to an earlier point without restoring from a traditional backup.

Useful for:

- Accidental changes
- Developer mistakes
- Incorrect data modifications

### Exam caution

Do not assume Backtrack is available in every Aurora architecture or engine/version combination.

---

# 39. Aurora Clone

Aurora cloning can create a copy of a cluster using copy-on-write behavior.

Concept:

```text
Original Cluster
      |
      +---- Clone
```

Initially, data does not need to be physically copied in full.

This can be useful for:

- Testing
- Development
- Data analysis
- Temporary environments

AWS describes Aurora cloning as a fast, low-I/O operation using copy-on-write. citeturn0search4

---

# 40. Aurora and Applications

Common architecture:

```text
Users
  ↓
Route 53
  ↓
CloudFront / ALB
  ↓
Application
  ↓
RDS Proxy
  ↓
Aurora
```

The application tier should use the appropriate:

- Writer endpoint
- Reader endpoint
- Proxy
- Connection pooling strategy

---

# 41. Writer/Reader Endpoint Pattern

A common architecture:

```text
                Application
                /                       /                    Writes          Reads
             |              |
             v              v
      Writer Endpoint   Reader Endpoint
             |              |
             v              v
          Writer         Replicas
             \              /
              \            /
               Shared Storage
```

This separates read and write traffic.

---

# 42. Aurora Integration Patterns

Aurora can integrate with many AWS services and application components.

Examples:

- Amazon VPC
- IAM
- AWS KMS
- Amazon CloudWatch
- AWS Secrets Manager
- AWS Lambda
- Amazon ECS
- Amazon EKS
- Elastic Beanstalk
- Amazon RDS Proxy
- AWS DMS
- AWS Backup
- Route 53

---

# 43. Secrets Manager

Do not hard-code database passwords in application source code.

Better:

```text
Application
     ↓
Secrets Manager
     ↓
Database credentials
```

Secrets can be rotated according to the architecture.

---

# 44. KMS Encryption

Aurora supports encryption at rest using AWS KMS.

Think:

```text
Aurora Storage
     ↓
KMS encryption
```

Also consider:

- TLS in transit
- IAM policies
- Security groups
- Private subnets

---

# 45. VPC Design

Aurora runs within a VPC.

A production architecture typically uses private subnets.

Example:

```text
Internet
   ↓
ALB
   ↓
Private App Subnets
   ↓
Private DB Subnets
   ↓
Aurora
```

Do not expose Aurora directly to the public internet when private connectivity is sufficient.

---

# 46. Security Groups

A common pattern:

```text
ALB SG
  ↓
App SG
  ↓
Aurora SG
```

The Aurora security group should permit database traffic only from the required application security group(s).

---

# 47. Monitoring

Use Amazon CloudWatch and Aurora monitoring capabilities to observe:

- CPU
- Memory-related metrics
- Database connections
- Latency
- Throughput
- I/O
- Replica lag
- Storage
- Performance

---

# 48. Performance Insights

Performance analysis should identify the real bottleneck.

Possible bottlenecks:

```text
CPU
I/O
Locks
Connections
Queries
Memory
Network
```

Do not automatically scale hardware without understanding the workload.

---

# 49. Aurora I/O Architecture

Aurora's storage layer is optimized around a distributed log-based storage architecture rather than treating storage like a conventional independently attached disk for each instance.

Conceptually:

```text
Database Engine
      ↓
Log / Storage Layer
      ↓
Distributed Aurora Storage
```

This allows Aurora to optimize durability and I/O behavior.

---

# 50. Quorum-Based Storage

Aurora's storage system uses quorum-based mechanisms for I/O.

The architecture does not require every storage copy to respond before proceeding.

The exact quorum behavior is an implementation detail, but the SAP-C02 concept is:

```text
Distributed copies
+
Quorum
=
High durability + availability
```

AWS describes Aurora as using quorum techniques in its distributed storage architecture. citeturn0search4turn0search11

---

# 51. Aurora Storage Durability

Aurora's storage replication is independent of the number of DB instances.

This means:

```text
1 Writer
```

can still have highly durable distributed storage.

Adding:

```text
3 Readers
```

does not create the primary durability mechanism.

### Exam trap

Do not confuse:

```text
Reader replicas
```

with:

```text
Storage replicas
```

---

# 52. Aurora Failover Design

Best practice:

```text
AZ-A
Writer

AZ-B
Reader

AZ-C
Reader
```

If the writer fails:

```text
AZ-A failure
      ↓
Reader promoted
      ↓
New writer
```

This provides high availability.

---

# 53. Aurora Global Database + DR

For regional disaster recovery:

```text
Region A
Primary Aurora
      |
      | Global replication
      v
Region B
Secondary Aurora
```

Benefits:

- Cross-Region recovery
- Low-latency local reads
- Geographic resilience

AWS describes Global Database as a mechanism for global reads and recovery from Region-wide outages. citeturn0search8

---

# 54. Global Database vs Read Replica

### Aurora Replica

```text
Same Region
+
Shared storage
+
Read scaling
+
HA
```

### Global Database

```text
Multiple Regions
+
Separate regional clusters
+
Global reads
+
Cross-Region DR
```

### Memorize

```text
Replica → Regional read scaling

Global Database → Global reads + Regional DR
```

---

# 55. Aurora vs RDS MySQL/PostgreSQL

| Requirement | Consider |
|---|---|
| Standard managed MySQL/PostgreSQL | RDS |
| Aurora-compatible workload | Aurora |
| High availability + shared distributed storage | Aurora |
| Very high read scaling | Aurora |
| Global database capability | Aurora |
| Need broad RDS engine choices | RDS |
| Cost-sensitive low/moderate workload | Compare both |

Do not assume Aurora is always cheaper.

---

# 56. Aurora Decision Matrix

| Requirement | Aurora Feature |
|---|---|
| High availability | Multi-AZ Aurora Replicas |
| Read scaling | Aurora Replicas |
| Read endpoint management | Reader endpoint |
| Write connection | Writer endpoint |
| Cross-Region DR | Global Database |
| Global low-latency reads | Global Database |
| Variable compute demand | Serverless v2 |
| I/O-intensive cost | I/O-Optimized |
| Connection pooling | RDS Proxy |
| Historical recovery | Backups / PITR |
| Fast temporary copy | Aurora Clone |
| Encryption at rest | KMS |
| Secret management | Secrets Manager |
| Metrics | CloudWatch |

---

# 57. SAP-C02 Scenario Mapping

| Scenario | Likely Answer Direction |
|---|---|
| Managed MySQL/PostgreSQL with high availability | Aurora |
| Separate compute and storage | Aurora |
| Scale reads horizontally | Aurora Replicas |
| Automatically distribute read traffic | Reader endpoint |
| Automatic writer failover | Aurora + reader replicas |
| Regional disaster recovery | Aurora Global Database |
| Global users need local reads | Global Database |
| Highly I/O-intensive workload | Aurora I/O-Optimized |
| Variable database capacity | Aurora Serverless v2 |
| Too many application DB connections | RDS Proxy |
| Accidental data deletion | PITR / Backtrack where appropriate |
| Fast database copy for testing | Aurora Clone |
| Database encryption | KMS |
| Store DB credentials securely | Secrets Manager |
| Private database access | VPC private subnets |
| Monitor database performance | CloudWatch / Performance Insights |
| More read throughput | Add Aurora Replicas |
| More write capacity | Scale writer / optimize workload |
| Cross-Region read-only copies | Global Database |

---

# 58. Common SAP-C02 Exam Traps

## Trap 1 — Aurora Replicas provide the storage copies

Wrong.

Aurora's storage layer independently maintains distributed copies.

---

## Trap 2 — Adding readers increases write capacity

Wrong.

Readers primarily increase read capacity.

---

## Trap 3 — Multi-AZ solves regional disaster recovery

Wrong.

Use Aurora Global Database for cross-Region DR.

---

## Trap 4 — Global Database is synchronous

Wrong.

Cross-Region replication is asynchronous.

---

## Trap 5 — Global secondary clusters are independent writers

Wrong.

The primary Region is the authoritative write Region.

---

## Trap 6 — I/O-Optimized is always cheaper

Wrong.

It is intended for workloads where its pricing model provides better economics, especially I/O-intensive workloads.

---

## Trap 7 — Bigger DB instance solves every performance issue

Wrong.

First determine whether the bottleneck is:

- Query
- I/O
- CPU
- Memory
- Connections
- Read throughput
- Write throughput

---

## Trap 8 — Serverless is always cheaper

Wrong.

Cost depends on utilization and scaling patterns.

---

## Trap 9 — Storage must be manually provisioned

Wrong.

Aurora storage automatically grows with data requirements.

---

## Trap 10 — Aurora Clone copies the entire database immediately

Wrong.

Aurora cloning uses copy-on-write behavior.

---

# 59. Aurora Architecture Decision Tree

```text
Need managed relational database?
          ↓
MySQL / PostgreSQL compatible?
          ↓
Need high performance + HA?
          ↓
Aurora
          |
          +------------------+
          |                  |
       Read-heavy         Global users
          |                  |
          ↓                  ↓
 Aurora Replicas       Global Database
          |
          ↓
 Reader Endpoint
```

---

# 60. Performance Decision Tree

```text
Performance problem
        ↓
Identify bottleneck
        |
  ┌─────┼───────────┬───────────┐
  ↓     ↓           ↓           ↓
 Reads Writes      I/O       Connections
  ↓     ↓           ↓           ↓
Add   Scale      I/O-       RDS Proxy
Readers Writer    Optimized
  ↓     ↓
Query/index optimization
```

---

# 61. HA/DR Decision Tree

```text
Need availability?
       ↓
Same Region?
       |
      YES
       ↓
Aurora Replicas
       ↓
Different AZs

Need Region-level DR?
       ↓
Aurora Global Database
       ↓
Secondary Region(s)
       ↓
Failover when required
```

---

# 62. Cost Optimization Checklist

```text
□ Evaluate Aurora Standard vs I/O-Optimized
□ Measure I/O spending
□ Right-size DB instances
□ Use read replicas for read scaling
□ Avoid unnecessary readers
□ Evaluate Serverless v2 for variable workloads
□ Use connection pooling where appropriate
□ Monitor storage growth
□ Monitor query performance
□ Remove unused clusters/instances
□ Evaluate Database Savings Plans where applicable
□ Monitor cross-Region Global Database costs
```

---

# 63. Performance Checklist

```text
□ Identify CPU bottlenecks
□ Identify I/O bottlenecks
□ Check query performance
□ Check connection count
□ Check replica lag
□ Check reader utilization
□ Check storage behavior
□ Add readers for read-heavy workloads
□ Scale writer for writer bottlenecks
□ Use RDS Proxy for connection pressure
□ Consider I/O-Optimized for I/O-heavy workloads
□ Use Serverless v2 for highly variable capacity needs
```

---

# 64. HA Checklist

```text
□ Writer instance configured
□ Aurora Replicas available
□ Replicas distributed across AZs
□ Reader endpoint used for reads
□ Writer endpoint used for writes
□ Failover tested
□ Application reconnect behavior tested
□ RDS Proxy evaluated if connection management is important
```

---

# 65. Global Database Checklist

```text
□ Primary Region selected
□ Secondary Region(s) selected
□ Global replication configured
□ Local read requirements evaluated
□ Cross-Region latency measured
□ Replication lag monitored
□ DR RTO/RPO defined
□ Switchover procedure documented
□ Failover procedure documented
□ DNS/application routing tested
□ Cross-Region costs evaluated
```

---

# 66. 30-Second Revision

```text
Aurora
  ↓
Compute + Shared Distributed Storage
  ↓
Storage across multiple AZs
  ↓
Writer + Aurora Replicas
  ↓
Reader Endpoint → Read Scaling
Writer Endpoint → Writes
  ↓
Failover → Replica becomes Writer
  ↓
Global Database → Cross-Region
  ↓
I/O-Optimized → I/O-heavy
  ↓
Serverless v2 → Variable capacity
  ↓
RDS Proxy → Connection pooling
  ↓
PITR → Historical recovery
```

### Memorize

```text
Aurora Replica
→ Same Region read scaling + HA

Global Database
→ Multi-Region reads + DR

I/O-Optimized
→ I/O-intensive workload

Serverless v2
→ Variable compute demand

RDS Proxy
→ Connection management

PITR
→ Restore to earlier point
```

---

# 67. SAP-C02 Golden Rules

1. **Aurora separates compute from storage.**
2. **Aurora DB instances connect to a shared distributed cluster volume.**
3. **Aurora storage is replicated across multiple AZs in a Region.**
4. **Do not confuse storage copies with Aurora DB instances.**
5. **Aurora Replicas primarily provide read scaling and help with HA.**
6. **Aurora supports up to 15 Aurora Replicas for read scaling.**
7. **Use the reader endpoint to distribute read connections across replicas.**
8. **Use the writer endpoint for write traffic.**
9. **If the writer fails, Aurora can promote a suitable reader.**
10. **Adding readers does not automatically increase write capacity.**
11. **Aurora Global Database provides cross-Region read scaling and DR.**
12. **Global Database replication is asynchronous.**
13. **The primary Region remains the main write Region.**
14. **Use Global Database for Region-level disaster recovery rather than relying only on Multi-AZ.**
15. **Aurora I/O-Optimized removes separate read/write I/O charges and is intended for I/O-intensive workloads.**
16. **Do not assume I/O-Optimized is always cheaper; evaluate actual I/O economics.**
17. **Aurora Serverless v2 is useful when compute demand varies.**
18. **Do not assume Serverless is always the cheapest option.**
19. **RDS Proxy helps manage large numbers of application database connections.**
20. **PITR is for restoring to an earlier point in time.**
21. **Aurora cloning uses copy-on-write and is useful for fast database copies.**
22. **Use KMS for encryption at rest and Secrets Manager for database credentials where appropriate.**
23. **Keep Aurora in private subnets unless a public architecture is explicitly required.**
24. **Use security groups to restrict database access to required application tiers.**
25. **For global applications, place read workloads near users using Global Database secondary Regions.**
26. **Always identify the actual bottleneck before scaling the database.**
27. **Read scaling and write scaling are different architectural problems.**
28. **For SAP-C02, choose the solution that meets the required performance, availability, RTO/RPO, security, and cost objectives with minimal unnecessary complexity.**

---

# 68. Final SAP-C02 Mental Model

When you see an Aurora question:

```text
Need managed relational DB?
        ↓
MySQL/PostgreSQL?
        ↓
Need Aurora architecture?
        ↓
Compute + Shared Storage
        ↓
What is the problem?
        |
   ┌────┼─────────────┐
   ↓    ↓             ↓
 Reads Writes       Global
   ↓    ↓             ↓
Replicas Writer     Global DB
   ↓    ↓             ↓
Reader   Scale      Multi-Region
Endpoint
        |
        ↓
I/O-heavy?
   ↓
I/O-Optimized

Variable demand?
   ↓
Serverless v2

Too many DB connections?
   ↓
RDS Proxy

Need historical recovery?
   ↓
PITR / Backups

Need fast temporary copy?
   ↓
Aurora Clone
```

## Ultimate Aurora Formula

```text
Aurora
=
Separated Compute + Distributed Storage
+
Multi-AZ Durability
+
Aurora Replicas
+
Automatic Failover
+
Global Database
+
Flexible Compute Scaling
+
I/O Optimization
+
Managed Backups
```

> **SAP-C02 takeaway:** The most important Aurora architectural idea is **compute/storage separation**. Aurora's distributed shared storage provides the foundation for high availability, fast replica creation, and scalable database architecture. Use **Aurora Replicas for regional read scaling and HA**, **Aurora Global Database for cross-Region reads and DR**, **I/O-Optimized for I/O-intensive economics**, **Serverless v2 for variable capacity**, and **RDS Proxy for connection management**. Always match the feature to the actual business requirement rather than choosing the most advanced feature automatically.
