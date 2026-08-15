# Architecture Patterns - Access Control Frameworks

## Course Overview

**Level:** Advanced  
**Duration:** 40 minutes  
**Language:** English  
**Rating:** 4.7/5 (37 reviews)  
**Source:** AWS Technical Field Communities (TFC)

> This README is a study-oriented summary based on the course description provided. It is not a replacement for official AWS documentation or organization-specific security policies.

---

# 1. Introduction

Access control is a fundamental security requirement for generative AI systems.

As generative AI applications become more capable, they may access:

- Enterprise data
- Knowledge bases
- Databases
- APIs
- Cloud resources
- External tools
- Other AI agents
- Autonomous workflows

Authentication and authorization help ensure that only the right identities can access the right resources and perform the right actions.

A robust security architecture combines access control with:

- Data protection
- Logging and monitoring
- Threat detection
- Secure configuration
- Defense in depth
- Continuous review

---

# 2. Authentication vs. Authorization

These concepts are closely related but solve different problems.

## Authentication

**Authentication answers:**

> Who are you?

Authentication verifies the identity of a user, application, service, or agent.

Examples include:

- Username and password
- Multi-factor authentication (MFA)
- Identity providers
- IAM identities
- Federated identities
- Service identities
- Cryptographic credentials

## Authorization

**Authorization answers:**

> What are you allowed to do?

After an identity is established, authorization determines which resources and actions are permitted.

Examples:

- Read a document
- Query a database
- Invoke an AI model
- Call an API
- Execute a tool
- Modify a configuration
- Access a knowledge base

### Simple model

```text
Authentication
      |
      v
Who is the requester?
      |
      v
Authorization
      |
      v
What can the requester access?
      |
      v
Resource / Action
```

---

# 3. Why Access Control Matters for Generative AI

Traditional applications already require strong access controls, but generative AI can introduce additional complexity.

An AI application may dynamically:

- Retrieve information
- Select tools
- Call APIs
- Generate queries
- Invoke other services
- Interact with external systems
- Delegate tasks to other agents

This means access control must protect not only the initial user but also the actions performed by the AI system.

A useful security principle is:

> The model should not be trusted to enforce authorization by itself.

Authorization should be enforced by application and infrastructure controls outside the model.

---

# 4. Identity in Generative AI Systems

A generative AI architecture may involve multiple identities.

For example:

```text
Human User
    |
    v
AI Application
    |
    v
AI Agent
    |
    +------> Knowledge Base
    |
    +------> Database
    |
    +------> Tool / API
    |
    +------> Other Agent
```

Each component may require its own identity and permissions.

Potential identities include:

- Human users
- Applications
- Services
- AI agents
- Automated workflows
- External systems

The architecture should clearly establish which identity is responsible for each action.

---

# 5. Least Privilege

The **principle of least privilege** means giving an identity only the permissions required to perform its intended task.

For example, an AI support agent that only needs to read customer information should not receive permissions to:

- Delete customer records
- Modify billing information
- Change security settings
- Create administrator accounts

### Least-privilege model

```text
Identity
   |
   v
Required Task
   |
   v
Minimum Permissions
   |
   v
Specific Resources
```

Least privilege reduces the potential impact of:

- Compromised accounts
- Prompt injection
- Malicious users
- Misconfigured agents
- Compromised tools
- Application vulnerabilities

---

# 6. Defense in Depth

Access control should not rely on a single security mechanism.

A defense-in-depth approach introduces multiple independent controls.

Example:

```text
+--------------------------------+
| Identity Authentication        |
+--------------------------------+
| Authorization                  |
+--------------------------------+
| Resource Policies              |
+--------------------------------+
| Application-Level Controls     |
+--------------------------------+
| Data-Level Controls            |
+--------------------------------+
| Network Controls               |
+--------------------------------+
| Logging & Monitoring           |
+--------------------------------+
| Detection & Response           |
+--------------------------------+
```

If one control fails, other layers can reduce the impact.

---

# 7. Data Access Control

Generative AI applications often use enterprise data.

Examples:

- Documents
- Customer records
- Databases
- Internal knowledge bases
- Object storage
- Search indexes
- APIs

Access should be limited according to the identity and business purpose.

## Example

A user may have access to:

```text
Department A Documents
```

but not:

```text
Department B Confidential Documents
```

