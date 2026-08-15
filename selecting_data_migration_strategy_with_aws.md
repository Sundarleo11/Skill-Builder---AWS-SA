# Selecting Your Data Migration Strategy with AWS

## Course Overview

**Level:** Fundamental  
**Duration:** 30 minutes  
**Language:** English  
**Rating:** 4.6/5 (496 reviews)

> This README is a study-oriented guide based on the course description provided. It is intended for learning and revision and does not replace current AWS documentation.

---

# 1. Introduction

Migrating storage and data to the AWS Cloud requires more than simply copying files from one location to another.

The right migration strategy depends on factors such as:

- Type of data
- Amount of data
- Data access patterns
- Source environment
- Target AWS service
- Migration timeline
- Network connectivity
- Downtime requirements
- Performance requirements
- Security and compliance requirements
- Cost
- Operational complexity

A successful migration starts with understanding the organization's data and selecting the AWS service or combination of services that best matches the workload.

---

# 2. Why Data Migration Strategy Matters

Different types of data have different requirements.

For example:

- File-based data may be best suited for file storage.
- Object data may be migrated to object storage.
- Block storage workloads may require a different migration approach.
- Large datasets may require specialized transfer services.
- Frequently changing data may require continuous synchronization.

Using the wrong migration method can result in:

- Excessive downtime
- Poor performance
- Higher costs
- Data inconsistency
- Complex operations
- Difficult cutover procedures

The objective is to select a migration strategy that balances **speed, cost, performance, reliability, security, and operational effort**.

---

# 3. Understand the Data First

Before selecting a migration service, understand the source data.

Important questions include:

### What type of data is it?

Examples:

- Files
- Objects
- Databases
- Block storage
- Archives
- Backups
- Logs

### How much data exists?

Consider:

- Gigabytes
- Terabytes
- Petabytes

### How quickly does the data change?

Determine:

- Static data
- Occasionally changing data
- Frequently changing data
- Continuously changing data

### How is the data accessed?

Consider:

- File system access
- Object/API access
- Block-level access
- Database queries

---

# 4. Data Classification

Data classification helps determine appropriate migration and storage controls.

A simple classification model might be:

```text
Public
  |
Internal
  |
Confidential
  |
Highly Sensitive / Regulated
```

Classification can influence:

- Encryption
- Access controls
- Storage location
- Data transfer method
- Retention
- Monitoring
- Compliance requirements

Always use the organization's actual data-classification policy in production environments.

---

# 5. Common Data Types

## File Data

File data is organized into files and directories.

Examples:

- Documents
- Shared folders
- User home directories
- Application files
- Media files

Typical requirements include:

- File and directory semantics
- Shared access
- Permissions
- File locking
- Existing application compatibility

---

## Object Data

Object storage organizes data as objects rather than traditional files and directories.

Examples:

- Images
- Videos
- Backups
- Logs
- Data lakes
- Static website content

Object storage is highly scalable and well suited for large collections of unstructured data.

---

## Block Data

Block storage provides block-level storage volumes to compute systems.

Common characteristics include:

- Low-latency access
- Operating-system disks
- Application storage
- Database storage

Block workloads often require a migration approach that preserves the required storage characteristics.

---

## Database Data

Database migration has requirements beyond simple file copying.

Consider:

- Database engine
- Transaction consistency
- Change rate
- Downtime tolerance
- Replication
- Schema compatibility
- Application dependencies

Database-specific migration approaches may therefore be required.

---

# 6. AWS Storage Services

AWS provides several storage services for different workload requirements.

## Amazon S3

Amazon S3 is an object storage service.

Typical use cases include:

- Backups
- Archives
- Data lakes
- Media
- Logs
- Static content
- Large unstructured datasets

Conceptually:

```text
Applications
     |
     v
Amazon S3
     |
     +--> Objects
     +--> Buckets
     +--> Storage Classes
```

---

## Amazon EBS

Amazon Elastic Block Store provides block storage volumes for Amazon EC2.

Typical use cases include:

- Operating-system volumes
- Application volumes
- Database storage
- Low-latency block storage

---

## Amazon EFS

