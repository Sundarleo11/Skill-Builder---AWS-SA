# Architecture Patterns – Design Patterns by Application Type
## SAP-C02 Exam Revision README

> **Course:** Architecture Patterns - Design Patterns by Application Type
>
> **Duration:** 2 hours | **Level:** Advanced
>
> **Exam target:** AWS Certified Solutions Architect – Professional (SAP-C02)

AWS describes SAP-C02 as an exam focused on designing optimized AWS solutions using the AWS Well-Architected Framework. The current exam has four domains: Organizational Complexity (26%), New Solutions (29%), Continuous Improvement (25%), and Migration/Modernization (20%). Application-type patterns are especially useful for reasoning about security, reliability, performance, cost, decoupling, and deployment trade-offs. citeturn0search1turn0search10

---

# 1. Core Idea

Different GenAI applications need different architectures.

Do **not** start with:

> “Which AWS service should I use?”

Start with:

> “What type of application is this, what is the user interaction pattern, what data is required, what latency is acceptable, and what security/reliability requirements exist?”

Typical application types:

```text
                    GenAI Applications
                           |
       ┌───────────────────┼───────────────────┐
       ↓                   ↓                   ↓
    Chatbots              RAG               Agents
       |                   |                   |
       ↓                   ↓                   ↓
 Conversation        Knowledge Q&A        Tool execution

       ┌───────────────────┼───────────────────┐
       ↓                   ↓                   ↓
  Summarization       Content generation   Insights/Analytics
       |                   |                   |
       ↓                   ↓                   ↓
 Documents           Text/media/code       Business intelligence
```

---

# 2. Pattern Selection Framework

For every SAP-C02 scenario, identify:

1. **Application type**
2. **User interaction**
3. **Data source**
4. **Need for real-time response**
5. **Need for external knowledge**
6. **Need for tool execution**
7. **Security requirements**
8. **Availability/RTO/RPO**
9. **Scale**
10. **Cost constraints**

Then select the simplest architecture that satisfies the requirements.

---

# 3. Chatbot Pattern

Typical architecture:

```text
User
 ↓
API / Web application
 ↓
Authentication
 ↓
Conversation service
 ↓
Memory
 ↓
Optional RAG
 ↓
Foundation Model
 ↓
Guardrails
 ↓
Response
```

Use for:

- Customer support
- Internal assistants
- FAQ
- Employee help desks
- Conversational interfaces

---

# 4. Simple Chatbot

If the chatbot only needs general model knowledge:

```text
User
 ↓
API
 ↓
Model
 ↓
Response
```

Do not introduce RAG, vector databases, agents, and complex orchestration unless requirements justify them.

**SAP-C02 principle:**

> Prefer the simplest architecture that meets the requirements.

---

# 5. Conversational Chatbot With Memory

```text
User
 ↓
API
 ↓
Conversation Manager
 ↓
Memory Store
 ↓
Relevant Conversation
 ↓
Model
 ↓
Response
```

Store conversation state externally rather than depending on one application instance.

Benefits:

- Horizontal scaling
- Failover
- Persistent sessions
- Multi-instance support

---

# 6. Customer Support Chatbot

Typical architecture:

```text
Customer
 ↓
Web / API
 ↓
Authentication
 ↓
Orchestrator
 ├── Knowledge retrieval
 ├── Customer data
 ├── Order API
 └── Model
       ↓
    Guardrails
       ↓
    Response
```

Important:

**Customer-specific information must be authorized before it reaches the model.**

Do not allow the model to determine whether a customer can access an order.

---

# 7. RAG Application Pattern

Use RAG when the application needs external or frequently changing knowledge.

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
```

Online:

```text
User Query
 ↓
Retriever
 ↓
Relevant Documents
 ↓
Prompt
 ↓
Foundation Model
 ↓
Answer
```

Good use cases:

- Company policies
- Product documentation
- Technical manuals
- Legal/internal documents
- Frequently updated knowledge

---

# 8. Enterprise RAG

```text
                       ┌→ S3 Documents
                       |
User → API → Auth → Retriever
                       |
                       └→ Vector Store
                              |
                              ↓
                       Relevant Context
                              |
                              ↓
                         Foundation Model
                              |
                              ↓
                         Guardrails
                              |
                              ↓
                           Answer
