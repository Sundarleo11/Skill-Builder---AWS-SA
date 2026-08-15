# Architecture Patterns – Architecture Components
## SAP-C02 Exam Revision README

> **Target:** AWS Certified Solutions Architect – Professional (SAP-C02)
>
> **Course:** Architecture Patterns - Architecture Components
>
> **Duration:** 2 hours
>
> **Level:** Advanced
>
> **Theme:** Designing GenAI applications as modular, evolvable systems rather than simple API integrations.

---

# 1. SAP-C02 Core Idea

The most important lesson is:

> **A production GenAI application is a distributed system, not simply an LLM API call.**

A weak design:

```text
User
  ↓
Application
  ↓
LLM
```

A stronger production architecture:

```text
Users
  ↓
API / Application
  ↓
Authentication & Authorization
  ↓
Orchestration
  ↓
┌───────────────┬────────────────┐
│               │                │
Memory       Retrieval        Tools
│               │                │
└───────────────┴────────────────┘
                ↓
             Model
                ↓
          Guardrails
                ↓
        Output Validation
                ↓
       Observability / Audit
```

The architecture should allow individual components to evolve without rebuilding the entire application.

---

# 2. Why GenAI Systems Need Good Architecture

GenAI systems change rapidly.

Potential changes include:

- Foundation model versions
- Model providers
- Prompt templates
- Retrieval strategy
- Embedding models
- Knowledge sources
- Tool integrations
- Guardrails
- Business requirements
- Traffic volume
- Security requirements

If everything is tightly coupled:

```text
Application
   ├── Prompt
   ├── Model
   ├── Retrieval
   ├── Tools
   └── Business logic
```

then changing one component can require changing everything.

Better:

```text
Application
   ↓
Stable interfaces
   ↓
Independent components
```

---

# 3. Modularity

**Modularity** means splitting the system into components with clear responsibilities.

Example:

```text
Authentication
     ↓
Request Handler
     ↓
Prompt Manager
     ↓
Retriever
     ↓
Model Adapter
     ↓
Guardrails
     ↓
Response Formatter
```

Each component should have a specific responsibility.

---

# 4. Separation of Concerns

Do not mix:

- Business logic
- Prompt logic
- Retrieval
- Model invocation
- Security
- Observability
- Data storage

Example of poor design:

```text
One Lambda
 ├── authentication
 ├── database queries
 ├── prompt construction
 ├── vector search
 ├── model invocation
 ├── response parsing
 └── auditing
```

Better:

```text
Auth Service
      ↓
Orchestrator
      ↓
Retriever
      ↓
Model Service
      ↓
Guardrail
      ↓
Audit
```

---

# 5. Stable Interfaces

A key architecture principle:

> **Components should communicate through stable contracts.**

For example:

```text
Application
   ↓
Model Interface
   ↓
Model A
```

Later:

```text
Application
   ↓
Same Model Interface
   ↓
Model B
```

The application does not need to be completely rewritten.

---

# 6. Model Abstraction

Avoid hard-coding model-specific behavior throughout the application.

Poor:

```text
Application
   ↓
Provider-specific model API
   ↓
Model
```

Better:

```text
Application
   ↓
Model abstraction / adapter
   ↓
Selected model
```

This allows:

- Model replacement
- Version upgrades
- A/B testing
- Model routing
- Cost optimization

---

# 7. Model Adapter Pattern

Concept:

```text
Application
     ↓
Model Adapter
     ↓
┌──────────┬──────────┬──────────┐
│ Model A  │ Model B  │ Model C  │
└──────────┴──────────┴──────────┘
```

The adapter converts application requests into model-specific requests.

It can normalize:

- Request format
- Parameters
- Response format
- Errors
- Metrics

---

# 8. Model Evolution

Models improve over time.

A production application should support:

```text
Model v1
   ↓
Model v2
   ↓
Model v3
```

without changing the entire application.

Use:

- Model abstraction
- Configuration-driven model selection
- Versioned prompts
- Evaluation
- Canary deployments
- Rollback

---

# 9. Configuration vs Code

Do not hard-code frequently changing values.

Prefer configuration for:

- Model ID
- Temperature where applicable
- Token limits
- Retrieval count
- Feature flags
- Prompt versions
- Guardrail settings

Concept:

```text
Application
    ↓
Configuration
    ↓
Model / Prompt / Retrieval settings
```

This allows changes without full application rewrites.

---

# 10. Prompt Management

Prompts are part of application behavior.

Treat prompts like software artifacts.

Use:

- Versioning
- Testing
- Evaluation
- Rollback
- Approval workflows

Example:

```text
Prompt v1
Prompt v2
Prompt v3
```

Do not change production prompts blindly.

---

# 11. Prompt Templates

Separate:

```text
System instructions
+
Retrieved context
+
User input
```

Concept:

```text
Prompt Template
      +
Dynamic Context
      +
User Request
      ↓
Final Prompt
```

