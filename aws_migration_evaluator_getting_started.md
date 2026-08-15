# AWS Migration Evaluator Getting Started

## Course Overview

**Level:** Fundamental  
**Duration:** 45 minutes  
**Language:** English  
**Rating:** 4.6/5 (473 reviews)

> This README is a study-oriented guide based on the course description provided. It is intended for learning and revision and does not replace current AWS documentation.

---

# 1. Introduction

**AWS Migration Evaluator** helps organizations assess their existing infrastructure and build a data-driven business case for moving workloads to AWS.

It is designed to analyze on-premises environments and help estimate AWS costs for migrated workloads.

A simplified process is:

```text
Existing Environment
        |
        v
Data Collection
        |
        v
Workload Analysis
        |
        v
AWS Cost Modeling
        |
        v
Right-Sizing / Best-Fit Analysis
        |
        v
Migration Business Case
```

The goal is to replace assumptions with measured workload information and estimated AWS costs.

---

# 2. What Migration Evaluator Helps With

Migration Evaluator can help organizations:

- Assess existing infrastructure
- Collect workload information
- Analyze compute requirements
- Analyze memory requirements
- Analyze storage requirements
- Consider Microsoft licensing information
- Estimate AWS costs
- Identify right-sized AWS configurations
- Compare current infrastructure with AWS scenarios
- Build a migration business case
- Support migration planning and decision-making

---

# 3. Why Cost Modeling Matters

Migration decisions should not be based only on current hardware specifications.

For example, an existing server may have:

- More CPU capacity than it actually uses
- More memory than required
- Excess storage capacity
- Different utilization patterns throughout the day

Simply replacing an existing physical server with an equivalently sized AWS instance may not be cost efficient.

A better approach is:

```text
Current Infrastructure
        |
        v
Measure Actual Utilization
        |
        v
Understand Requirements
        |
        v
Select Appropriate AWS Resources
        |
        v
Estimate Cost
```

---

# 4. Existing Infrastructure Assessment

Migration Evaluator focuses on understanding workloads in the current environment.

Important data can include:

- CPU utilization
- Memory utilization
- Storage requirements
- Storage performance
- Server configuration
- Operating system
- Microsoft licensing information
- Workload characteristics

The quality of the resulting cost model depends on the quality and completeness of the collected data.

---

# 5. Compute Analysis

Compute assessment looks beyond the physical server's maximum capacity.

Important questions include:

- How many CPUs are allocated?
- How much CPU is actually being used?
- What are peak utilization levels?
- What are average utilization levels?
- How much compute capacity does the workload really require?

Conceptually:

```text
Provisioned CPU
      |
      v
Actual Utilization
      |
      v
Required Capacity
      |
      v
AWS Instance Recommendation
```

This can help identify over-provisioned workloads.

---

# 6. Memory Analysis

Memory is another important sizing factor.

A server may have significantly more memory installed than its workload requires.

Migration analysis should consider:

- Installed memory
- Used memory
- Peak memory
- Memory utilization patterns
- Application requirements

Right-sizing should ensure that the target environment provides enough capacity without unnecessarily paying for unused resources.

---

# 7. Storage Analysis

Storage requirements include more than capacity.

Consider:

- Total storage
- Used storage
- Growth
- Storage performance
- Access patterns
- Read/write requirements
- Backup requirements

A workload with 5 TB of storage does not necessarily require the same storage architecture as another 5 TB workload.

---

# 8. Microsoft Licensing

Microsoft licensing can significantly influence migration economics.

Examples include:

- Windows Server
- SQL Server
- Other Microsoft workloads

When evaluating AWS costs, licensing considerations should be included where applicable.

Licensing options and eligibility can vary, so organizations should verify current AWS and Microsoft licensing requirements before making production decisions.

---

# 9. Data Collection

Migration Evaluator uses information about the existing environment to build an analysis.

A conceptual workflow:

```text
Environment
    |
    v
Data Collection
    |
    v
Inventory + Utilization
    |
    v
Analysis
    |
    v
AWS Cost Model
```

The collected data should represent actual workload behavior over an appropriate observation period.

---

# 10. Importance of Utilization Data

A server's configuration does not tell the complete story.

Example:

```text
Server A
16 vCPUs allocated
CPU average utilization = low
CPU peak utilization = moderate
```

Another server may have the same hardware but very different utilization.

Therefore:

> **Measure workload behavior instead of sizing entirely from provisioned capacity.**

This is a key principle of cost modeling and right-sizing.

---