```

Security should be enforced before retrieval results are supplied to the model.

---

# 9. RAG vs Fine-Tuning

### Frequently changing knowledge

Use:

```text
RAG
```

### Behavior/style/task adaptation

Fine-tuning may be appropriate depending on the requirement.

### General reasoning

Use an appropriate foundation model.

**Exam trap:**

> Do not automatically fine-tune/retrain just because business documents change.

---

# 10. Document Q&A

Pattern:

```text
S3
 ↓
Document processing
 ↓
Chunking
 ↓
Embedding
 ↓
Vector index
```

Query:

```text
Question
 ↓
Semantic retrieval
 ↓
Relevant chunks
 ↓
LLM
 ↓
Answer + citations where required
```

---

# 11. Multi-Tenant RAG

For SaaS:

```text
Tenant A → Tenant A data
Tenant B → Tenant B data
```

Possible controls:

- Tenant-specific indexes
- Metadata filtering
- IAM
- ABAC
- Application authorization
- Separate storage where isolation requirements justify it

Example:

```text
Query
 +
tenant_id = A
 +
authorized classification
 ↓
Retriever
```

Never rely on the model to enforce tenant isolation.

---

# 12. Agent Pattern

Agents are appropriate when the application must:

- Plan
- Choose tools
- Execute actions
- Observe results
- Continue until the task is complete

Concept:

```text
User
 ↓
Agent
 ↓
Plan
 ↓
Choose tool
 ↓
Execute
 ↓
Observe
 ↓
Continue / finish
```

---

# 13. Agent vs Chatbot

### Chatbot

Usually:

```text
Question
 ↓
Model
 ↓
Answer
```

### Agent

Usually:

```text
Goal
 ↓
Reason / plan
 ↓
Tool selection
 ↓
Action
 ↓
Result
 ↓
Next action
```

**SAP-C02 clue:**

If the question says **"must interact with multiple external systems and take actions"**, think **agent/tool orchestration**, not a simple chatbot.

---

# 14. Agent With Tools

```text
                    ┌→ Database
                    |
User → Agent → Tool Router
                    |
                    ├→ REST API
                    |
                    ├→ Search
                    |
                    └→ Business System
```

Apply least privilege to every tool.

---

# 15. Agent Security

Never give an agent unrestricted administrator access.

Better:

```text
User Identity
 ↓
Authorization
 ↓
Agent
 ↓
Allowed Tool
 ↓
Allowed Operation
```

For high-risk operations:

```text
Agent
 ↓
Approval
 ↓
Tool
```

AWS's current SAP-C02 guide explicitly calls out responsible AI controls such as content filtering, access controls for generative/agentic AI, and human oversight workflows. citeturn0search1

---

# 16. Human-in-the-Loop Pattern

Use when actions are high impact.

```text
User
 ↓
Agent
 ↓
Proposed Action
 ↓
Policy Check
 ↓
Human Approval
 ↓
Execution
```

Examples:

- Financial transaction
- Production deployment
- Account deletion
- High-risk customer action

---

# 17. Insight / Data Analysis Pattern

Insight systems are different from conversational chatbots.

Typical flow:

```text
Data Sources
 ↓
ETL / Data Pipeline
 ↓
Data Lake / Warehouse
 ↓
Analytics
 ↓
GenAI Analysis
 ↓
Business Insight
```

Use for:

- Sales analysis
- Financial summaries
- Operational reports
- Trend detection
- Executive insights

---

# 18. GenAI Data Analyst

```text
User
 ↓
Natural-language question
 ↓
Orchestrator
 ↓
Schema / metadata
 ↓
Query generation
 ↓
Validation
 ↓
Analytics engine
 ↓
Result
 ↓
Model
 ↓
Explanation
```

**Security rule:**

The generated query must execute with appropriate permissions.

Do not give the model unrestricted database access.

---

# 19. Text-to-SQL Pattern

```text
User:
"Show revenue by region."

        ↓

LLM
        ↓
SQL
        ↓
Validation / policy
        ↓
Database
        ↓
Result
        ↓
LLM
        ↓
Natural-language explanation
```

Important controls:

- Read-only role where appropriate
- Query allow/deny policies
- Schema restrictions
- Row/column-level authorization
- Query timeout
- Resource limits

---

# 20. Content Generation Pattern

Examples:

- Marketing copy
- Product descriptions
- Emails
- Reports
- Documentation
- Code generation

Architecture:

```text
User
 ↓
Prompt / Template
 ↓
Model
 ↓
Guardrails
 ↓
Quality validation
 ↓
Human review if required
 ↓