This is easier to maintain than constructing prompts throughout business code.

---

# 12. Prompt Injection

User input must not automatically be treated as trusted instructions.

Example:

```text
System Instructions
       ↓
Retrieved Data
       ↓
Untrusted User Content
```

The architecture should clearly distinguish trusted instructions from untrusted data.

Use appropriate:

- Input validation
- Guardrails
- Access controls
- Prompt boundaries
- Output validation

---

# 13. Retrieval-Augmented Generation

RAG separates knowledge from the model.

```text
User
 ↓
Query
 ↓
Retriever
 ↓
Knowledge Store
 ↓
Relevant Context
 ↓
Prompt
 ↓
Foundation Model
 ↓
Answer
```

Benefits:

- Fresh information
- Domain-specific knowledge
- Reduced need to retrain the foundation model
- Better grounding

---

# 14. RAG Components

Typical RAG architecture:

```text
Documents
   ↓
Ingestion
   ↓
Chunking
   ↓
Embeddings
   ↓
Vector Store
   ↓
Retriever
   ↓
Relevant Context
   ↓
Prompt
   ↓
LLM
```

---

# 15. Ingestion Pipeline

Documents may arrive through:

- Amazon S3
- Databases
- SaaS systems
- APIs
- Enterprise repositories

Pipeline:

```text
Source
 ↓
Extract
 ↓
Clean
 ↓
Chunk
 ↓
Embed
 ↓
Index
```

This should generally be separate from the online query path.

---

# 16. Online Query Path

For a user request:

```text
User
 ↓
Query processing
 ↓
Embedding / retrieval
 ↓
Relevant documents
 ↓
Prompt construction
 ↓
Model
 ↓
Response
```

Do not run the entire document ingestion pipeline synchronously for every user query.

---

# 17. Vector Store

A vector store enables similarity-based retrieval.

Concept:

```text
Document
   ↓
Embedding
   ↓
Vector
   ↓
Vector Store
```

User query:

```text
Query
 ↓
Embedding
 ↓
Similarity Search
 ↓
Relevant Documents
```

---

# 18. Metadata Filtering

Vector similarity alone may not be enough.

Use metadata such as:

- Tenant ID
- Department
- Document type
- Region
- Security classification
- Date
- Access permissions

Example:

```text
Similarity Search
+
tenant_id = TenantA
+
classification = internal
```

This helps enforce data boundaries.

---

# 19. Tenant Isolation

For multi-tenant SaaS:

```text
Tenant A
  ↓
Tenant A data

Tenant B
  ↓
Tenant B data
```

Never allow retrieval to cross tenant boundaries.

Possible approaches:

- Separate indexes
- Separate data stores
- Tenant metadata filters
- IAM policies
- Application-level authorization
- Attribute-based access control

---

# 20. Retrieval Security

Important principle:

> **Authorization should happen before returning data to the model.**

Bad:

```text
Retrieve everything
 ↓
Let model decide what user can see
```

Good:

```text
User identity
 ↓
Authorization
 ↓
Authorized retrieval
 ↓
Relevant context
 ↓
Model
```

The LLM should not be the security boundary.

---

# 21. Orchestration Layer

The orchestrator coordinates components.

```text
User Request
     ↓
Orchestrator
   ┌─┼────┬──────┐
   ↓ ↓    ↓      ↓
Memory RAG Tools Guardrails
   └─┼────┴──────┘
     ↓
   Model
```

Responsibilities can include:

- Request routing
- Tool selection
- Retrieval
- Prompt construction
- Model invocation
- Error handling
- Response processing

---

# 22. Agentic Architecture

An agent can:

```text
Understand goal
    ↓
Plan
    ↓
Choose tool
    ↓
Execute
    ↓
Observe result
    ↓
Continue or finish
```

Example:

```text
User:
"Find my order and summarize the issue."

Agent
 ↓
Order API
 ↓
Retrieve order
 ↓
Analyze
 ↓
Generate summary
```

---

# 23. Tool Use

Tools allow models to interact with external systems.

Examples:

- APIs
- Databases
- Search
- Calculators
- Business systems
- Workflow engines

Architecture:

```text
Model
  ↓
Tool selection
  ↓
Tool API
  ↓
Result
  ↓
Model
```

---

# 24. Tool Security

Never assume the model should have unrestricted access.

Use:

```text
User identity
 ↓
Authorization
 ↓
Tool permissions
 ↓
Tool execution
```

Apply least privilege.

A model should not automatically receive administrator-level access.

---

# 25. Human-in-the-Loop

Some actions require human approval.

Example:

```text
Agent
 ↓
High-risk action
 ↓
Human approval
 ↓
Execute
```

Useful for:

- Financial transactions
- Account deletion
- Production changes
- Sensitive communications
- High-impact decisions

---

# 26. Memory

GenAI applications may need different types of memory.

### Short-term memory

Current conversation.

### Long-term memory