The AI system should respect those permissions when retrieving information.

---

# 8. Authorization in RAG Systems

Retrieval-Augmented Generation (RAG) applications retrieve information from external sources before generating an answer.

A simplified flow is:

```text
User
  |
  v
Application
  |
  v
Authorization Check
  |
  v
Retriever
  |
  v
Permitted Documents
  |
  v
Model
  |
  v
Response
```

A major security risk is retrieving documents that the user should not be able to access.

## Important principle

**Do not assume that because the model can retrieve a document, the user is authorized to see it.**

Authorization should be enforced at the data-access layer.

---

# 9. Authentication for AI Agents

As autonomous AI agents become more common, each agent may need an identifiable security context.

An agent should have:

- A defined identity
- Limited permissions
- Clearly defined responsibilities
- Access boundaries
- Auditable actions
- Appropriate credentials

Agents should not automatically inherit unrestricted permissions from the user or application.

---

# 10. Agent-to-Agent Access

Modern architectures may contain multiple specialized agents.

Example:

```text
Orchestrator Agent
       |
       +----> Research Agent
       |
       +----> Data Agent
       |
       +----> Reporting Agent
       |
       +----> Action Agent
```

Each agent should receive only the permissions required for its role.

For example:

| Agent | Example Permission |
|---|---|
| Research Agent | Read approved knowledge sources |
| Data Agent | Query approved datasets |
| Reporting Agent | Generate reports |
| Action Agent | Execute specifically approved actions |

This reduces the blast radius if one agent is compromised or behaves unexpectedly.

---

# 11. Autonomous Agent Security

Autonomous agents can execute tasks with limited human intervention.

This creates additional access-control requirements.

Consider:

- What actions can the agent perform?
- Which resources can it access?
- How long do its credentials remain valid?
- Can it create additional actions?
- Can it delegate tasks?
- Can it modify its own permissions?
- Which actions require human approval?

High-impact actions should generally receive stronger controls than low-risk actions.

---

# 12. Privilege Boundaries

A privilege boundary defines how far an identity or agent can operate.

Examples:

```text
Agent
 |
 +--> Read data
 |
 +--> Query database
 |
 +--> Call approved API
 |
 X--> Delete production database
```

A strong architecture prevents an AI agent from crossing its intended privilege boundary.

---

# 13. Temporary and Short-Lived Credentials

Long-lived credentials increase risk if they are exposed.

Where supported, prefer temporary credentials or short-lived authorization mechanisms.

Benefits include:

- Reduced exposure window
- Easier credential rotation
- Lower impact from credential leakage
- Better separation between tasks

This is particularly valuable for autonomous agents and dynamic workflows.

---

# 14. Human-in-the-Loop Controls

Not every action should be fully autonomous.

For high-risk operations, consider requiring human approval.

Examples:

- Financial transactions
- Deleting important data
- Changing production configuration
- Sending sensitive communications
- Modifying security policies
- Granting permissions
- Executing irreversible operations

### Example

```text
AI Agent
   |
   v
Proposed Action
   |
   v
Risk Evaluation
   |
   +---- Low Risk ----> Automatic Execution
   |
   +---- High Risk ---> Human Approval
                         |
                         v
                     Execution
```

---

# 15. Role-Based Access Control

**RBAC (Role-Based Access Control)** grants permissions through defined roles.

Example:

```text
User
  |
  v
Support Analyst Role
  |
  +--> Read Support Documents
  +--> Create Support Tickets
  X----> Modify IAM
```

RBAC simplifies administration when users have well-defined responsibilities.

Common roles might include:

- Administrator
- Developer
- Analyst
- Support Agent
- Auditor
- AI Agent

---

# 16. Attribute-Based Access Control

**ABAC (Attribute-Based Access Control)** evaluates attributes when deciding whether an action is permitted.

Potential attributes include:

- User identity
- Department
- Resource classification
- Location
- Device state
- Time
- Environment
- Data sensitivity

Example:

```text
User Department = Finance
        +
Data Classification = Confidential
        +
Action = Read
        |
        v
Authorization Decision
```

ABAC can be useful when access decisions need more context than simple roles provide.

---

# 17. Policy-Based Access Control

Policy-based approaches express authorization requirements as policies.

A policy may define:

- Who can access a resource
- What action is permitted
- Which resource is involved
- Under which conditions
- When access is allowed or denied

Example concept:

```text
ALLOW
User: SupportRole
Action: Read
Resource: SupportDocuments
Condition: User is authenticated
```

Policies should be centrally governed and regularly reviewed.

---

# 18. Service-to-Service Authorization

AI applications frequently call multiple services.

Example:

```text
Application
   |
   +--> Model Service
   |
   +--> Search Service
   |
   +--> Database
   |
   +--> Storage
   |
   +--> External API
```

Every service-to-service interaction should have appropriate authentication and authorization.

Do not assume that internal network location automatically means the request is trusted.

---

# 19. Access Control and Prompt Injection

Prompt injection can attempt to influence an AI system into performing actions outside its intended permissions.

For example:

```text
Attacker
   |
   v
Malicious Prompt
   |
   v
AI Agent
   |
   v
Unauthorized Tool Request
```

Strong authorization controls should prevent the tool request even if the model attempts to generate it.

### Key principle

**Prompt-level instructions are not a substitute for security authorization.**

The infrastructure or application layer should independently verify whether the requested action is permitted.

---

# 20. Logging and Monitoring

Access control becomes significantly stronger when actions are auditable.

Monitor:

- Authentication attempts
- Authorization failures
- Data access
- Tool calls
- Agent actions
- Permission changes
- Credential usage
- Administrative operations
- High-risk actions

Logs can help detect:

- Account compromise
- Privilege escalation
- Unauthorized access
- Abnormal agent behavior
- Repeated failed authorization attempts

---

# 21. Access Reviews

Permissions should not be granted permanently without review.

Regularly evaluate:

- Who has access?
- Why do they need access?
- Which permissions are unused?
- Which agents have excessive privileges?
- Which credentials are still active?
- Are role assignments still appropriate?
- Have responsibilities changed?

Remove unnecessary permissions promptly.

---

# 22. Separation of Duties

Separation of duties reduces the possibility that one identity can perform an entire high-risk workflow without oversight.

For example:

```text
Agent A
  |
  +--> Prepare Transaction
          |
          v
      Human Review
          |
          v
Agent B / Authorized Service
  |
  +--> Execute Transaction
```

This can be useful for sensitive or irreversible operations.

---

# 23. Secure Access Control Architecture

A comprehensive architecture may look like:

```text
                         +------------------+
                         | Identity Provider|
                         +--------+---------+
                                  |
                                  v
+----------+             +------------------+
|   User   | ----------> | Authentication   |
+----------+             +--------+---------+
                                  |
                                  v
                         +------------------+
                         | Authorization    |
                         | / Policy Engine  |
                         +--------+---------+
                                  |
                   +--------------+--------------+
                   |              |              |
                   v              v              v
               AI Model      Knowledge       Tools/APIs
                              Sources
                   |              |              |
                   +--------------+--------------+
                                  |
                                  v
                         +------------------+
                         | Logging &        |
                         | Monitoring       |
                         +------------------+
```

Every access path should have clearly defined ownership and permissions.

---

# 24. Access Control Checklist

## Authentication

- [ ] Verify user and service identities.
- [ ] Use strong authentication.
- [ ] Use MFA where appropriate.
- [ ] Avoid unnecessary long-lived credentials.
- [ ] Use appropriate service identities.

## Authorization

- [ ] Apply least privilege.
- [ ] Define explicit permissions.
- [ ] Separate read and write permissions.
- [ ] Protect sensitive resources.
- [ ] Review authorization policies regularly.

## AI Agents

- [ ] Give agents dedicated identities where appropriate.
- [ ] Limit agent permissions.
- [ ] Restrict available tools.
- [ ] Define agent privilege boundaries.
- [ ] Monitor agent actions.
- [ ] Require approval for high-risk operations.

## RAG and Data

- [ ] Enforce authorization before retrieval.
- [ ] Prevent unauthorized document access.
- [ ] Apply data-level permissions.
- [ ] Protect sensitive data.
- [ ] Review knowledge-source permissions.

## Tools and APIs

- [ ] Authenticate service-to-service requests.
- [ ] Authorize every sensitive operation.
- [ ] Validate tool parameters.
- [ ] Limit tool capabilities.
- [ ] Log tool calls.

## Monitoring