Publish
```

For regulated/high-impact content, add approval workflows.

---

# 21. Content Personalization

```text
User profile
 +
Context
 +
Business rules
 ↓
Prompt
 ↓
Model
 ↓
Personalized content
```

Only provide the model with the minimum required personal data.

---

# 22. Summarization Pattern

For long documents:

```text
Document
 ↓
Chunking
 ↓
Summarize chunks
 ↓
Combine summaries
 ↓
Final summary
```

This is useful when the source is too large for a single context window.

---

# 23. Map-Reduce Summarization

```text
                 Document
                    |
              ┌─────┼─────┐
              ↓     ↓     ↓
           Chunk A B     C
              ↓     ↓     ↓
          Summary Summary Summary
              └─────┼─────┘
                    ↓
             Final synthesis
```

This pattern can improve scalability for large document collections.

---

# 24. Batch Content Generation

For large workloads:

```text
Input data
 ↓
Queue
 ↓
Workers
 ↓
Model
 ↓
Results
 ↓
S3 / Database
```

Avoid keeping an HTTP connection open for a long-running batch job.

---

# 25. Recommendation Pattern

A recommendation system may combine:

```text
User behavior
 +
Product/catalog data
 +
Business rules
 +
ML/GenAI
```

Example:

```text
User
 ↓
Recommendation service
 ↓
Candidate retrieval
 ↓
Ranking
 ↓
Optional GenAI explanation
 ↓
Response
```

Do not assume the LLM itself must perform all ranking.

---

# 26. Search + GenAI Pattern

```text
User Query
 ↓
Search
 ↓
Relevant results
 ↓
GenAI
 ↓
Natural-language answer
```

This is useful when users need both:

- Accurate retrieval
- Natural-language synthesis

---

# 27. Enterprise Knowledge Assistant

Combine:

```text
Authentication
      ↓
Authorization
      ↓
Search / RAG
      ↓
Conversation memory
      ↓
Foundation model
      ↓
Guardrails
      ↓
Answer
```

Common use cases:

- HR assistant
- IT help desk
- Engineering assistant
- Policy assistant

---

# 28. Workflow Automation Pattern

For deterministic business processes:

```text
Event
 ↓
Workflow
 ├→ Step 1
 ├→ Step 2
 ├→ AI decision
 ├→ Approval
 └→ Step 3
```

Use a workflow engine when deterministic orchestration, retries, state, and approvals are important.

**Exam principle:**

> Use deterministic workflow orchestration around probabilistic AI components.

---

# 29. AI + Step Functions

A useful pattern:

```text
Step Functions
 ↓
Validate
 ↓
AI call
 ↓
Validate output
 ↓
Human approval
 ↓
Business action
```

This is particularly useful when the workflow requires durable state and controlled execution.

---

# 30. Event-Driven AI

Example:

```text
S3 Upload
 ↓
Event
 ↓
Queue
 ↓
AI Processing
 ↓
Result Store
```

Good for:

- Document processing
- Classification
- Summarization
- Image analysis
- Batch enrichment

---

# 31. Real-Time AI

Interactive applications need low latency.

```text
Client
 ↓
API
 ↓
Application
 ↓
Model
 ↓
Streaming response
 ↓
Client
```

Optimize:

- Network path
- Retrieval latency
- Model selection
- Prompt size
- Concurrency
- Streaming

---

# 32. Streaming Chatbot

Instead of:

```text
Wait
 ↓
Complete response
 ↓
Display
```

use:

```text
Model
 ↓
Token stream
 ↓
Client
```

This improves **perceived latency**, even when total generation time is unchanged.

---

# 33. Batch vs Interactive

| Requirement | Pattern |
|---|---|
| Immediate user answer | Synchronous |
| Large document processing | Asynchronous |
| Millions of records | Batch |
| Event-triggered processing | Event-driven |
| Long workflow | Queue + workflow |
| Tool-based automation | Agent |
| Knowledge Q&A | RAG |
| Natural conversation | Chatbot |

---

# 34. Multimodal Application

For applications using text + images/audio/video:

```text
User
 ↓
Input classification
 ↓
Multimodal model
 ↓
Processing
 ↓
Guardrails
 ↓
Response
```

Use specialized processing components where required rather than forcing every input through one model.

---

# 35. Document Intelligence

Typical architecture:

```text
Document
 ↓
Object storage
 ↓
Text / structure extraction
 ↓
Classification
 ↓