# 11. Baseline

A baseline describes the current environment before migration.

A useful baseline can include:

- Number of servers
- CPU allocation
- Memory
- Storage
- Utilization
- Operating systems
- Licensing
- Current infrastructure cost
- Workload criticality

The baseline provides a reference for comparing AWS scenarios.

---

# 12. AWS Cost Modeling

Migration Evaluator can produce optimized cost-modeling scenarios.

A simplified model:

```text
Current Workload
       |
       v
Measured Requirements
       |
       v
AWS Resource Options
       |
       v
Optimization
       |
       v
Estimated AWS Cost
```

The model can help organizations understand potential AWS infrastructure costs before committing to migration.

---

# 13. Right-Sizing

**Right-sizing** means selecting resources that are appropriate for the workload rather than simply matching the existing infrastructure configuration.

Example:

```text
Current Server
32 CPUs
128 GB RAM
       |
       v
Measured Usage
Low CPU / Moderate Memory
       |
       v
Potentially Smaller AWS Configuration
```

Right-sizing can reduce unnecessary cloud spending.

However, right-sizing must not remove capacity required for:

- Peak workloads
- Business growth
- Failover
- Performance
- Availability
- Application requirements

---

# 14. Best-Fit Placement

Migration Evaluator can algorithmically help identify potential best-fit AWS placements for workloads.

A simplified process:

```text
Workload Requirements
        |
        v
AWS Resource Candidates
        |
        v
Constraint Analysis
        |
        v
Cost Optimization
        |
        v
Best-Fit Placement
```

The selected placement should still be reviewed by architects and application owners.

---

# 15. Cost Model vs. Final AWS Bill

A cost model is an estimate, not a guarantee of the final AWS bill.

Actual AWS costs depend on:

- Resource usage
- AWS Region
- Pricing model
- Discounts
- Savings Plans
- Reserved capacity where applicable
- Data transfer
- Storage usage
- Monitoring
- Backup
- Network services
- Architecture changes

Therefore:

> Use Migration Evaluator to inform decisions, not as a substitute for detailed production cost validation.

---

# 16. Migration Business Case

A business case explains why migration makes sense from a financial and operational perspective.

It may compare:

```text
Current Environment
       |
       +----> Infrastructure Cost
       +----> Operations
       +----> Hardware Refresh
       +----> Data Center Costs
       |
       v
AWS Scenario
       |
       +----> AWS Compute
       +----> Storage
       +----> Networking
       +----> Licensing
       +----> Operations
```

The business case should consider more than raw compute pricing.

---

# 17. Total Cost Considerations

A complete migration analysis may consider:

### Current Environment

- Hardware
- Data center
- Power
- Cooling
- Maintenance
- Software licensing
- Operations
- Hardware refresh cycles

### AWS Environment

- Compute
- Storage
- Network
- Licensing
- Backup
- Monitoring
- Security services
- Support
- Data transfer

The actual comparison should use the organization's specific financial assumptions.

---

# 18. Migration Evaluator Workflow

A typical assessment workflow can be represented as:

```text
1. Define Assessment Scope
        |
        v
2. Collect Environment Data
        |
        v
3. Validate Data Quality
        |
        v
4. Analyze Workloads
        |
        v
5. Build AWS Scenarios
        |
        v
6. Review Right-Sizing
        |
        v
7. Compare Costs
        |
        v
8. Build Business Case
        |
        v
9. Use Results for Migration Planning
```

---

# 19. Assessment Scope

Before starting, define what should be assessed.

Possible scope includes:

- Entire data center
- Selected application groups
- Specific business units
- Test environments
- Production environments
- Specific server clusters

Clearly defining scope helps prevent incomplete or misleading conclusions.

---

# 20. Data Quality

Cost models are only as useful as the underlying data.

Check:

- Is the inventory complete?
- Is utilization data available?
- Does the observation period represent normal workloads?
- Are peak periods included?
- Are inactive servers identified?
- Are duplicate systems present?
- Is storage data accurate?
- Is licensing information accurate?

Poor data can result in poor recommendations.

---

# 21. Observation Period

Workload utilization changes over time.

For example:

```text
Monday
  |
  |---- Normal utilization

Month End
  |
  |---- Higher utilization

Quarter End
  |
  |---- Peak workload
```

An assessment should capture representative workload behavior.

If only a short or unusual period is observed, the resulting model may not accurately represent normal or peak requirements.

---

# 22. Application Grouping

Servers should not always be evaluated independently.

Applications often contain multiple components.

Example:

```text
Web Tier
   |
   v
Application Tier
   |
   v
Database Tier
   |
   v
Storage
```

Migration planning should identify these relationships.

Grouping workloads can help with:

- Migration waves
- Dependency planning
- Cost analysis
- Architecture design
- Business impact analysis

---

# 23. Dependencies

Before migrating, identify dependencies such as:

- Databases
- File systems
- DNS
- Active Directory
- Authentication
- Network services
- External APIs
- Monitoring
- Backup systems
- Licensing servers

A server that appears inexpensive to migrate may still depend on other systems that significantly affect the migration plan.

---

# 24. Migration Groups

Workloads can be grouped based on:

- Application
- Business unit
- Dependency
- Criticality
- Environment
- Migration complexity

Example:

```text
Wave 1
  Low-risk applications

Wave 2
  Internal business applications

Wave 3
  Customer-facing workloads

Wave 4
  Business-critical systems
```

Cost modeling can help inform these decisions, but technical dependencies must also be considered.

---

# 25. Security and Governance

Cost analysis should not be separated from security.

Consider:

- IAM
- Encryption
- Network controls
- Logging
- Monitoring
- Compliance
- Data classification
- Access requirements

A cheaper architecture is not automatically the right architecture if it fails security or compliance requirements.

---

# 26. Common Migration Evaluator Use Cases

## Use Case 1: Data Center Exit

An organization wants to understand the financial impact of moving its infrastructure to AWS.

Migration Evaluator can help establish a baseline and model AWS scenarios.

---

## Use Case 2: Hardware Refresh Decision

An organization needs to decide whether to purchase new hardware or migrate to AWS.

A cost model can help compare potential scenarios.

---

## Use Case 3: Cloud Business Case

Leadership wants a financial justification for cloud migration.

Migration analysis can provide workload-level information that supports the business case.

---

## Use Case 4: Right-Sizing

An organization suspects that existing servers are over-provisioned.

Utilization analysis can identify opportunities for more appropriate AWS resource sizing.

---

## Use Case 5: Large Portfolio Assessment

An organization has hundreds or thousands of workloads.

An assessment can help organize the environment and create cost-modeling scenarios at scale.

---

# 27. Common Mistakes

## Mistake 1: Using hardware specifications as the only sizing input

A server's installed capacity may be much higher than actual workload requirements.

## Mistake 2: Using an unrepresentative observation period

Peak and normal workloads may be missed.

## Mistake 3: Ignoring licensing

Microsoft licensing can materially affect migration economics.

## Mistake 4: Ignoring dependencies

Cost modeling does not eliminate application architecture analysis.

## Mistake 5: Treating estimates as guarantees

AWS costs vary based on actual usage and architecture.

## Mistake 6: Optimizing cost at the expense of requirements

The cheapest option may not satisfy performance, resilience, or compliance requirements.

## Mistake 7: Ignoring growth

A workload that is correctly sized today may need additional capacity in the future.

---

# 28. Best Practices

1. **Define the assessment scope clearly.**
2. **Collect representative workload data.**
3. **Measure utilization rather than relying only on provisioned capacity.**
4. **Include CPU, memory, storage, and licensing considerations.**
5. **Validate the quality of collected data.**
6. **Identify application dependencies.**
7. **Use right-sizing as an optimization, not an excuse to under-provision.**
8. **Consider growth and peak demand.**
9. **Compare realistic AWS scenarios.**
10. **Include operational and supporting costs.**
11. **Review security and compliance requirements.**
12. **Use results as input to a broader migration strategy.**
13. **Validate important assumptions before production migration.**
14. **Update cost models as requirements change.**

---

# 29. Cost Modeling Checklist

## Assessment

- [ ] Define assessment scope.
- [ ] Inventory servers.
- [ ] Collect CPU data.
- [ ] Collect memory data.
- [ ] Collect storage data.
- [ ] Collect operating-system information.
- [ ] Identify Microsoft licensing.
- [ ] Identify application dependencies.

## Data Quality

- [ ] Confirm inventory completeness.
- [ ] Check utilization data.
- [ ] Include representative periods.
- [ ] Identify peak workloads.
- [ ] Remove duplicate or irrelevant records.
- [ ] Validate storage information.

## AWS Modeling

- [ ] Identify target AWS Region.
- [ ] Evaluate right-sizing.
- [ ] Consider workload requirements.
- [ ] Consider availability requirements.
- [ ] Consider storage requirements.
- [ ] Consider licensing.
- [ ] Consider networking.
- [ ] Estimate supporting service costs.