Information retained across sessions.

### External memory

Data stored in databases or knowledge stores.

Concept:

```text
User
 ↓
Conversation
 ↓
Memory Store
 ↓
Relevant Memory
 ↓
Model
```

---

# 27. Do Not Put Everything in Model Context

Memory should not mean:

```text
Entire user history
 ↓
Every prompt
```

Instead:

```text
Current request
+
Relevant memory
+
Relevant knowledge
```

This reduces:

- Token usage
- Latency
- Cost
- Context overload

---

# 28. Guardrails

Guardrails help control model inputs and outputs.

Concept:

```text
Input
 ↓
Input Guardrail
 ↓
Model
 ↓
Output Guardrail
 ↓
User
```

Can help with:

- Unsafe content
- Sensitive information
- Topic restrictions
- Prompt injection defenses
- Policy enforcement

---

# 29. Guardrails Are Not IAM

Important SAP-C02 distinction:

**IAM controls AWS resource access.**

**Guardrails control model/application behavior.**

Example:

```text
IAM
→ Can this workload access S3?

Guardrail
→ Is this model response allowed?
```

Do not substitute one for the other.

---

# 30. Output Validation

Never blindly trust model output.

Validate:

- Schema
- Required fields
- Data types
- Allowed values
- Business rules

Example:

```text
LLM
 ↓
JSON
 ↓
Schema validation
 ↓
Business validation
 ↓
Application
```

---

# 31. Structured Output

For application integration:

```text
LLM
 ↓
Structured response
 ↓
Validator
 ↓
Application
```

Example:

```json
{
  "customer_id": "123",
  "priority": "HIGH",
  "summary": "..."
}
```

This is more reliable than parsing arbitrary natural language.

---

# 32. Error Handling

GenAI systems can fail at multiple layers.

```text
API
 ↓
Retrieval
 ↓
Model
 ↓
Tool
 ↓
Validation
```

Handle failures independently.

Possible patterns:

- Retry transient failures
- Exponential backoff
- Circuit breaker
- Timeout
- Fallback model
- Fallback response
- Dead-letter queue for asynchronous workloads

---

# 33. Retries

Retry only appropriate failures.

Good candidates:

- Temporary network errors
- Throttling
- Transient service errors

Be careful with:

- Non-idempotent operations
- Invalid requests
- Authorization failures

Repeated retries can amplify load.

---

# 34. Circuit Breaker

If a downstream dependency repeatedly fails:

```text
Application
    ↓
Circuit Breaker
    ↓
Failing service
```

After repeated failures:

```text
OPEN
 ↓
Fail fast
```

This protects the rest of the system.

---

# 35. Timeouts

Every external dependency should have appropriate timeouts.

Example:

```text
Application
 ↓
Retriever timeout
 ↓
Model timeout
 ↓
Tool timeout
```

Without timeouts, requests can remain stuck and consume resources.

---

# 36. Fallbacks

Example:

```text
Primary Model
      ↓
Failure
      ↓
Fallback Model
```

or:

```text
Primary Retrieval
      ↓
Failure
      ↓
Cached / alternate source
```

Fallback should preserve acceptable functionality.

---

# 37. Observability

A production GenAI architecture needs visibility across components.

```text
User
 ↓
API
 ↓
Orchestrator
 ↓
Retriever
 ↓
Model
 ↓
Tool
 ↓
Response
```

Track each stage.

---

# 38. Important Metrics

### Application

- Request rate
- Error rate
- End-to-end latency

### Model

- TTFT
- Tokens/sec
- Input tokens
- Output tokens
- Model errors

### Retrieval

- Retrieval latency
- Number of documents
- Relevance/quality metrics

### Infrastructure

- CPU
- Memory
- Network
- Accelerator utilization

### Business

- Cost/request
- User satisfaction
- Task success rate

---

# 39. Distributed Tracing

A single user request may involve:

```text
API
 ↓
Lambda
 ↓
Retriever
 ↓
Vector Store
 ↓
Model
 ↓
Tool
 ↓
Database
```

Distributed tracing helps identify which component causes latency or failure.

---

# 40. Logging

Log enough information for troubleshooting, but avoid logging sensitive data unnecessarily.

Potential fields:

- Request ID
- Tenant ID where appropriate
- Model/version
- Prompt version
- Latency
- Token counts
- Tool calls
- Error type

Do not blindly log:

- Secrets
- Credentials
- Sensitive user content
- Private information

---

# 41. Auditability

For enterprise systems, record important actions.

Example:

```text
User
 ↓
Request
 ↓
Tool selected
 ↓
Authorization
 ↓
Action
 ↓
Result
```

Audit trails support:

- Compliance
- Security investigation
- Troubleshooting
- Governance

---

# 42. Data Governance

GenAI architecture should define:

```text
Where data comes from
Where data is stored
Who can access it
How long it is retained
How it is encrypted
How it is deleted
```

---

