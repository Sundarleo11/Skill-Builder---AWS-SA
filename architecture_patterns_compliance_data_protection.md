# Architecture Patterns - Compliance and Data Protection

## Course Overview

**Level:** Advanced  
**Duration:** 30 minutes  
**Language:** English  
**Rating:** 4.8/5 (41 reviews)  
**Source:** AWS Technical Field Communities (TFC)

> This README is a study-oriented summary of the course topic. It is not a replacement for the official AWS course material.

## 1. Introduction

Compliance and data protection are critical considerations when designing and deploying generative AI solutions. The appropriate controls and responsibilities depend heavily on the **scope of the use case**.

AWS uses a **Generative AI Security Scoping Matrix** to help organizations understand how security, compliance, and data protection responsibilities change across different solution architectures.

The key idea is:

> As you move between different generative AI scopes, your level of control, responsibility, and required safeguards changes.

---

## 2. Generative AI Security Scoping Matrix

The scoping matrix provides a framework for thinking about security and compliance responsibilities based on how a generative AI capability is consumed.

When evaluating a solution, consider:

- Who owns the underlying AI infrastructure?
- Who manages the model?
- Where is customer data processed?
- What data is sent to external services?
- What security controls are available?
- What compliance obligations apply?
- Who is responsible for protecting the data?

### Why scope matters

Different architectures provide different levels of control.

For example:

- A managed AI service may provide significant operational convenience.
- A customized or self-managed solution may provide greater control.
- More control generally means more operational responsibility.
- More abstraction can reduce operational burden but may limit direct control.

---

## 3. Compliance Responsibilities

Compliance involves ensuring that a solution meets applicable laws, regulations, standards, policies, and contractual requirements.

When designing generative AI systems, evaluate:

### Regulatory requirements

Determine which regulations and industry requirements apply to the workload.

Examples may include:

- Data protection and privacy regulations
- Industry-specific compliance requirements
- Data residency requirements
- Records retention requirements
- Security standards
- Contractual obligations

### Governance

Organizations should establish governance processes covering:

- Acceptable AI use
- Data classification
- Access control
- Model and application risk
- Monitoring and auditing
- Incident response
- Retention and deletion
- Third-party risk management

---

## 4. Data Protection

Data protection focuses on preventing unauthorized access, disclosure, alteration, loss, or misuse of information.

For generative AI workloads, pay particular attention to:

- Personally identifiable information (PII)
- Confidential business information
- Customer data
- Intellectual property
- Authentication credentials and secrets
- Regulated information

### Data minimization

Only provide the AI system with the data required to perform the task.

Benefits include:

- Reduced exposure
- Lower privacy risk
- Smaller potential impact of a security incident
- Easier compliance management

### Data classification

Classify information before sending it to a generative AI application.

A typical classification approach could include:

1. Public
2. Internal
3. Confidential
4. Highly sensitive / regulated

The exact classification scheme should match organizational policy.

---

## 5. Privacy-Preserving Design

Privacy should be considered during architecture design rather than added after deployment.

Useful design principles include:

### Minimize

Collect and process only the information necessary for the intended purpose.

### Protect

Use appropriate encryption, access controls, identity management, and security monitoring.

### Isolate

Separate sensitive workloads and data where appropriate.

### Control access

Apply least-privilege access so users and applications receive only the permissions they need.

### Monitor

Log and monitor relevant activity to identify suspicious behavior and support auditing.

### Retain appropriately

Define retention periods and delete information when it is no longer required, subject to legal and business requirements.

---

## 6. Buy vs. Build

A major architectural decision is whether to consume an existing managed capability or build more of the solution yourself.

### Buy

Using a managed generative AI capability generally provides:

- Faster implementation
- Reduced infrastructure management
- Greater abstraction
- Lower operational overhead

However, abstraction can mean less direct control over underlying components.

### Build

Building or managing more of the solution can provide:

- Greater architectural control
- More customization
- More control over data flows
- Greater ability to implement specialized requirements

However, it also introduces additional responsibility for:

- Infrastructure
- Security controls
- Configuration
- Monitoring
- Patching and maintenance
- Compliance implementation

### Key trade-off

**Buy vs. build is fundamentally a trade-off between control and convenience.**

Managed services can reduce operational burden, while more self-managed architectures can provide greater control at the cost of increased responsibility.

---

## 7. Shared Responsibility

Compliance and security responsibilities are not automatically transferred to a cloud provider.

A useful way to think about responsibility is:

**Cloud provider responsibilities**
- Security of the underlying cloud infrastructure
- Service-level security capabilities
- Certain managed-service controls

**Customer responsibilities**
- Data classification
- Identity and access management configuration
- Application security
- Appropriate data handling
- Compliance requirements specific to the workload
- Correct service configuration
- Governance and organizational policies

The exact division of responsibility depends on the AWS service and architecture being used.

---

## 8. Architecture Review Questions

Before deploying a generative AI workload, ask:

### Data

- What data will the application process?
- Is the data sensitive or regulated?
- Is all data necessary?
- Where does the data originate?
- Where is it stored and processed?
- How long should it be retained?

### Access

- Who can access the application?
- Who can access the underlying data?
- Are least-privilege permissions being used?
- Are privileged operations monitored?

### Compliance

- Which regulations apply?
- Are there data residency requirements?
- Are audit records required?
- Are retention and deletion requirements defined?
- Has the solution been reviewed by the appropriate compliance stakeholders?

### Architecture

- Is a managed service appropriate?
- Would additional control justify a more customized architecture?
- Which security responsibilities remain with the customer?
- Are third-party components involved?

---

## 9. Practical Design Principles

Use the following principles when designing compliant generative AI systems:

1. **Understand the scope of the workload.**
2. **Classify data before processing it.**
3. **Minimize sensitive information sent to AI systems.**
4. **Apply least-privilege access controls.**
5. **Encrypt data in transit and at rest where appropriate.**
6. **Define retention and deletion policies.**
7. **Monitor and audit important activity.**
8. **Understand the shared responsibility model.**
9. **Evaluate regulatory and contractual requirements early.**
10. **Balance control and convenience when choosing between buy and build.**
11. **Use privacy-preserving design principles from the beginning.**
12. **Regularly review the architecture as the AI workload evolves.**

---

## 10. Quick Revision Summary

| Topic | Key Point |
|---|---|
| Security Scoping Matrix | Helps identify security and compliance responsibilities based on solution scope |
| Compliance | Align the workload with applicable laws, regulations, standards, and policies |
| Data Protection | Protect sensitive information throughout its lifecycle |
| Privacy | Minimize data collection and processing and apply appropriate safeguards |
| Governance | Establish policies, ownership, monitoring, and review processes |
| Buy vs. Build | Trade convenience and abstraction against control and responsibility |
| Shared Responsibility | Security and compliance responsibilities are distributed between provider and customer |
| Data Minimization | Process only the information necessary for the intended purpose |
| Least Privilege | Give users and applications only the access they require |

---

## 11. Key Takeaway

The central lesson is that **compliance and data protection requirements depend on the scope and architecture of the generative AI solution**.

As organizations move between managed, customized, and self-managed approaches, the balance between **control, convenience, and responsibility** changes.

A strong architecture therefore starts by understanding:

**What data is being processed → where it goes → who controls it → which regulations apply → what security controls are required → who is responsible for each control.**

This approach helps teams design generative AI solutions that are secure, privacy-aware, governable, and aligned with compliance requirements.

---

## Disclaimer

This document is a learning aid based on the provided course description. Always consult the current official AWS documentation, service-specific documentation, organizational policies, and applicable laws or regulations when making production architecture and compliance decisions.