GenAI processing
 ↓
Validation
 ↓
Database
```

Useful for:

- Invoices
- Contracts
- Applications
- Forms
- Reports

---

# 36. Code Generation Assistant

Pattern:

```text
Developer
 ↓
Code context
 ↓
Repository retrieval
 ↓
Model
 ↓
Security / policy checks
 ↓
Generated code
 ↓
Tests
 ↓
Human review
```

Do not automatically deploy AI-generated code to production without appropriate validation.

---

# 37. Incident Assistant

```text
Monitoring
 ↓
Alert
 ↓
Incident Assistant
 ↓
Logs / Metrics / Traces
 ↓
Knowledge base
 ↓
Diagnosis
 ↓
Suggested remediation
```

For automatic remediation:

```text
Suggestion
 ↓
Policy
 ↓
Approval if required
 ↓
Execution
```

---

# 38. Customer Service Agent

```text
Customer
 ↓
Authentication
 ↓
Agent
 ├→ CRM
 ├→ Order API
 ├→ Knowledge Base
 └→ Ticketing
        ↓
    Response
```

Key concerns:

- Identity
- Data authorization
- Tool permissions
- PII
- Audit
- Human escalation

---

# 39. Financial Analysis Assistant

```text
User
 ↓
Authorization
 ↓
Financial data
 ↓
Analytics
 ↓
Model
 ↓
Explanation
```

Important:

- Strong data controls
- Audit
- Deterministic calculations
- Human oversight for high-impact decisions

Do not rely on the LLM for precise arithmetic when a deterministic calculation engine can perform it.

---

# 40. Healthcare / Sensitive Data Pattern

For sensitive workloads:

```text
Identity
 ↓
Fine-grained authorization
 ↓
Minimum required data
 ↓
Secure processing
 ↓
Guardrails
 ↓
Audit
```

Use appropriate compliance and data-governance controls.

---

# 41. AI Application With External APIs

```text
User
 ↓
Application
 ↓
Agent / Orchestrator
 ↓
API authorization
 ↓
External API
 ↓
Result
 ↓
Model
```

Protect API credentials and enforce per-tool permissions.

---

# 42. Multi-Agent Pattern

Multiple specialized agents can perform different tasks.

```text
                 Supervisor
                /    |                    /     |               Research  Analyst  Writer
              \      |      /
               \     |     /
                 Result
```

Use when task decomposition provides clear value.

Do not introduce multi-agent complexity for a simple request.

---

# 43. Supervisor Pattern

```text
User
 ↓
Supervisor Agent
 ├→ Research Agent
 ├→ Data Agent
 ├→ Action Agent
 └→ Review Agent
```

The supervisor coordinates specialized workers.

Consider:

- Added latency
- Added cost
- More failure points
- More complex observability
- Security boundaries

---

# 44. Sequential Agent Pattern

```text
Agent A
 ↓
Agent B
 ↓
Agent C
 ↓
Final result
```

Useful when each stage depends on the previous result.

---

# 45. Parallel Agent Pattern

```text
             ┌→ Agent A
Request ─────┼→ Agent B
             └→ Agent C
                   ↓
                Combine
```

Useful when tasks are independent.

Can reduce latency compared with sequential execution.

---

# 46. Application Pattern Selection

## If the requirement says:

### "Natural conversation"

Think:

```text
Chatbot
+
Memory
+
Optional RAG
```

### "Answer from company documents"

Think:

```text
RAG
+
Vector/search store
```

### "Take actions using APIs"

Think:

```text
Agent
+
Tools
+
Authorization
```

### "Generate reports"

Think:

```text
Data pipeline
+
Analytics
+
GenAI summarization
```

### "Process millions of documents"

Think:

```text
S3
+
Events/queue
+
Workers
+
Batch processing
```

### "Long-running workflow"

Think:

```text
Queue
+
Workflow orchestration
```

---

# 47. Application Type Decision Tree

```text
What does the application need?
              |
      ┌───────┼────────┐
      ↓       ↓        ↓
Conversation Knowledge Actions
      |       |        |
   Chatbot    RAG     Agent
```

Then:

```text
Large-scale offline processing?
          |
         YES
          ↓
Async / batch architecture
```

And:

```text
High-risk action?
          |
         YES
          ↓
Approval + policy + audit
```

---

# 48. RAG vs Agent

### RAG

Question:

> "Find information and answer me."

```text
Query
 ↓
Retrieve
 ↓