Amazon Elastic File System provides managed file storage.

It is useful when applications require shared file-system access.

Examples include:

- Shared application files
- Content management
- Linux workloads
- Shared directories

---

## Amazon FSx

Amazon FSx provides managed file systems for specific workload requirements.

Different FSx options support different file-system technologies and use cases.

Examples include workloads requiring:

- Windows file systems
- High-performance file systems
- Specialized enterprise file-system capabilities

Always check current AWS documentation for supported features and service options.

---

# 7. AWS Data Migration Services

AWS provides several services and tools to help move data.

The appropriate choice depends on the type and characteristics of the migration.

Important services include:

- AWS DataSync
- AWS Snow Family
- AWS Storage Gateway
- AWS Database Migration Service
- AWS Transfer Family
- Amazon S3
- AWS Application Migration Service for server migration scenarios

---

# 8. AWS DataSync

**AWS DataSync** is designed to simplify and accelerate data transfers between storage systems and AWS storage services.

It can be useful for:

- File-system migrations
- Online data transfer
- Synchronization
- Data movement between supported storage locations

A simplified flow:

```text
Source Storage
      |
      v
AWS DataSync
      |
      v
AWS Storage
```

DataSync is particularly useful when network connectivity is available and the workload benefits from managed, automated transfer.

---

# 9. AWS Snow Family

The AWS Snow Family provides physical devices that can help transfer large amounts of data when network-based transfer is impractical.

Conceptually:

```text
On-Premises Data
      |
      v
Snow Device
      |
      | Physical Transport
      v
AWS
```

Physical transfer can be useful when:

- Data volumes are very large
- Network bandwidth is limited
- Network transfer would take too long
- Data must be transferred using a physical device

The exact Snow Family device and current capabilities should be selected based on the latest AWS offerings.

---

# 10. AWS Storage Gateway

AWS Storage Gateway provides hybrid storage capabilities that connect on-premises environments with AWS storage.

It can help organizations:

- Extend storage into AWS
- Maintain hybrid workflows
- Support backup and archive use cases
- Provide cloud-backed storage

This can be useful as part of a gradual migration strategy rather than a one-time bulk migration.

---

# 11. AWS Database Migration Service

**AWS Database Migration Service (AWS DMS)** is designed for database migration and replication.

It can help move data between supported database environments while minimizing downtime for many migration scenarios.

A simplified workflow:

```text
Source Database
       |
       v
AWS DMS
       |
       v
Target Database
```

Database migrations should consider:

- Source and target engines
- Schema conversion requirements
- Change data capture
- Replication
- Transaction consistency
- Application compatibility
- Cutover strategy

---

# 12. AWS Transfer Family

AWS Transfer Family provides managed file transfer capabilities for supported protocols.

It can be useful when organizations need to move files using existing transfer workflows and protocols.

Typical scenarios include:

- SFTP-based transfers
- FTP-related workflows
- Business-to-business file exchange
- Moving files into AWS storage

---

# 13. Online vs. Offline Migration

A major strategy decision is whether to use online or offline transfer.

## Online Migration

Data moves over a network.

```text
Source
  |
  | Network
  v
AWS
```

### Advantages

- No physical device shipping
- Can support ongoing synchronization
- Useful for incremental migration
- Convenient when network bandwidth is sufficient

### Considerations

- Network bandwidth
- Transfer duration
- Network costs
- Connectivity reliability
- Security controls

---

## Offline Migration

Data is copied to a physical device and transported to AWS.

```text
Source
  |
  v
Physical Device
  |
  | Transport
  v
AWS
```

### Advantages

- Useful for very large datasets
- Reduces dependency on network bandwidth
- Can be faster than network transfer in some situations

### Considerations

- Device logistics
- Shipping
- Security
- Scheduling
- Physical handling

---

# 14. Choosing Online or Offline Transfer

Consider the following:

```text
                    Data Volume
                        |
                        v
              Is network transfer
                  practical?
                  /                       Yes        No
                 |          |
                 v          v
              Online      Offline
```

Also evaluate:

- Available bandwidth
- Required migration window
- Data change rate
- Security requirements
- Cost
- Geographic location