## Business Case

- [ ] Establish current-state baseline.
- [ ] Model AWS scenarios.
- [ ] Compare costs.
- [ ] Document assumptions.
- [ ] Identify risks.
- [ ] Identify potential savings.
- [ ] Identify migration priorities.

## Decision

- [ ] Review results with technical teams.
- [ ] Review results with finance.
- [ ] Review results with business stakeholders.
- [ ] Validate critical assumptions.
- [ ] Use results to build migration waves.
- [ ] Update the model when architecture changes.

---

# 30. Knowledge Check Questions

### Question 1

What is the primary purpose of AWS Migration Evaluator?

**Answer:** To assess existing workloads and help create optimized AWS cost-modeling scenarios that support cloud migration decisions and business cases.

### Question 2

Why is utilization data important?

**Answer:** It helps determine actual workload requirements rather than sizing AWS resources only from provisioned hardware capacity.

### Question 3

What infrastructure characteristics are relevant to the assessment?

**Answer:** Compute, memory, storage, workload utilization, operating-system information, and relevant licensing information.

### Question 4

What is right-sizing?

**Answer:** Selecting resources that are appropriately sized for actual workload requirements rather than simply matching existing infrastructure.

### Question 5

Why should peak utilization be considered?

**Answer:** Average utilization alone may hide periods when workloads require significantly more capacity.

### Question 6

Why does Microsoft licensing matter?

**Answer:** Licensing costs and eligibility can materially affect the financial comparison between the current environment and AWS.

### Question 7

Does a Migration Evaluator cost model guarantee the final AWS bill?

**Answer:** No. It is an estimate based on assumptions, measured workloads, pricing, and modeled architecture.

### Question 8

Why are application dependencies important?

**Answer:** Workloads often depend on databases, authentication, DNS, storage, networking, and other systems that affect migration planning.

### Question 9

What should be included in a migration business case?

**Answer:** Current-state costs, AWS modeled costs, assumptions, potential savings, risks, migration priorities, and relevant operational considerations.

### Question 10

Why should assessment data be representative?

**Answer:** Workload behavior changes over time, and an unrepresentative observation period can produce inaccurate sizing and cost estimates.

---

# 31. Quick Revision Summary

| Concept | Key Point |
|---|---|
| Migration Evaluator | Helps assess workloads and build AWS cost models |
| Assessment | Analysis of the current infrastructure |
| CPU | Helps determine compute requirements |
| Memory | Helps determine memory requirements |
| Storage | Helps determine capacity and storage needs |
| Utilization | Shows actual workload behavior |
| Right-Sizing | Selecting appropriate resource capacity |
| Best-Fit Placement | Matching workloads to suitable AWS resources |
| Microsoft Licensing | Important component of migration economics |
| Baseline | Current-state reference for comparison |
| Cost Model | Estimated AWS scenario based on available data |
| Business Case | Financial and operational justification for migration |
| Dependencies | Systems and services required by workloads |
| Observation Period | Time window used to capture workload behavior |
| Migration Waves | Grouped workloads migrated in controlled stages |

---

# 32. Final Takeaway

AWS Migration Evaluator helps organizations move from assumptions to evidence when planning cloud migration.

The core process is:

```text
Inventory
   ↓
Measure
   ↓
Analyze
   ↓
Right-Size
   ↓
Model AWS Costs
   ↓
Build Business Case
   ↓
Plan Migration
```

The most important principles are:

1. **Measure actual workload behavior.**
2. **Do not size AWS resources solely from existing hardware.**
3. **Include CPU, memory, storage, and licensing considerations.**
4. **Capture representative utilization data.**
5. **Consider peak workloads and future growth.**
6. **Account for application dependencies.**
7. **Treat cost models as estimates rather than guarantees.**
8. **Balance cost optimization with performance, availability, security, and compliance.**
9. **Use the results to prioritize and plan migration waves.**
10. **Validate important assumptions before production migration.**

The ultimate goal is to answer:

> **What will our workloads require in AWS, what could that architecture cost, and how can we build a defensible business case for migration?**

Migration Evaluator can provide valuable input to that decision by combining workload information, utilization analysis, and AWS cost modeling.

---

## Disclaimer

This document is a learning aid based on the course description supplied by the user. It is not official AWS training material. AWS service capabilities, pricing, licensing rules, terminology, and recommended practices can change. Always consult the latest AWS documentation, AWS pricing information, and applicable software licensing terms before making production migration or financial decisions.