Generate
```

### Agent

Question:

> "Perform a task using multiple systems."

```text
Goal
 ↓
Plan
 ↓
Tools
 ↓
Actions
 ↓
Result
```

**Very important exam distinction.**

---

# 49. Chatbot vs RAG

### Simple chatbot

```text
User → Model → Response
```

### Knowledge chatbot

```text
User → Retriever → Context → Model → Response
```

Use RAG when authoritative external knowledge is required.

---

# 50. RAG vs Search

### Search

Returns documents/results.

### RAG

Retrieves documents and uses a model to synthesize an answer.

```text
Search:
Query → Results

RAG:
Query → Results → Model → Answer
```

---

# 51. AI vs Deterministic Logic

Use deterministic code for:

- Exact calculations
- Authorization
- Financial transactions
- Policy enforcement
- Data validation
- State transitions

Use GenAI for:

- Natural-language understanding
- Summarization
- Generation
- Flexible reasoning
- Conversational interfaces

**Golden rule:**

> Keep deterministic business-critical controls outside the model.

---

# 52. Security Architecture for Every Pattern

Regardless of application type:

```text
Identity
 ↓
Authorization
 ↓
Data filtering
 ↓
Model / Agent
 ↓
Guardrails
 ↓
Output validation
 ↓
Audit
```

Use defense in depth.

---

# 53. IAM vs Guardrails

### IAM

Controls:

```text
Who/what can access AWS resources?
```

### Guardrails

Controls:

```text
What content/behavior should be allowed?
```

Do not confuse the two.

---

# 54. Observability by Application Type

### Chatbot

Track:

- Latency
- TTFT
- Tokens
- Errors
- User/session metrics

### RAG

Also track:

- Retrieval latency
- Retrieved document count
- Relevance/quality

### Agent

Also track:

- Tool calls
- Tool failures
- Number of steps
- Agent latency
- Cost per task

### Batch

Also track:

- Queue depth
- Throughput
- Processing failures
- Retry count

---

# 55. Cost Optimization by Pattern

### Chatbot

- Smaller model where acceptable
- Prompt optimization
- Streaming
- Caching where safe

### RAG

- Retrieve only relevant chunks
- Optimize embeddings/retrieval
- Cache safe repeated queries

### Agent

- Limit unnecessary tool calls
- Use appropriate model per task
- Prevent runaway loops

### Batch

- Queue and process asynchronously
- Right-size workers
- Batch operations where appropriate

---

# 56. Reliability Patterns

Use:

```text
Timeout
Retry
Circuit breaker
Fallback
Queue
Dead-letter queue
Idempotency
Health checks
```

Choose based on the failure mode.

AWS's SAP-C02 outline explicitly emphasizes loosely coupled dependencies, failure design, HA, RTO/RPO, and managed services for reliability. citeturn0search10

---

# 57. Deployment Patterns

For AI applications:

```text
Development
 ↓
Evaluation
 ↓
Canary
 ↓