---

# 15. Data Transfer Time

A simple conceptual estimate is:

```text
Transfer Time ≈ Data Size / Effective Throughput
```

For example, a 10 TB dataset transferred at a sustained effective rate of 1 Gbps will require substantially more time than the same dataset transferred at 10 Gbps.

Real-world transfer time is affected by:

- Network overhead
- Protocol efficiency
- Latency
- Parallelism
- Source performance
- Destination performance
- Encryption
- Compression
- Network contention

Therefore, estimates should use realistic sustained throughput rather than theoretical link speed.

---

# 16. Data Change Rate

The rate at which data changes is important.

### Low change rate

A one-time bulk transfer may be sufficient.

### High change rate

Continuous synchronization or replication may be more appropriate.

Example:

```text
Initial Transfer
      |
      v
Continuous Changes
      |
      v
Incremental Sync
      |
      v
Final Cutover
```

This approach can reduce downtime and minimize the amount of data that must be transferred during the final migration window.

---

# 17. Downtime Requirements

Migration strategies should be aligned with business downtime requirements.

### High downtime tolerance

A simpler migration approach may be sufficient.

### Low downtime tolerance

Consider:

- Continuous replication
- Incremental synchronization
- Parallel environments
- Testing before cutover
- Planned final synchronization

The more stringent the downtime requirement, the more carefully the migration process must be designed.

---

# 18. Migration Strategies

Several broad strategies can be used when moving workloads and data to AWS.

## Rehost

Move the workload with minimal changes.

Often called:

> Lift and shift

### Benefits

- Faster migration
- Fewer application changes
- Lower initial migration complexity

### Considerations

- May not take full advantage of cloud-native capabilities
- Existing architecture may carry forward technical debt

---

## Replatform

Move the workload while making limited modifications to take advantage of managed AWS services.

### Benefits

- Can reduce operational effort
- May improve scalability
- Can modernize selected components

### Considerations

- More work than rehosting
- Requires application analysis

---

## Refactor / Re-architect

Redesign the application to use cloud-native architecture.

### Benefits

- Greater scalability potential
- Modern architecture
- Potentially improved resilience
- Better use of managed services

### Considerations

- Highest migration effort
- Greater application changes
- Longer project timeline

---

## Retain

Keep the workload in its current environment.

This may be appropriate when:

- Migration is not currently justified
- Dependencies prevent migration
- Business requirements have not changed
- The workload needs further assessment

---

## Retire

Remove workloads that are no longer required.

Retirement can reduce:

- Infrastructure costs
- Operational overhead
- Security exposure
- Unnecessary migration work

---

# 19. The 6 Rs

A common framework for application migration decisions is the **6 Rs**:

| Strategy | Description |
|---|---|
| Rehost | Move with minimal changes |
| Replatform | Make limited changes to use cloud capabilities |
| Repurchase | Replace with a different product or service |
| Refactor | Redesign the application |
| Retire | Remove applications that are no longer needed |
| Retain | Keep applications where they are for now |

The appropriate strategy can differ between applications, even within the same organization.

---

# 20. Storage Migration Decision Framework

Use this sequence:

```text
1. Identify the data
        |
        v
2. Classify the data
        |
        v
3. Understand access patterns
        |
        v
4. Measure data volume
        |
        v
5. Measure change rate
        |
        v
6. Define downtime requirements
        |
        v
7. Evaluate network connectivity
        |
        v
8. Select target AWS storage
        |
        v
9. Select migration service
        |
        v
10. Test and validate
        |
        v
11. Perform migration
        |
        v
12. Validate and optimize
```

---

# 21. Migration Planning

Before moving data, create an inventory.

Capture information such as:

| Attribute | Example |
|---|---|
| Data Type | Files |
| Size | 20 TB |
| Change Rate | High |
| Access Pattern | Shared file system |
| Source | On-premises NAS |
| Target | AWS file storage |
| Downtime | Minimal |
| Compliance | Sensitive |
| Migration Method | Online synchronization |

This makes migration decisions more systematic.

---

# 22. Security Considerations

Data migration must preserve the security posture of the workload.

