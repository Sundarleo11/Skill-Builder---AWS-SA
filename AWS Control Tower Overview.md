# AWS Control Tower Overview

AWS Control Tower helps organizations set up and govern a secure, multi-account AWS environment that follows AWS best practices. It orchestrates multiple AWS services to create and maintain a well-architected landing zone.

## Core Services Used by AWS Control Tower

- **AWS Organizations**: Manages multiple AWS accounts and centralizes governance using Organizational Units (OUs) and Service Control Policies (SCPs).
- **IAM Identity Center**: Provides federated identity and centralized access management across AWS accounts.
- **AWS Service Catalog**: Enables administrators to create and manage catalogs of approved AWS resources and provisioning templates.
- **Account Factory**: Automates account creation and account lifecycle management for new AWS accounts.
- **AWS Config**: Tracks configuration changes, assesses compliance, and records resource state over time.
- **AWS CloudFormation**: Deploys and manages resources using infrastructure-as-code templates.
- **Amazon CloudWatch**: Collects monitoring and operational metrics, logs, and alarms for ongoing visibility.
- **AWS CloudTrail**: Records account activity and API calls for auditing, compliance, and security analysis.

## Key Advantages

- **Centralized governance**: Control Tower provides a single place to manage security, operations, and compliance across multiple accounts.
- **Automated account provisioning**: Account Factory simplifies and standardizes new account creation.
- **Built-in best practices**: It enforces AWS recommended configurations and guardrails out of the box.
- **Improved compliance**: AWS Config and CloudTrail help ensure resources remain compliant and activity is auditable.
- **Faster onboarding**: New teams and accounts can be provisioned quickly with standardized architecture.
- **Visibility and monitoring**: CloudWatch and CloudTrail provide ongoing visibility into account behavior and operational health.

## Why This Matters

AWS Control Tower makes it easier to manage a multi-account AWS environment while maintaining security, compliance, and operational efficiency. It is especially useful for organizations that need to scale with consistent governance and best-practice controls.

---

*This README summarizes the AWS Control Tower services covered in the lesson and highlights the main advantages of using Control Tower for a landing zone.*