Production
```

Use:

- Infrastructure as Code
- CI/CD
- Versioned prompts
- Versioned model configuration
- Automated tests
- Rollback

SAP-C02 includes deployment strategies and rollback mechanisms as part of new-solution design. citeturn0search10

---

# 58. Common SAP-C02 Traps

## Trap 1: Use an agent for everything

Wrong.

If the task is simply Q&A from documents, RAG may be simpler.

## Trap 2: Use RAG for every chatbot

Wrong.

If the model already has sufficient knowledge and no private/fresh data is needed, a simple chatbot may be enough.

## Trap 3: Let the model authorize access

Wrong.

Use IAM/application authorization.

## Trap 4: Give an agent administrator access

Wrong.

Use least privilege.

## Trap 5: Make long workflows synchronous

Usually wrong.

Use queues/workflows.

## Trap 6: Use GenAI for exact calculations

Usually wrong.

Use deterministic calculation systems.

## Trap 7: Put all application logic in one Lambda

Poor architecture for complex workflows.

Separate responsibilities.

## Trap 8: Use multi-agent architecture for a simple task

Unnecessary complexity.

## Trap 9: Give the model the entire database

Wrong.

Retrieve only authorized, relevant information.

## Trap 10: Deploy a new model directly to 100%

Risky.

Evaluate and use controlled rollout where appropriate.

---

# 59. SAP-C02 Scenario Cheat Sheet

| Requirement | Preferred Pattern |
|---|---|
| Conversational interface | Chatbot |
| Private enterprise knowledge | RAG |
| Frequently changing documents | RAG + automated ingestion |
| Execute business actions | Agent + tools |
| Multiple independent tasks | Parallel orchestration |
| Sequential dependent tasks | Workflow/sequential pattern |
| Human approval | Workflow + approval |
| Large document processing | Async/batch |
| Real-time interaction | Synchronous + streaming |
| Millions of records | Batch/event-driven |
| Natural-language analytics | Text-to-SQL + validation |
| Business report generation | Analytics + GenAI |
| High-risk operations | Authorization + approval + audit |
| Model replacement | Model abstraction + evaluation |
| Burst traffic | Queue/autoscaling |
| Multi-tenant knowledge | Tenant-aware authorization/retrieval |

---

# 60. SAP-C02 Golden Rules

1. Choose the architecture based on the application requirement.
2. Do not add GenAI components without a requirement.
3. Simple chatbot → model + conversation management.
4. Private/fresh knowledge → RAG.
5. External actions → agent + tools.
6. Long-running work → asynchronous processing.
7. Large offline workloads → batch/event-driven.
8. Deterministic business rules belong outside the model.
9. Authorization belongs outside the model.
10. Give tools least-privilege permissions.
11. Use human approval for high-risk actions.
12. Validate model-generated structured output.
13. Use RAG instead of retraining solely because knowledge changes frequently.
14. Filter RAG results by tenant and authorization requirements.
15. Keep application state in durable external systems.
16. Use queues to absorb bursts.
17. Use workflow orchestration for durable multi-step processes.
18. Use parallel execution for independent tasks.
19. Use sequential execution when later steps depend on earlier results.
20. Do not use multi-agent architecture unless it provides measurable value.
21. Use streaming to improve perceived interactive latency.
22. Use smaller models when quality requirements permit.
23. Monitor token usage and tool calls for cost control.
24. Use retries only for transient failures.
25. Make asynchronous processing idempotent.
26. Add circuit breakers for repeatedly failing dependencies.
27. Use guardrails for model/content controls.
28. Use IAM for AWS resource access.
29. Use audit logging for important actions.
30. Protect sensitive data before sending it to models.
31. Do not expose unrestricted database access to an LLM.
32. Use read-only database permissions for analytical workloads where appropriate.
33. Validate generated SQL before execution.
34. Use timeouts on model/tool/database operations.
35. Separate offline ingestion from online inference.
36. Use model evaluation before production migration.
37. Use canary/controlled rollout for risky changes.
38. Design according to RTO/RPO.
39. Use managed services when they reduce operational overhead without violating requirements.
40. Always balance security, reliability, performance, and cost.

---

# 61. 30-Second Revision

```text
APPLICATION TYPE
       |
       ├── Conversation
       │      ↓
       │   CHATBOT
       │
       ├── Private/fresh knowledge
       │      ↓
       │     RAG
       │
       ├── Actions/tools
       │      ↓
       │    AGENT
       │
       ├── Data insights
       │      ↓
       │ ANALYTICS + GENAI
       │
       ├── Large/long-running
       │      ↓
       │ ASYNC / BATCH
       │
       └── Complex business workflow
              ↓
        WORKFLOW ORCHESTRATION
```

---

# 62. Ultimate SAP-C02 Mental Model

When reading a scenario, ask:

```text
1. What is the application type?
          ↓
2. What data does it need?
          ↓
3. Does it need external knowledge?
          ↓
4. Does it need to take actions?
          ↓
5. Is the workload interactive or asynchronous?
          ↓
6. What security boundary is required?
          ↓
7. What happens when a dependency fails?
          ↓
8. What is the scale?
          ↓
9. What is the cost constraint?
          ↓
10. What is the simplest architecture that satisfies all requirements?
```

The goal is **not** to create the most sophisticated GenAI architecture.

The goal is to create the **right architecture for the application type and business requirements**.

---

# 63. Final Exam Formula

```text
Right Application Pattern
        +
Least Privilege
        +
Relevant Data
        +
Appropriate Model
        +
Reliable Integration
        +
Observability
        +
Scalability
        +
Cost Control
        =
Strong SAP-C02 Architecture
```

> **Remember:** SAP-C02 is fundamentally an architecture decision exam. AWS's official guide emphasizes optimized solutions, Well-Architected design, security, reliability, performance, cost, deployment, and loosely coupled architectures. citeturn0search1turn0search10