# 43. Encryption

Protect:

- Documents
- Vector stores
- Conversation memory
- Logs
- Cached responses
- Model-related data

Use encryption in transit and at rest where appropriate.

AWS KMS can provide key management for supported AWS services.

---

# 44. IAM Least Privilege

The GenAI application should receive only required permissions.

Example:

```text
Application role
 ↓
Read only required S3 prefix
```

Not:

```text
Application role
 ↓
AdministratorAccess
```

---

# 45. Secrets Management

Do not store API keys/passwords directly in:

- Source code
- Prompts
- Container images
- Plain-text configuration

Use appropriate secret-management mechanisms such as:

- AWS Secrets Manager
- AWS Systems Manager Parameter Store where appropriate
- IAM roles for AWS service access

---

# 46. Stateless Application Layer

Whenever possible, keep application compute stateless.

```text
Request
 ↓
Any available instance
```

Store state externally:

```text
DynamoDB
S3
Database
Memory store
```

Benefits:

- Horizontal scaling
- Easier recovery
- Better availability
- Easier deployment

---

# 47. Stateful vs Stateless

### Stateless

```text
Instance
 ↓
No important local state
```

Easy to scale horizontally.

### Stateful

```text
Instance
 ↓
Important local session/state
```

Harder to scale and recover.

For GenAI systems, store conversation state/memory in appropriate external systems rather than relying on one application instance.

---

# 48. Synchronous Architecture

Use for interactive workloads.

```text
User
 ↓
API
 ↓
Orchestrator
 ↓
Model
 ↓
Response
```

Good when the user expects immediate feedback.

---

# 49. Asynchronous Architecture

Use when processing can happen later.

```text
User
 ↓
API
 ↓
Queue
 ↓
Worker
 ↓
GenAI
 ↓
Result Store
```

Benefits:

- Decoupling
- Burst handling
- Retry
- Backpressure
- Long-running processing

---

# 50. Queue-Based Load Leveling

When traffic spikes:

```text
Clients
   ↓
Queue
   ↓
Workers
   ↓
Model
```

The queue absorbs bursts.

Workers process at a controlled rate.

This prevents the model service from being overwhelmed.

---

# 51. Backpressure

Backpressure prevents producers from overwhelming consumers.

```text
Producer
   ↓
Queue
   ↓
Consumer
```

If consumer capacity is limited:

```text
Queue grows
 ↓
Rate limit / slow producer
```

This is a common distributed-system pattern.

---

# 52. Event-Driven Architecture

Example:

```text
S3 upload
   ↓
Event
   ↓
Processing workflow
   ↓
Document extraction
   ↓
Embedding
   ↓
Vector index
```

This is useful for decoupled ingestion pipelines.

---

# 53. Workflow Orchestration

Complex GenAI workflows may require:

```text
Step 1
 ↓
Step 2
 ↓
Parallel steps
 ↓
Approval
 ↓
Model
 ↓
Validation
```

Use workflow orchestration where appropriate rather than placing a complex workflow into one function.

---

# 54. Fan-Out / Fan-In

Example:

```text
Request
   ↓
Fan-out
 ┌─┼────┐
 ↓ ↓    ↓
A  B    C
 └─┼────┘
   ↓
Fan-in
   ↓
Result
```

Useful when independent tasks can run concurrently.

---

# 55. Multi-Model Architecture

Do not assume one model must handle every task.

Example:

```text
Router
 ├── Classification model
 ├── Embedding model
 ├── Small language model
 └── Large reasoning model
```

Choose the appropriate model for each workload.

---

# 56. Model Routing Architecture

```text
                  ┌→ Small model
Request → Router ─┼→ Medium model
                  └→ Large model
```

Routing criteria can include:

- Complexity
- Cost
- Latency
- User tier
- Data sensitivity
- Required capabilities

---

# 57. Evaluation Layer

Before replacing a model:

```text
Candidate Model
      ↓
Evaluation Dataset
      ↓
Quality
Latency
Cost
Safety
      ↓
Production decision
```

Do not evaluate models only by benchmark scores.

Use workload-specific tests.

---

# 58. Regression Testing

A new model or prompt can unexpectedly change behavior.

Test:

```text
Model v1
vs
Model v2
```

Check:

- Accuracy
- Hallucination
- Safety
- Latency
- Cost
- Structured output validity

---

# 59. Canary Deployment

Example:

```text
95% → Current model
5%  → New model
```

Monitor.

If successful:

```text
25%
 ↓
50%
 ↓
100%
```

If failures increase:

```text
Rollback
```

---

# 60. Blue/Green Deployment

Maintain:

```text
Blue → Current production
Green → New version
```

Switch traffic when validated.

Useful for controlled releases and quick rollback.

---

# 61. Feature Flags

Feature flags can control:

```text
New model
New prompt
New retrieval strategy
New guardrail
```

without redeploying the entire application.

---

# 62. Resilience

