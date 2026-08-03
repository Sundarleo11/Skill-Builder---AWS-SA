# Multi-Account Resource Inventory and Visualization

## Overview

This course explains how to build a centralized inventory and visualization solution for AWS resources across multiple AWS accounts. It leverages AWS Config, CloudFormation, AWS Systems Manager, Amazon Athena, and Amazon QuickSight to collect, query, and visualize resource data.

---

## Learning Objectives

After completing this course, I can:

- Understand enterprise resource visibility challenges in multi-account AWS environments.
- Validate and optimize AWS Config architecture.
- Automate deployment using AWS CloudFormation.
- Configure AWS Systems Manager Automation.
- Build Amazon QuickSight analyses and dashboards.
- Configure dashboard refresh strategies.
- Query AWS Config data using Amazon Athena.
- Optimize SQL queries for better performance.

---

# Module 1 – Multi-Account Resource Inventory Foundations

## Key Topics

### Enterprise Visibility Challenges

- Resources spread across multiple AWS accounts.
- Difficult to maintain centralized inventory.
- Manual reporting is time-consuming.
- Compliance and governance become challenging.

### AWS Config

AWS Config records:

- AWS resource inventory
- Configuration history
- Configuration changes
- Compliance status

Benefits:

- Continuous monitoring
- Compliance auditing
- Resource tracking
- Change history

---

# Module 2 – CloudFormation Automation

## CloudFormation

Infrastructure as Code (IaC) service used to deploy AWS resources automatically.

Benefits:

- Repeatable deployments
- Version control
- Consistent environments
- Reduced manual effort

### Systems Manager Automation

Automates operational tasks.

Examples:

- Configuration updates
- Deployment automation
- Resource management
- Maintenance workflows

---

# Module 3 – Amazon QuickSight Dashboards

## Amazon QuickSight

Business Intelligence (BI) service for visualizing AWS Config data.

Capabilities:

- Interactive dashboards
- Charts
- KPIs
- Resource inventory reports
- Compliance reports

### Dashboard Sharing

- Share dashboards with users
- Schedule automatic refresh
- Keep visualizations updated

---

# Module 4 – Amazon Athena

## Amazon Athena

Serverless SQL query service for querying data stored in Amazon S3.

Uses:

- Query AWS Config snapshots
- Generate inventory reports
- Analyze compliance
- Create business reports

Example SQL

```sql
SELECT resourceType,
COUNT(*)
FROM config_table
GROUP BY resourceType;
```

### Performance Best Practices

- Partition data
- Compress files
- Query only required columns
- Avoid SELECT *
- Use filtered queries
- Optimize data formats (Parquet/ORC)

---

# AWS Services Covered

- AWS Organizations
- AWS Config
- AWS CloudFormation
- AWS Systems Manager
- Amazon S3
- Amazon Athena
- Amazon QuickSight
- IAM

---

# End-to-End Workflow

```
AWS Accounts
      │
      ▼
AWS Config Records Resources
      │
      ▼
Configuration Data Stored in S3
      │
      ▼
Athena Queries Data
      │
      ▼
QuickSight Creates Dashboards
      │
      ▼
Business & Compliance Reports
```

---

# SAP-C02 Exam Tips

✔ AWS Config provides centralized configuration tracking.

✔ CloudFormation automates deployment using Infrastructure as Code.

✔ Systems Manager Automation reduces manual operational tasks.

✔ Athena queries data directly from Amazon S3 using SQL.

✔ QuickSight visualizes inventory and compliance dashboards.

✔ Use Athena + QuickSight for reporting without managing database servers.

---

# Services to Remember

| Service | Purpose |
|----------|---------|
| AWS Config | Resource inventory and compliance |
| CloudFormation | Infrastructure as Code |
| Systems Manager | Automation |
| Amazon Athena | SQL queries on S3 |
| Amazon QuickSight | Dashboards and BI |
| Amazon S3 | Store Config snapshots |

---

# Exam Keywords

- Multi-Account Inventory
- Resource Visibility
- Configuration History
- Compliance Monitoring
- Infrastructure as Code
- Business Intelligence
- Serverless SQL
- Dashboard Sharing
- Athena Optimization
- AWS Config Aggregator

---

## Summary

This course demonstrates how to create a centralized resource inventory solution for multiple AWS accounts by collecting configuration data with AWS Config, deploying infrastructure using CloudFormation, automating setup with Systems Manager, querying data using Athena, and visualizing insights with Amazon QuickSight.