Consider:

- Encryption in transit
- Encryption at rest
- IAM permissions
- Network security
- Data classification
- Access controls
- Logging
- Monitoring
- Credential protection
- Compliance requirements

Do not assume that data is safe simply because it is being migrated to AWS.

---

# 23. Data Integrity

A migration is successful only when the destination contains the correct data.

Validation may include:

- File counts
- File sizes
- Checksums
- Database consistency
- Application-level validation
- Permission validation
- Metadata verification

Example:

```text
Source Data
    |
    v
Migration
    |
    v
Target Data
    |
    v
Integrity Validation
    |
    +--> Match --> Continue
    |
    +--> Mismatch --> Investigate
```

---

# 24. Testing

Always test migration procedures before moving critical workloads.

Testing should verify:

- Data integrity
- Application functionality
- Permissions
- Performance
- Network connectivity
- Backup and recovery
- Monitoring
- Cutover procedures
- Rollback procedures

A successful test migration reduces production risk.

---

# 25. Cutover Planning

The cutover is the transition from the source environment to AWS.

A typical process is:

```text
Prepare
  |
  v
Initial Migration
  |
  v
Continuous / Incremental Sync
  |
  v
Freeze or Quiesce Source
  |
  v
Final Synchronization
  |
  v
Validate Target
  |
  v
Redirect Users / Applications
  |
  v
Production on AWS
```

The exact process depends on the workload.

---

# 26. Rollback Planning

A migration should have a documented rollback strategy.

Consider:

- What conditions trigger rollback?
- Can the source remain available?
- How will data changes be handled?
- How long can the business tolerate disruption?
- Who has authority to initiate rollback?
- How will users be redirected?

Rollback planning is especially important for business-critical workloads.

---

# 27. Cost Considerations

Migration costs may include:

- AWS storage
- Compute resources
- Data transfer
- Migration services
- Temporary staging resources
- Network connectivity
- Physical transfer devices
- Monitoring
- Testing environments

Compare the total cost of each migration strategy rather than evaluating only the migration service price.

---

# 28. Common Migration Mistakes

## Mistake 1: Choosing a service before understanding the data

Start with the workload and data requirements.

## Mistake 2: Ignoring data change rate

Frequently changing data may require synchronization rather than a one-time copy.

## Mistake 3: Ignoring downtime

A migration method that takes several days may be unsuitable for a workload requiring minutes of downtime.

## Mistake 4: Ignoring dependencies

Applications may depend on databases, file systems, DNS, authentication, or external services.

## Mistake 5: Skipping validation

Data should be verified after migration.

## Mistake 6: Ignoring security

Data must remain appropriately protected during transfer and after migration.

## Mistake 7: No rollback plan

Production migration should have clearly defined failure and recovery procedures.

---

# 29. Best Practices

1. **Inventory the data before migration.**
2. **Classify data according to organizational policy.**
3. **Understand data access patterns.**
4. **Measure data volume and change rate.**
5. **Define downtime requirements.**
6. **Evaluate available network bandwidth.**
7. **Select the appropriate AWS target storage service.**
8. **Choose the migration service based on workload requirements.**
9. **Use encryption and appropriate access controls.**
10. **Test migration procedures before production.**
11. **Validate data integrity after migration.**
12. **Plan cutover and rollback procedures.**
13. **Migrate in manageable waves when appropriate.**
14. **Monitor the migration process.**
15. **Remove temporary resources when they are no longer needed.**

---

# 30. Data Migration Checklist

## Discovery

- [ ] Inventory data sources.
- [ ] Identify data owners.
- [ ] Identify data types.
- [ ] Measure data volume.
- [ ] Measure data change rate.
- [ ] Document access patterns.
- [ ] Identify application dependencies.

## Strategy

- [ ] Determine migration objective.
- [ ] Evaluate the 6 Rs where applicable.
- [ ] Define downtime requirements.
- [ ] Evaluate online vs. offline migration.
- [ ] Select target AWS storage.
- [ ] Select migration tooling.

## Security