GenAI architecture should tolerate:

- Model errors
- Throttling
- Retrieval failures
- Network failures
- Tool failures
- Region/service issues where relevant

Use:

```text
Timeouts
Retries
Circuit breakers
Queues
Fallbacks
Health checks
```

---

# 63. Availability Zones

For application components deployed on AWS infrastructure:

```text
AZ-A
       → Load Balancer → Application
   /
AZ-B
```

Avoid single-AZ dependency for critical application components where the service architecture permits.

---

# 64. Multi-Region

For high availability/disaster recovery requirements:

```text
Region A
   ↓
Primary

Region B
   ↓
Standby / secondary
```

The exact pattern depends on:

- RTO
- RPO
- Data replication
- Model/service availability
- Cost
- Operational complexity

Do not automatically choose multi-region.

---

# 65. RTO vs RPO

### RTO

How quickly the application must recover.

### RPO

How much data loss is acceptable.

Example:

```text
RTO = 15 minutes
RPO = 5 minutes
```

Architecture must be designed to meet those targets.

---

# 66. Cost-Aware Architecture

Consider costs across:

```text
Model inference
Retrieval
Storage
Data transfer
Application compute
Logging
Caching
Monitoring
```

A model that is cheaper per token may still be more expensive overall if it requires many more tokens or retries.

---

# 67. Component Ownership

A mature architecture gives each component a clear responsibility.

Example:

| Component | Responsibility |
|---|---|
| API | Request entry |
| Auth | Identity |
| IAM | AWS resource authorization |
| Orchestrator | Workflow |
| Retriever | Knowledge retrieval |
| Vector store | Semantic search data |
| Memory | User/session state |
| Model adapter | Model abstraction |
| LLM | Generation/reasoning |
| Guardrail | Policy/safety |
| Validator | Output correctness |
| Queue | Decoupling/backpressure |
| Observability | Metrics/logs/traces |

---

# 68. Anti-Pattern: God Service

Bad:

```text
One service
 ├── Auth
 ├── Retrieval
 ├── Model
 ├── Memory
 ├── Tools
 ├── Billing
 ├── Logging
 └── Notifications
```

Problems:

- Hard to test
- Hard to scale independently
- High blast radius
- Difficult model migration
- Difficult deployments

---

# 69. Anti-Pattern: Hard-Coded Model

Bad:

```text
Business Logic
 ↓
Specific model ID
 ↓
Model
```

Problems:

- Difficult model migration
- Hard A/B testing
- Tight coupling
- Difficult cost optimization

Better:

```text
Business Logic
 ↓
Model abstraction
 ↓
Configuration / router
 ↓
Model
```

---

# 70. Anti-Pattern: LLM as Security Boundary

Bad:

```text
Database
 ↓
LLM decides
 ↓
User
```

The model should not determine authorization.

Better:

```text
Identity
 ↓
IAM / Authorization
 ↓
Authorized data
 ↓
LLM
```

---

# 71. Anti-Pattern: Retrieve Everything

Bad:

```text
Entire knowledge base
 ↓
Huge prompt
 ↓
LLM
```

Better:

```text
Query
 ↓
Authorized retrieval
 ↓
Relevant documents
 ↓
LLM
```

---

# 72. Anti-Pattern: One Giant Prompt

Bad:

```text
Business rules
+
Entire history
+
Entire database
+
All documents
+
User query
```

Better:

```text
System instructions
+
Relevant memory
+
Relevant retrieved context
+
User request
```

---

# 73. Anti-Pattern: Synchronous Long-Running Work

Bad:

```text
User
 ↓
HTTP request
 ↓
20-minute GenAI job
```

Better:

```text
User
 ↓
API
 ↓
Queue
 ↓
Worker
 ↓
Result store
 ↓
Notification/status
```

---

# 74. Anti-Pattern: No Observability

If a user says:

> "The AI is slow."

You should be able to determine:

```text
API latency?
Retrieval latency?
Queue latency?
Model TTFT?
Generation speed?
Tool latency?
Network?
```

Without tracing, troubleshooting becomes guesswork.

---

# 75. Architecture Evolution

Good architecture supports:

```text
Today:
Model A

Tomorrow:
Model B

Later:
Model C
```

without changing:

```text
Business application
```

This is the central architectural principle of the course.

---

# 76. Component Contract Example

A model component could expose:

```text
generate(request) → response
```

Application should not need to know:

```text
How the model is hosted
Which provider is used
Which hardware is used
Which API version is used
```

The abstraction hides implementation details.

---

# 77. Dependency Inversion

High-level business logic should depend on abstractions rather than implementation details.

```text
Business Logic
      ↓
Interface
      ↓
Implementation
```

This makes model and infrastructure changes easier.

---

# 78. Blast Radius

A failure in one component should not bring down the entire system.

Example:

```text
Recommendation model failure
        ↓
Chat still works
```

Use isolation and graceful degradation.

---

