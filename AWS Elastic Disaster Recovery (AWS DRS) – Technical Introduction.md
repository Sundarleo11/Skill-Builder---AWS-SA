# AWS Elastic Disaster Recovery (AWS DRS) – Technical Introduction

## 1. Overview

**AWS Elastic Disaster Recovery (AWS DRS)** helps you recover on-premises and cloud-based applications by replicating servers to AWS.

The main goal is to minimize:

- **Downtime**
- **Data loss**
- **Recovery time**
- **Operational complexity**

AWS DRS provides a disaster recovery solution where workloads can be replicated continuously to AWS and recovered when a disaster or disruption occurs.

---

## 2. Key Concepts

### Source Server

The **source server** is the server running the workload that needs protection.

Examples:

- On-premises physical server
- Virtual machine
- Cloud-based server

The source server's data is continuously replicated to AWS.

### Staging Area

AWS DRS uses a **staging area subnet** in AWS to receive replicated data.

The staging environment is designed to provide a cost-effective location for maintaining replicated data until recovery is required.

### Recovery Instance

During a recovery operation, AWS DRS launches recovery instances in AWS.

These instances can be used to run the application after a disaster.

---

## 3. Continuous Data Replication

AWS DRS continuously replicates data from the source environment to AWS.

The replication process helps maintain a current copy of the workload in AWS.

### Benefits

- Reduces potential data loss
- Keeps recovery data up to date
- Supports fast recovery
- Allows testing without affecting production

---

## 4. Recovery

When a disaster occurs, you can initiate recovery of the protected servers.

The recovery process can include:

1. Select the source servers.
2. Choose the appropriate recovery configuration.
3. Launch recovery instances.
4. Verify application functionality.
5. Redirect users or traffic to the recovered environment.

---

## 5. Failover

**Failover** means moving the workload from the primary environment to the AWS recovery environment.

Failover can be used when:

- The primary environment becomes unavailable.
- A major infrastructure failure occurs.
- A disaster affects the production environment.

---

## 6. Failback

**Failback** means returning the workload from AWS back to the original environment after the original environment has been restored.

Typical flow:

**Primary → AWS Recovery → Primary**

1. Production environment fails.
2. Recover workload in AWS.
3. Run the application from AWS.
4. Restore the original environment.
5. Replicate changes back.
6. Perform failback.

---

## 7. Recovery Point Objective (RPO)

**RPO** describes how much data loss an organization can tolerate.

Example:

If the RPO is **5 minutes**, the organization expects that at most approximately 5 minutes of data could potentially be lost after a disaster.

### Remember

**RPO = How much data can I afford to lose?**

---

## 8. Recovery Time Objective (RTO)

**RTO** describes how quickly an application needs to be restored after a disruption.

Example:

If the RTO is **30 minutes**, the application should be recovered within approximately 30 minutes.

### Remember

**RTO = How quickly must I recover?**

---

## 9. Disaster Recovery Testing

AWS DRS supports testing recovery procedures.

Testing is important because organizations need to verify that:

- Recovery configurations work correctly.
- Applications can start successfully.
- Dependencies are available.
- Recovery procedures are understood.
- Recovery objectives can be achieved.

Regular testing helps identify problems before an actual disaster occurs.

---

## 10. Recovery Strategy

A disaster recovery architecture should consider:

- Application dependencies
- Network configuration
- Security groups
- IAM permissions
- Storage
- DNS
- Database dependencies
- Application startup order
- Recovery objectives

For applications with multiple servers, recovery should account for the relationship between those servers.

---

## 11. High-Level Architecture

```text
             Production Environment
                     |
                     |
             Source Servers
                     |
                     | Continuous Replication
                     v
              AWS DRS Service
                     |
                     v
              Staging Area
                     |
                     |
              Disaster Occurs
                     |
                     v
             Recovery Process
                     |
                     v
            Recovery Instances
                     |
                     v
              Application Runs
                   in AWS
```

---

## 12. Failover and Failback Flow

```text
Normal Operation

Production
    |
    | Continuous Replication
    v
AWS Staging Area


Disaster

Production
    X
    |
    v
AWS Recovery Instances
    |
    v
Application Available


After Production Recovery

AWS Recovery
    |
    | Replication / synchronization
    v
Production
    |
    v
Failback Complete
```

---

## 13. Important Exam Points

For the **AWS Solutions Architect Professional (SAP-C02)** exam, remember:

| Concept | Meaning |
|---|---|
| RPO | Maximum acceptable data loss |
| RTO | Maximum acceptable recovery time |
| Source server | Original workload |
| Staging area | AWS environment receiving replicated data |
| Recovery instance | Instance used to run the recovered workload |
| Failover | Move workload to AWS |
| Failback | Return workload to original environment |
| Continuous replication | Keeps recovery data synchronized |
| DR testing | Validates recovery procedures |

---

## 14. Quick Memory Trick

### RPO

**P = Point**

> "How far back can I go?"

➡️ Data loss

### RTO

**T = Time**

> "How long can I take?"

➡️ Recovery time

---

## 15. SAP-C02 Scenario Thinking

When you see a question asking for:

### Minimum data loss

Think:

**RPO → Continuous replication**

### Fast application recovery

Think:

**RTO → Recovery instances / recovery process**

### Primary environment is unavailable

Think:

**Failover → AWS**

### Original environment is restored

Think:

**Failback → Original environment**

### Verify DR before disaster

Think:

**Recovery testing**

---

## 16. Course Reference

The uploaded AWS Skill Builder learning plan lists:

**AWS Elastic Disaster Recovery – A Technical Introduction**

- Level: Intermediate
- Duration: 1 hour 30 minutes

The course description states that it covers AWS Elastic Disaster Recovery, including setup, disaster readiness, failover to AWS, and failback to the source environment.