- [ ] Classify sensitive data.
- [ ] Plan encryption in transit.
- [ ] Plan encryption at rest.
- [ ] Define IAM permissions.
- [ ] Review network security.
- [ ] Review compliance requirements.

## Migration

- [ ] Perform a test migration.
- [ ] Validate data integrity.
- [ ] Monitor transfer progress.
- [ ] Perform incremental synchronization if required.
- [ ] Prepare cutover.
- [ ] Execute final synchronization.
- [ ] Redirect production workloads.

## Post-Migration

- [ ] Validate applications.
- [ ] Validate data.
- [ ] Validate permissions.
- [ ] Validate monitoring.
- [ ] Validate backups.
- [ ] Document the new environment.
- [ ] Remove unnecessary migration resources.
- [ ] Review migration results.

---

# 31. Knowledge Check Questions

### Question 1

What should you determine before selecting a migration service?

**Answer:** Understand the data type, volume, change rate, access patterns, source environment, target requirements, downtime requirements, security needs, and connectivity.

### Question 2

When is AWS DataSync useful?

**Answer:** It is useful for managed online data transfers and synchronization between supported storage locations.

### Question 3

When might the AWS Snow Family be useful?

**Answer:** When very large datasets need to be transferred and network-based transfer is impractical or too slow.

### Question 4

What is the difference between online and offline migration?

**Answer:** Online migration transfers data over a network, while offline migration uses physical devices to transport data.

### Question 5

Why does data change rate matter?

**Answer:** Frequently changing data may require continuous or incremental synchronization to maintain consistency and reduce downtime.

### Question 6

What is rehosting?

**Answer:** Moving a workload to AWS with minimal changes, commonly called lift and shift.

### Question 7

What is replatforming?

**Answer:** Moving a workload while making limited changes to take advantage of cloud capabilities.

### Question 8

What is refactoring?

**Answer:** Redesigning or significantly modifying an application to use a cloud-native architecture.

### Question 9

Why is data validation important?

**Answer:** It confirms that the migrated data is complete, accurate, and usable.

### Question 10

Why is rollback planning important?

**Answer:** It provides a controlled recovery path if the production migration does not meet requirements.

---

# 32. Quick Revision Summary

| Topic | Key Point |
|---|---|
| Data Migration Strategy | Select based on data and business requirements |
| Data Type | Determines appropriate storage and migration approach |
| Data Volume | Influences transfer method and duration |
| Change Rate | Determines whether continuous synchronization is needed |
| Downtime | Influences cutover and replication strategy |
| AWS DataSync | Managed online data transfer and synchronization |
| AWS Snow Family | Physical data transfer for large datasets |
| Storage Gateway | Supports hybrid storage scenarios |
| AWS DMS | Database migration and replication |
| Transfer Family | Managed file-transfer workflows |
| S3 | Object storage |
| EBS | Block storage |
| EFS | Managed file storage |
| FSx | Managed specialized file systems |
| Rehost | Move with minimal changes |
| Replatform | Move with limited optimization |
| Refactor | Redesign for cloud-native architecture |
| Retain | Keep workload in its current environment |
| Retire | Remove workloads that are no longer needed |
| Data Integrity | Validate data after migration |
| Cutover | Transition production operation to AWS |
| Rollback | Recovery strategy if migration fails |

---

# 33. Final Takeaway

There is no single migration strategy that works for every workload.

The best approach begins with understanding:

**What data do we have? → How much data is there? → How quickly does it change? → How is it accessed? → How much downtime is acceptable? → What AWS storage should hold it? → Which migration service best fits the requirements?**

A successful AWS data migration balances:

**Data characteristics + Business requirements + Migration method + Security + Cost + Operational complexity**

The most important principle is:

> **Choose the migration strategy based on the workload, not simply on the migration tool.**

By properly assessing data types, volume, change rate, access patterns, connectivity, downtime, security, and business requirements, organizations can select an AWS migration approach that is efficient, secure, reliable, and appropriate for their workloads.

---

## Disclaimer

This document is a learning aid based on the course description supplied by the user. It is not official AWS training material. AWS service capabilities, supported features, pricing, terminology, and recommended practices can change. Always consult the latest AWS documentation before planning or executing a production data migration.