# 79. Graceful Degradation

If a non-critical component fails:

```text
Primary feature
     ↓
Optional recommendation fails
     ↓
Return core response
```

Do not fail the entire application unnecessarily.

---

# 80. Bulkhead Pattern

Separate resources for independent workloads.

```text
Customer Chat
   ↓
Pool A

Batch Analytics
   ↓
Pool B
```

A spike in batch workloads should not consume all resources needed for interactive users.

---

# 81. Priority Queues

Example:

```text
High priority
   ↓
Customer request

Medium
   ↓
Internal request

Low
   ↓
Batch workload
```

Under load, process according to business priority.

---

# 82. Security Architecture

A production GenAI system should have defense in depth:

```text
Identity
 ↓
IAM
 ↓
Network controls
 ↓
Data authorization
 ↓
Input protection
 ↓
Guardrails
 ↓
Output validation
 ↓
Logging / audit
```

No single control is sufficient.

---

# 83. Defense in Depth

Examples:

```text
IAM
+
Encryption
+
Network isolation
+
Data filtering
+
Guardrails
+
Validation
+
Monitoring
```

This is stronger than relying on the model itself.

---

# 84. Data Residency

Enterprise requirements may restrict where data can be processed or stored.

Consider:

- AWS Region
- Data stores
- Logs
- Model/service availability
- Cross-region transfer

Architecture must satisfy regulatory/business requirements.

---

# 85. Data Minimization

Send only data required for the task.

```text
Full customer record
        ↓
Only required attributes
        ↓
Model
```

Benefits:

- Security
- Privacy
- Lower token usage
- Lower cost

---

# 86. PII Handling

If sensitive data is unnecessary:

```text
Detect
 ↓
Redact / tokenize
 ↓
Model
```

Where required, implement appropriate controls around sensitive information.

---

# 87. Caching Architecture

A safe cache design:

```text
Request
 ↓
Authorization
 ↓
Cache lookup
 ↓
Tenant/user-aware key
 ↓
Cached result
```

Do not use an authorization-blind global cache for private data.

---

# 88. API Gateway Pattern

A typical entry architecture:

```text
Client
 ↓
Amazon API Gateway
 ↓
Lambda / Application
 ↓
Orchestrator
 ↓
GenAI services
```

Benefits can include:

- API management
- Authentication integration
- Throttling
- Request control
- Observability

---

# 89. Serverless Pattern

For variable workloads:

```text
API Gateway
 ↓
Lambda
 ↓
Orchestrator
 ↓
Amazon Bedrock
```

Good for:

- Event-driven workflows
- Variable request volume
- Reduced infrastructure management

But account for service quotas, timeouts, and execution characteristics.

---

# 90. Storage Selection

Do not use one database for every purpose.

Example:

```text
S3
→ Documents / objects

DynamoDB
→ Application/session metadata

Relational DB
→ Relational business data

Vector-capable store
→ Semantic retrieval

ElastiCache / cache
→ Low-latency reusable data
```

Choose based on access pattern.

---

# 91. RAG Storage Architecture

```text
Raw Documents
     ↓
Amazon S3
     ↓
Processing Pipeline
     ↓
Embeddings
     ↓
Vector Store

Online:
User Query
     ↓
Vector Search
     ↓
Relevant Context
     ↓
Model
```

Keep raw source data separate from derived vector representations.

---

# 92. Event-Driven Document Ingestion

Example:

```text
Document uploaded to S3
        ↓
S3 Event
        ↓
EventBridge / Queue
        ↓
Processing
        ↓
Chunking
        ↓
Embedding
        ↓
Vector index
```

Benefits:

- Decoupling
- Retry
- Scalability
- Asynchronous processing

---

# 93. Exactly-Once vs At-Least-Once

Distributed systems often process messages more than once.

Therefore design ingestion/workflows to be **idempotent** where possible.

Example:

```text
Document ID
+
Version
```

can prevent duplicate indexing.

---

# 94. Idempotency

An operation is idempotent when repeating it produces the same desired result.

Important for:

- Retries
- Queue processing
- Event processing
- Tool execution

Be especially careful with operations such as:

```text
Charge credit card
Delete resource
Send email
```

---

# 95. Tool Execution and Idempotency

Bad:

```text
Agent retries
 ↓
Charge customer again
```

Better:

```text
Idempotency key
 ↓
Safe retry
```

The LLM should not be allowed to create uncontrolled side effects.

---

# 96. Human Approval for Side Effects

For high-risk tools:

```text
Model
 ↓
Proposed action
 ↓
Policy check
 ↓
Human approval
 ↓
Execution
```

This limits agentic risk.

---

# 97. Architecture Decision Framework

For SAP-C02 scenarios, ask:

### 1. What is the workload?

- Interactive
- Batch
- Event-driven
- Agentic
- RAG
- SaaS

### 2. What is the bottleneck?

- Latency
- Cost
- Throughput
- Memory
- Availability
- Security