- [ ] Log authentication events.
- [ ] Log authorization failures.
- [ ] Monitor privilege changes.
- [ ] Detect unusual access patterns.
- [ ] Review high-risk agent actions.

---

# 25. Security Review Questions

Before deploying a generative AI system, ask:

### Identity

- Who is the user?
- What identity does the application use?
- What identity does the agent use?
- Are identities unique and auditable?

### Permissions

- What can the user access?
- What can the agent access?
- Which tools can the agent invoke?
- Are permissions limited to the minimum required?

### Data

- Can the model retrieve unauthorized information?
- Are data permissions enforced independently of the model?
- Are sensitive resources protected?

### Agents

- Can an agent perform irreversible actions?
- Can an agent delegate tasks?
- Can an agent modify its own permissions?
- Which actions require human approval?

### Operations

- Are access decisions logged?
- Are failed authorization attempts monitored?
- Are permissions reviewed periodically?
- Can compromised identities be quickly disabled?

---

# 26. Common Access Control Mistakes

Avoid these patterns:

### Mistake 1: Trusting the model to enforce permissions

The model should not be the final authorization mechanism.

### Mistake 2: Giving agents broad permissions

An agent should not have administrator-level access simply because it might need one administrative capability.

### Mistake 3: Ignoring retrieved-data permissions

RAG systems must ensure that users only receive information they are authorized to access.

### Mistake 4: Using shared credentials

Shared credentials make accountability and investigation difficult.

### Mistake 5: No monitoring

Without logs and monitoring, unauthorized access may remain undetected.

### Mistake 6: Permanent access

Permissions should be reviewed and removed when no longer necessary.

### Mistake 7: No human approval for high-risk operations

Sensitive or irreversible actions may require explicit human oversight.

---

# 27. Quick Revision Summary

| Concept | Key Idea |
|---|---|
| Authentication | Establishes who the requester is |
| Authorization | Determines what the requester can do |
| Least Privilege | Grant only required permissions |
| Defense in Depth | Use multiple independent security controls |
| RBAC | Permissions based on roles |
| ABAC | Permissions based on attributes and context |
| Policy-Based Access | Decisions based on explicit policies |
| RAG Security | Enforce permissions before returning retrieved data |
| Agent Identity | Give agents controlled and auditable identities |
| Tool Security | Independently authorize tool/API operations |
| Human-in-the-Loop | Require approval for high-risk actions |
| Logging | Record access and security events |
| Access Reviews | Periodically remove unnecessary permissions |
| Separation of Duties | Prevent one identity from controlling sensitive workflows |

---

# 28. Key Takeaways

The most important lessons are:

1. **Authentication verifies identity; authorization controls permissions.**
2. **Never rely on the AI model itself to enforce authorization.**
3. **Apply least privilege to users, applications, agents, tools, and services.**
4. **Use defense in depth for sensitive data and operations.**
5. **Enforce authorization at the data-access layer in RAG systems.**
6. **Give autonomous agents controlled identities and privilege boundaries.**
7. **Independently authorize every sensitive tool or API action.**
8. **Use human approval for high-risk or irreversible operations.**
9. **Use RBAC, ABAC, or policy-based controls according to the architecture's needs.**
10. **Use short-lived credentials where appropriate.**
11. **Log authentication, authorization, data access, and agent activity.**
12. **Regularly review and remove unnecessary permissions.**
13. **Use separation of duties for sensitive workflows.**
14. **Design access control to remain effective even if an AI component is manipulated.**

---

# 29. Final Takeaway

As generative AI systems evolve from simple chat interfaces into autonomous applications and multi-agent systems, access control becomes increasingly important.

A secure architecture should answer four questions for every important action:

**Who is requesting it? → What are they allowed to do? → What resource are they accessing? → Is the action appropriate under the current security policy?**

The strongest approach combines:

**Authentication + Authorization + Least Privilege + Defense in Depth + Data Protection + Logging & Monitoring + Human Oversight**

This creates a security framework capable of protecting data, model interactions, tools, and autonomous AI agents as their responsibilities grow and evolve.

---

## Disclaimer

This document is a learning aid based on the course description supplied by the user. It should not be treated as official AWS guidance. For production implementations, consult the latest AWS documentation, service-specific security guidance, organizational security policies, and applicable regulatory requirements.