### 3. What component should own the responsibility?

Do not solve a retrieval problem with a model change.

Do not solve an authorization problem with a prompt.

Do not solve a burst problem with permanent peak infrastructure.

---

# 98. Decision Tree – Model Changes

```text
Need to replace model?
       ↓
Is application tightly coupled?
       |
      YES
       ↓
Introduce model abstraction
       ↓
Configuration / adapter
       ↓
Evaluate new model
       ↓
Canary
       ↓
Rollback if needed
```

---

# 99. Decision Tree – Knowledge Changes

```text
Knowledge changes frequently?
       |
      YES
       ↓
RAG
       ↓
External knowledge store
       ↓
Update ingestion/index
```

Do not automatically retrain the foundation model for every knowledge update.

---

# 100. Decision Tree – High Traffic

```text
Traffic spike
      ↓
Can requests be asynchronous?
      |
     YES
      ↓
Queue
      ↓
Workers
      ↓
Scale consumers

If interactive:
      ↓
Autoscale
+
Rate limiting
+
Appropriate warm capacity
+
Caching
```

---

# 101. Decision Tree – Security

```text
User requests data
       ↓
Authenticate
       ↓
Authorize
       ↓
Retrieve only authorized data
       ↓
Model
       ↓
Guardrail
       ↓
Validate output
       ↓
Audit
```

Remember:

> **The model is not the authorization layer.**

---

# 102. Decision Tree – Model Selection

```text
Need GenAI response
       ↓
What quality is required?
       ↓
Can smaller model meet it?
   |               |
 YES              NO
   ↓               ↓
Small model     Larger model
   ↓               ↓
Benchmark both
       ↓
Quality + cost + latency
```

---

# 103. Decision Tree – Resilience

```text
Dependency failure
       ↓
Is failure transient?
  |             |
 YES           NO
  ↓             ↓
Retry       Fallback / degrade
  ↓
Still failing?
  ↓
Circuit breaker
```

For async processing:

```text
Queue
+
Retry
+
Dead-letter queue
```

---

# 104. Decision Tree – Data Architecture

```text
What data?
    |
    ├── Object/document → S3
    |
    ├── Key-value/session → DynamoDB
    |
    ├── Relational → RDS/Aurora
    |
    ├── Semantic search → Vector-capable store
    |
    └── Hot cache → ElastiCache
```

Choose based on access pattern, not familiarity.

---

# 105. SAP-C02 Exam Traps

## Trap 1

**"Let the LLM decide whether the user is authorized."**

Wrong.

Authorization belongs to IAM/application authorization.

---

## Trap 2

**"Put the entire knowledge base into the prompt."**

Usually wrong.

Use retrieval and relevant context.

---

## Trap 3

**"Retrain the model whenever business data changes."**

Usually unnecessary.

Use RAG when external knowledge changes frequently.

---

## Trap 4

**"Use one giant service for simplicity."**

Usually poor for complex, evolving GenAI systems.

Prefer modular components with clear interfaces.

---

## Trap 5

**"Use the biggest model for every request."**

Not necessarily.

Use model routing or right-size based on quality requirements.

---

## Trap 6

**"Store session state on one application instance."**

Poor scalability/resilience.

Externalize important state.

---

## Trap 7

**"Retry every error."**

Wrong.

Retry transient failures; do not blindly retry invalid or non-idempotent operations.

---

## Trap 8

**"Cache every response globally."**

Potential data leakage and stale data.

Use appropriate cache keys and invalidation.

---

## Trap 9

**"Guardrails replace IAM."**

Wrong.

Guardrails and IAM solve different problems.

---

## Trap 10

**"Synchronous architecture for a 30-minute workflow."**

Usually inappropriate.

Use asynchronous processing.

---

## Trap 11

**"Multi-region is always required."**

Wrong.

Choose according to RTO/RPO, business requirements, cost, and service capabilities.

---

## Trap 12

**"More context is always better."**

Wrong.

Relevant context is better than maximum context.

---

# 106. SAP-C02 Golden Rules

1. **Treat GenAI as a distributed system, not merely an API call.**
2. **Use modular components with clear responsibilities.**
3. **Use stable interfaces to reduce coupling.**
4. **Abstract model-specific implementation details where model replacement is expected.**
5. **Keep frequently changing configuration outside business logic.**
6. **Version prompts like software artifacts.**
7. **Use RAG when external knowledge changes frequently.**
8. **Separate offline ingestion from the online query path.**
9. **Retrieve only relevant and authorized data.**
10. **Never use the LLM as the security boundary.**
11. **IAM controls AWS resource access; guardrails control model/application behavior.**
12. **Use metadata filtering and authorization for tenant isolation.**
13. **Use an orchestration layer for complex workflows.**
14. **Give agents least-privilege tool access.**
15. **Use human approval for high-risk side effects where required.**
16. **Validate model outputs before using them in business systems.**
17. **Use structured outputs for machine-to-machine integration.**
18. **Use timeouts, retries, and circuit breakers appropriately.**
19. **Do not retry non-idempotent operations blindly.**
20. **Use queues for asynchronous workloads and burst absorption.**
21. **Keep application compute stateless when possible.**
22. **Store important state in durable external services.**
23. **Use model routing when different requests have different requirements.**
24. **Evaluate new models before production migration.**
25. **Use canary/blue-green deployment for safer model changes.**
26. **Design for graceful degradation.**
27. **Use distributed tracing to identify component-level bottlenecks.**
28. **Do not log sensitive data unnecessarily.**
29. **Choose storage based on access patterns.**
30. **Use event-driven ingestion for scalable document processing.**
31. **Design asynchronous processing to tolerate duplicate delivery.**
32. **Use idempotency for retryable side-effect operations.**
33. **Use bulkheads to isolate workloads.**
34. **Use priority queues when workloads have different business importance.**
35. **Use multi-region only when business availability/DR requirements justify it.**
36. **Design according to RTO and RPO.**
37. **Use defense in depth for GenAI security.**
38. **Minimize sensitive data sent to models.**
39. **Optimize components independently rather than rebuilding the whole system.**
40. **The best architecture is one that can evolve as models and business requirements change.**

---

# 107. Quick Architecture Diagram

```text
                         USERS
                           |
                           v
                    API / APPLICATION
                           |
                    Authentication
                           |
                     Authorization
                           |
                           v
                    ORCHESTRATOR
              ┌────────────┼────────────┐
              |            |            |
              v            v            v
           MEMORY        RAG          TOOLS
              |            |            |
              |       ┌────┴────┐       |
              |       |         |       |
              |      S3     Vector DB   |
              |                  |      |
              └──────────┬───────┴──────┘
                         |
                         v
                  MODEL ABSTRACTION
                         |
                ┌────────┼────────┐
                v        v        v
             Model A  Model B  Model C
                         |
                         v
                    GUARDRAILS
                         |
                         v
                 OUTPUT VALIDATION
                         |
                         v
                      RESPONSE

        ┌─────────────────────────────────┐
        │ Observability / Audit / Security│
        └─────────────────────────────────┘
```

---

# 108. 30-Second Revision

```text
GENAI ARCHITECTURE

Don't build:
User → LLM

Build:
User
 ↓
Auth
 ↓
Orchestrator
 ↓
RAG / Memory / Tools
 ↓
Model abstraction
 ↓
Model
 ↓
Guardrails
 ↓
Validation
 ↓
Response

Across everything:
Security
Observability
Resilience
Scalability
Cost
```

---

# 109. Ultimate SAP-C02 Mental Model

When the question says:

> "The organization needs a GenAI architecture that can evolve as models and business requirements change."

Think:

```text
Modular architecture
        ↓
Separation of concerns
        ↓
Stable interfaces
        ↓
Model abstraction
        ↓
Externalized configuration
        ↓
Versioned prompts
        ↓
Independent RAG / memory / tools
        ↓
Observability
        ↓
Security
        ↓
Canary + rollback
```

When the question says:

> "Knowledge changes frequently."

Think:

```text
RAG
+
External knowledge store
+
Automated ingestion
```

When it says:

> "Protect tenant data."

Think:

```text
Identity
+
Authorization
+
Tenant-aware retrieval
+
Tenant-aware cache
+
Least privilege
```

When it says:

> "Long-running workload."

Think:

```text
Asynchronous
+
Queue
+
Worker
+
Retry
+
DLQ
```

When it says:

> "Replace model without rewriting application."

Think:

```text
Model abstraction
+
Adapter
+
Configuration
+
Evaluation
+
Canary
```

When it says:

> "AI application is becoming difficult to maintain."

Think:

```text
Decompose
+
Separate responsibilities
+
Stable contracts
+
Independent components
```

---

# 110. Final Exam Takeaway

The central SAP-C02 lesson is:

> **Design GenAI applications so that the model is replaceable, knowledge is externalized, security is enforced outside the model, workflows are modular, state is durable, failures are isolated, and every important component is observable.**

A strong architecture should survive:

```text
Model change
+
Prompt change
+
Knowledge change
+
Traffic growth
+
Tool change
+
Business requirement change
+
Component failure
```

without requiring a complete system rebuild.

## Final Formula

```text
Evolvable GenAI Architecture
=
Modularity
+
Stable Interfaces
+
Model Abstraction
+
RAG
+
Memory
+
Secure Tools
+
Guardrails
+
Output Validation
+
Observability
+
Resilience
+
Scalability
+
Least Privilege
```

**SAP-C02 mindset:**

> **Do not ask only "Which AWS service should I use?"**
>
> Ask:
>
> **"Which component owns this responsibility, how does it communicate with other components, what happens when it changes or fails, and how do I keep the system secure, scalable, observable, and evolvable?"**
