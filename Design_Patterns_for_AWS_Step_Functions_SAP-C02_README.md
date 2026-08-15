# Design Patterns for AWS Step Functions
## SAP-C02 Exam Revision README

> **Course:** Design Patterns for AWS Step Functions  
> **Duration:** 1h 30m | **Level:** Advanced  
> **Exam target:** AWS Certified Solutions Architect – Professional (SAP-C02)

---

# 1. Core Concept

AWS Step Functions is a managed workflow orchestration service.

Think:

```text
Business Process
      ↓
Step Functions
      ↓
 ┌────┼────┬────┐
 ↓    ↓    ↓    ↓
Lambda ECS  AWS  Human/API
          Services
```

Step Functions is especially useful when an architecture requires:

- Multi-step workflows
- Service orchestration
- Error handling
- Retries
- Timeouts
- Parallel processing
- Conditional branching
- Long-running processes
- Human approval
- Auditable workflow state

**SAP-C02 mindset:**

> Use Step Functions when orchestration, state management, retries, branching, or workflow visibility are important.

---

# 2. Why Step Functions?

Without orchestration:

```text
Lambda A
   ↓
Lambda B
   ↓
Lambda C
   ↓
Lambda D
```

Application code must manage:

- State
- Retries
- Timeouts
- Errors
- Ordering
- Parallelism
- Recovery

With Step Functions:

```text
              Step Functions
                    |
        ┌───────────┼───────────┐
        ↓           ↓           ↓
     Lambda       ECS        AWS Service
```

The workflow definition manages orchestration.

---

# 3. State Machine

A Step Functions workflow is a **state machine**.

Typical states include:

- Task
- Choice
- Parallel
- Map
- Wait
- Pass
- Succeed
- Fail

Mental model:

```text
Start
 ↓
Task
 ↓
Choice
 ├── Yes → Task
 └── No  → Task
              ↓
            End
```

---

# 4. Task State

A Task state performs work.

Examples:

```text
Task
 ↓
Lambda
```

or:

```text
Task
 ↓
ECS/Fargate
```

or:

```text
Task
 ↓
AWS SDK integration
```

Use Task when an actual operation must be performed.

---

# 5. Choice State

Choice implements conditional business logic.

```text
Order
 ↓
Choice
 ├── Amount > $1000 → Approval
 ├── Amount > $100  → Standard
 └── Otherwise      → Basic
```

Use Choice instead of writing branching logic into Lambda when the workflow itself should make the decision.

---

# 6. Parallel State

Parallel runs independent branches concurrently.

```text
              Parallel
             /   |               ↓    ↓    ↓
         Task A Task B Task C
            \    |    /
             \   |   /
              Continue
```

Use when tasks are independent.

### Example

After customer registration:

```text
Parallel
 ├── Create account
 ├── Send welcome email
 └── Create analytics record
```

---

# 7. Map State

Map is used for iterating over a collection.

```text
Input list
   ↓
Map
 ├── Item 1 → Process
 ├── Item 2 → Process
 ├── Item 3 → Process
 └── Item N → Process
```

Typical use cases:

- Process files
- Process orders
- Process records
- Run the same operation for many items

---

# 8. Map vs Parallel

### Parallel

Known independent branches:

```text
A + B + C
```

### Map

Repeated processing over items:

```text
Item 1
Item 2
Item 3
...
Item N
```

**Exam shortcut:**

> Parallel = different branches  
> Map = same processing pattern repeated over a collection

---

# 9. Distributed Map

For very large datasets, Distributed Map can provide large-scale parallel processing.

Think:

```text
Large dataset
      ↓
Distributed Map
 ┌────┼────┬────┬────┐
 ↓    ↓    ↓    ↓    ↓
Worker Worker Worker Worker
```

Useful for large-scale data processing where conventional workflow iteration is insufficient.

---

# 10. Wait State

Wait pauses the workflow.

Examples:

- Wait for a fixed duration
- Wait until a timestamp
- Delay between workflow stages
- Polling patterns

Example:

```text
Submit job
 ↓
Wait
 ↓
Check status
 ↓
Choice
```

---

# 11. Synchronous vs Asynchronous Workflows

### Synchronous

User/application waits for result.

```text
Request
 ↓
Step Functions
 ↓
Processing
 ↓
Response
```

### Asynchronous

Start workflow and return immediately.

```text
Request
 ↓
Start execution
 ↓
Return job ID
```

The client can later check status or receive notification.

**SAP-C02 principle:**

> Long-running workloads should generally not keep clients waiting on synchronous connections.

---

# 12. Retry Pattern

Transient failures should be retried.

```text
Task
 ↓
Failure
 ↓
Retry
 ↓
Task
```

Useful for:

- Temporary throttling
- Transient network errors
- Temporary service unavailability

Do not blindly retry permanent failures.

---

# 13. Exponential Backoff

Retries can use increasing delays.

```text
Attempt 1 → wait
Attempt 2 → wait longer
Attempt 3 → wait even longer
```

This reduces pressure on a failing dependency.

---

# 14. Catch Pattern

When retries are exhausted, use Catch to route the failure.

```text
Task
 ↓
Retry
 ↓
Failure
 ↓
Catch
 ├── Compensation
 ├── Notification
 └── Fail
```

---

# 15. Retry vs Catch

### Retry

> Try the same operation again.

### Catch

> Handle the failure through another workflow path.

Example:

```text
Payment
 ↓
Retry transient failure
 ↓
Still failing
 ↓
Catch
 ↓
Notify support
```

---

# 16. Timeout Pattern

Always consider tasks that may hang.

```text
Task
 ↓
Timeout
 ↓
Catch
 ↓
Recovery
```

Timeouts prevent a workflow from waiting indefinitely.

---

# 17. Heartbeat

For long-running tasks, heartbeat mechanisms can help determine whether the worker is still responding.

Concept:

```text
Workflow
   ↓
Long-running worker
   ↑
Heartbeat
```

If the expected heartbeat is missing, the workflow can treat the task as failed according to its configuration.

---

# 18. Error Handling Pattern

A robust workflow:

```text
Task
 ↓
Retry transient errors
 ↓
Catch permanent/exhausted errors
 ↓
Compensation / notification
 ↓
Failure or recovery
```

**SAP-C02 Golden Rule:**

> Design the failure path, not just the happy path.

---

# 19. Compensation / Saga Pattern

For distributed transactions:

```text
Step A
 ↓
Step B
 ↓
Step C
 ↓
Failure
```

You may need:

```text
Undo C
 ↓
Undo B
 ↓
Undo A
```

This is the **Saga / compensation pattern**.

Useful when a traditional ACID transaction cannot span multiple distributed services.

---

# 20. Example: Order Workflow

```text
Order
 ↓
Validate
 ↓
Reserve inventory
 ↓
Charge payment
 ↓
Ship
 ↓
Complete
```

Failure:

```text
Payment fails
 ↓
Release inventory
 ↓
Cancel order
 ↓
Notify customer
```

Step Functions can orchestrate this compensation logic.

---

# 21. Human Approval Pattern

Step Functions can orchestrate workflows that require human intervention.

```text
Request
 ↓
Validate
 ↓
Approval required
 ↓
Wait
 ↓
Human approval
 ↓
Continue
```

Use for:

- Financial approvals
- Production changes
- Account actions
- Compliance workflows
- High-risk operations

---

# 22. Callback / External Worker Pattern

A workflow can pause while an external process completes.

Concept:

```text
Step Functions
 ↓
Start external work
 ↓
Wait
 ↓
External worker completes
 ↓
Callback
 ↓
Continue
```

Useful when work is performed outside the immediate Step Functions execution.

---

# 23. Job Polling Pattern

For services that expose job status:

```text
Start Job
 ↓
Wait
 ↓
Check Status
 ↓
Choice
 ├── Running → Wait → Check
 ├── Success → Continue
 └── Failed  → Error
```

Avoid aggressive polling.

Use appropriate wait intervals.

---

# 24. Callback vs Polling

### Callback

External system notifies the workflow.

```text
Start
 ↓
Wait
 ↓
Callback
```

Better when supported because unnecessary polling is avoided.

### Polling

Workflow repeatedly asks:

```text
"Are you done?"
```

Use when the external service does not support a suitable callback mechanism.

---

# 25. Fan-Out / Fan-In

A common architecture pattern:

```text
             Start
               ↓
            Fan-Out
          /    |             ↓     ↓     ↓
       Task   Task   Task
          \    |    /
            Fan-In
               ↓
             Result
```

Use Parallel or Map depending on the problem.

---

# 26. Sequential Workflow

Use when every step depends on the previous step.

```text
A
↓
B
↓
C
↓
D
```

Example:

```text
Validate
 ↓
Create
 ↓
Configure
 ↓
Activate
```

---

# 27. Event-Driven Workflow

Example:

```text
S3 Event
 ↓
EventBridge
 ↓
Step Functions
 ↓
Processing
```

Other triggers can include application events and scheduled events.

Benefits:

- Loose coupling
- Automatic triggering
- Scalable architecture

---

# 28. Scheduled Workflow

For recurring jobs:

```text
Schedule
 ↓
Step Functions
 ↓
Workflow
```

Examples:

- Daily reports
- Nightly processing
- Periodic cleanup
- Scheduled data processing

Use an appropriate scheduler/event service to start the state machine.

---

# 29. Step Functions + Lambda

Common serverless pattern:

```text
API
 ↓
Step Functions
 ↓
Lambda A
 ↓
Lambda B
 ↓
Lambda C
```

But do **not** automatically create one Lambda for every tiny step.

Where direct AWS service integration is available, it may remove unnecessary Lambda code.

---

# 30. Step Functions + Direct AWS Service Integration

Instead of:

```text
Step Functions
 ↓
Lambda
 ↓
AWS Service
```

sometimes use:

```text
Step Functions
 ↓
AWS Service
```

Benefits:

- Less code
- Less operational overhead
- Fewer components
- Potentially lower cost
- Clearer workflow

**SAP-C02 clue:**

> If Step Functions can directly orchestrate the required AWS service, avoid an unnecessary Lambda wrapper.

---

# 31. AWS SDK Integrations

Step Functions can integrate with AWS services through service integrations.

Examples may include:

- Lambda
- ECS
- Batch
- DynamoDB
- SNS
- SQS
- EventBridge
- AWS Glue
- Amazon EMR
- AWS SDK integrations

Always verify the exact integration and execution semantics for the service in the current AWS documentation.

---

# 32. Standard vs Express Workflows

This is a major SAP-C02 topic.

## Standard Workflows

Think:

```text
Long-running
Durable
Auditable
Exactly-once workflow execution model
```

Use for:

- Business workflows
- Long-running orchestration
- Human approval
- Critical transactions
- Auditable processes

## Express Workflows

Think:

```text
High-volume
Short-duration
Event processing
```

Use for:

- High-throughput workloads
- Short-running workflows
- Event processing
- Streaming/data processing patterns

**Exam warning:**

Do not choose solely based on price. Look at:

- Duration
- Throughput
- Execution semantics
- Monitoring/audit requirements
- Use case

---

# 33. Standard vs Express Quick Table

| Requirement | Better fit |
|---|---|
| Long-running workflow | Standard |
| Human approval | Standard |
| Business-critical orchestration | Standard |
| Detailed execution history | Standard |
| Very high event volume | Express |
| Short-running processing | Express |
| Event/data processing | Express |

Always confirm current service limits and feature differences before production design.

---

# 34. Idempotency

Retries can cause duplicate operations.

Example:

```text
Charge customer
 ↓
Timeout
 ↓
Retry
 ↓
Charge again?
```

Danger: duplicate charge.

Use idempotency mechanisms where supported.

Concept:

```text
Request ID
 ↓
Check if already processed
 ↓
Process only once
```

**SAP-C02 Golden Rule:**

> Retry + non-idempotent operation = potential duplicate side effect.

---

# 35. Exactly-Once vs At-Least-Once Thinking

Do not assume every downstream operation is magically exactly once.

Consider:

- API semantics
- Retry behavior
- Network timeouts
- Duplicate requests
- Idempotency keys

The architecture must protect business operations from duplicate effects.

---

# 36. Distributed Transaction Pattern

Avoid trying to create a giant distributed ACID transaction across unrelated services.

Prefer:

```text
Step Functions
 +
Saga
 +
Compensation
```

Example:

```text
Reserve stock
 ↓
Charge payment
 ↓
Create shipment
```

If shipment fails:

```text
Refund payment
 ↓
Release stock
```

---

# 37. Workflow Versioning

Treat workflow definitions as code.

Use:

- Git
- Infrastructure as Code
- Code review
- Automated testing
- CI/CD
- Version control

Deployment:

```text
Code
 ↓
Test
 ↓
Deploy
 ↓
Validate
 ↓
Rollback if required
```

---

# 38. Security

Step Functions should follow least privilege.

Architecture:

```text
Step Functions IAM Role
       ↓
Only required permissions
       ↓
AWS services
```

Do not give the workflow broad administrator permissions.

---

# 39. IAM Role Separation

Prefer:

```text
State Machine Role
       ↓
Orchestration permissions
```

and appropriate service execution roles:

```text
Lambda Execution Role
ECS Task Role
Batch Role
```

Each component receives only the permissions it requires.

---

# 40. Data Protection

Sensitive data should be handled carefully.

Consider:

- Encryption at rest
- Encryption in transit
- IAM
- KMS
- Secrets Manager
- Parameter Store
- Data minimization

Do not put secrets directly into workflow definitions.

---

# 41. Secrets

Bad:

```text
Password = "secret123"
```

Better:

```text
Step Functions
 ↓
Secrets Manager / Parameter Store
 ↓
Retrieve securely
```

Use IAM permissions to control access.

---

# 42. Logging and Monitoring

Monitor:

- Executions
- Failures
- Duration
- State transitions
- Retries
- Timeouts
- Throttling
- Downstream errors

Integrate with appropriate AWS observability services.

---

# 43. Observability Pattern

```text
Step Functions
      |
      ├── Execution history
      |
      ├── Logs
      |
      ├── Metrics
      |
      └── Tracing where supported
```

For complex workflows:

```text
Trace:
API → Step Functions → Lambda → DynamoDB
```

This makes troubleshooting easier.

---

# 44. Dead-Letter / Failure Handling

For asynchronous workflows:

```text
Failure
 ↓
Retry
 ↓
Catch
 ↓
Failure destination / notification
```

Use appropriate queues, events, or notification mechanisms depending on the architecture.

---

# 45. Backpressure

If downstream systems cannot handle incoming traffic:

```text
Producer
 ↓
Queue
 ↓
Step Functions / Workers
 ↓
Slow dependency
```

The queue absorbs bursts.

Do not let the workflow overwhelm a constrained downstream service.

---

# 46. Throttling

If a service has API limits:

```text
High request rate
 ↓
Throttle
 ↓
Retry with backoff
```

Control concurrency where appropriate.

Map workflows are particularly important when processing large collections.

---

# 47. Concurrency Control

Suppose:

```text
1 million records
```

Do not automatically execute:

```text
1 million concurrent operations
```

Instead:

```text
Large dataset
 ↓
Controlled Map concurrency
 ↓
Workers
```

This protects downstream services and controls cost.

---

# 48. Cost Optimization

Use Step Functions efficiently.

Consider:

- Standard vs Express
- State transition volume
- Execution duration
- Lambda invocation count
- Unnecessary polling
- Unnecessary retries
- Concurrency
- Direct service integrations

**Important:**

> Cost optimization must not break reliability or business requirements.

---

# 49. Polling Cost Trap

Bad:

```text
Start job
 ↓
Wait 1 second
 ↓
Check
 ↓
Wait 1 second
 ↓
Check
 ↓
...
```

Better:

```text
Start job
 ↓
Appropriate wait
 ↓
Check
```

Or use callback/event notification when available.

---

# 50. Resilience Pattern

A resilient workflow:

```text
Task
 ↓
Timeout
 ↓
Retry transient error
 ↓
Catch permanent error
 ↓
Compensate
 ↓
Notify
```

This is much stronger than:

```text
Task
 ↓
Failure
 ↓
Entire system breaks
```

---

# 51. Workflow With Multiple Dependencies

Example:

```text
Order
 ↓
Inventory
 ↓
Payment
 ↓
Shipping
```

Each dependency can fail independently.

Design:

```text
Inventory failure → retry / compensate
Payment failure   → cancel order
Shipping failure  → refund / release inventory
```

---

# 52. Step Functions for Human Workflows

Example:

```text
Employee request
 ↓
Manager approval
 ↓
Security approval
 ↓
Provision resource
 ↓
Notify employee
```

Step Functions is useful because the workflow state can persist while waiting.

---

# 53. Step Functions + SQS

Pattern:

```text
Application
 ↓
SQS
 ↓
Worker
 ↓
Step Functions / Processing
```

Use queues when:

- Traffic is bursty
- Consumers are slower
- Loose coupling is required
- Retry isolation is needed

---

# 54. Step Functions + EventBridge

Pattern:

```text
AWS event
 ↓
EventBridge
 ↓
Step Functions
 ↓
Business workflow
```

Good for event-driven orchestration.

---

# 55. Step Functions + API Gateway

Common pattern:

```text
Client
 ↓
API Gateway
 ↓
Step Functions
 ↓
Workflow
```

For long-running workflows:

```text
Client
 ↓
Start execution
 ↓
Job ID
 ↓
Status endpoint
```

Do not make clients wait unnecessarily for long-running processes.

---

# 56. Step Functions + DynamoDB

Common patterns:

```text
Workflow
 ↓
DynamoDB
```

Use for:

- State persistence outside workflow
- Business records
- Idempotency records
- Status tracking
- Metadata

Do not confuse application business state with the workflow's orchestration state.

---

# 57. Step Functions + ECS/Fargate

Useful for longer-running/containerized workloads:

```text
Step Functions
 ↓
ECS/Fargate Task
 ↓
Complete
 ↓
Continue workflow
```

Good when Lambda runtime/resource characteristics are not appropriate.

---

# 58. Step Functions + AWS Batch

For compute-intensive batch workloads:

```text
Step Functions
 ↓
AWS Batch
 ↓
Large-scale compute
 ↓
Result
```

Step Functions orchestrates; Batch provides compute scheduling.

---

# 59. Step Functions + Glue / Data Processing

Example:

```text
Start
 ↓
Glue job
 ↓
Validate
 ↓
Transform
 ↓
Load
 ↓
Notify
```

Use Step Functions for orchestration rather than putting the entire pipeline into one custom application.

---

# 60. Workflow Choice Matrix

| Requirement | Pattern |
|---|---|
| Sequential steps | Chain of Task states |
| Conditional flow | Choice |
| Independent branches | Parallel |
| Process collection | Map |
| Huge collection | Distributed Map |
| Delay | Wait |
| Transient failure | Retry |
| Permanent/exhausted failure | Catch |
| Human decision | Wait/callback/approval |
| External job | Polling or callback |
| Distributed transaction | Saga/compensation |
| Bursty workload | Queue |
| Event trigger | EventBridge |
| Serverless processing | Lambda integration |
| Container processing | ECS/Fargate |
| Batch compute | AWS Batch |

---

# 61. SAP-C02 Scenario Recognition

### Scenario:

> A company needs to execute five independent operations simultaneously after an order is created.

Answer pattern:

```text
Parallel
```

---

### Scenario:

> Process every object in a large list using the same transformation.

Answer pattern:

```text
Map
```

---

### Scenario:

> Process millions of records with high concurrency.

Think:

```text
Distributed Map
+
controlled concurrency
```

---

### Scenario:

> A payment API sometimes returns temporary throttling errors.

Think:

```text
Retry
+
backoff
```

---

### Scenario:

> Payment fails permanently after retries.

Think:

```text
Catch
+
compensation
```

---

### Scenario:

> A workflow must wait for a manager's approval.

Think:

```text
Wait
+
callback/approval pattern
```

---

### Scenario:

> An external job takes 30 minutes.

Think:

```text
Asynchronous workflow
+
polling or callback
```

---

### Scenario:

> Three distributed services must behave like one business transaction.

Think:

```text
Saga
+
compensation
```

---

# 62. Common Exam Traps

## Trap 1: Lambda for every workflow step

Not always best.

Use direct service integrations where appropriate.

---

## Trap 2: Parallel for collection processing

Usually incorrect.

Use Map when processing items in a collection.

---

## Trap 3: Map for unrelated branches

Usually incorrect.

Use Parallel for distinct independent branches.

---

## Trap 4: Retry every error

Wrong.

Retry transient failures, not permanent validation errors.

---

## Trap 5: No idempotency

Dangerous when retries can create duplicate business operations.

---

## Trap 6: Synchronous long-running workflow

Often poor architecture.

Use asynchronous execution.

---

## Trap 7: Poll every second

Can create unnecessary cost and load.

Use suitable wait intervals or callbacks.

---

## Trap 8: Admin permissions

Violates least privilege.

---

## Trap 9: Secrets in state machine definition

Bad security practice.

Use managed secret storage.

---

## Trap 10: Distributed ACID transaction

Often inappropriate across independent services.

Use Saga/compensation where suitable.

---

## Trap 11: Unlimited Map concurrency

Can overwhelm downstream systems.

Control concurrency.

---

## Trap 12: Ignore failure path

SAP-C02 frequently tests resilience.

Always ask:

> "What happens when this step fails?"

---

# 63. SAP-C02 Golden Rules

1. Step Functions = workflow orchestration.
2. Use Task for work.
3. Use Choice for conditional branching.
4. Use Parallel for independent branches.
5. Use Map for collection processing.
6. Use Distributed Map for very large-scale collection processing where appropriate.
7. Use Wait for delays.
8. Retry transient failures.
9. Catch failures that cannot be recovered by retry.
10. Use exponential backoff for transient dependency failures.
11. Design explicit compensation for distributed transactions.
12. Make retryable business operations idempotent.
13. Avoid unnecessary Lambda wrappers.
14. Prefer direct AWS service integrations where appropriate.
15. Use Standard for durable/long-running business workflows.
16. Use Express for high-volume, short-duration workflow patterns where its semantics fit.
17. Use queues to absorb bursts.
18. Control concurrency to protect downstream systems.
19. Prefer callback/event notification over aggressive polling when supported.
20. Use asynchronous execution for long-running operations.
21. Use human approval for high-risk business actions.
22. Apply least privilege to state-machine IAM roles.
23. Keep secrets out of workflow definitions.
24. Use Secrets Manager or Parameter Store for secrets/configuration as appropriate.
25. Encrypt sensitive data.
26. Monitor workflow failures and latency.
27. Treat workflow definitions as code.
28. Use CI/CD and version control.
29. Design for timeouts.
30. Design for downstream throttling.
31. Do not assume retries are harmless.
32. Do not create distributed ACID transactions unnecessarily.
33. Use Saga/compensation for distributed business transactions.
34. Use EventBridge for suitable event-driven triggers.
35. Use SQS for decoupling and buffering.
36. Use ECS/Fargate when container execution is appropriate.
37. Use AWS Batch for batch compute orchestration.
38. Use DynamoDB for appropriate application data/idempotency/status use cases.
39. Use workflow state for orchestration, not as a replacement for every database.
40. Always design the failure path.

---

# 64. 30-Second Revision

```text
STEP FUNCTIONS
       |
       ├── Task
       │    → Do work
       |
       ├── Choice
       │    → Conditional branch
       |
       ├── Parallel
       │    → Independent branches
       |
       ├── Map
       │    → Iterate collection
       |
       ├── Wait
       │    → Delay
       |
       ├── Retry
       │    → Transient failure
       |
       └── Catch
            → Failure handling
```

Remember:

```text
Parallel = different branches
Map      = repeated processing
Retry    = try again
Catch    = handle failure
Wait     = pause
Choice   = decide
Task     = execute
```

---

# 65. Ultimate SAP-C02 Mental Model

When you see a Step Functions question:

```text
1. Is this really a workflow/orchestration problem?
                  ↓
2. Sequential, conditional, parallel, or collection?
                  ↓
3. What happens when a dependency fails?
                  ↓
4. Can the operation be retried safely?
                  ↓
5. Is it idempotent?
                  ↓
6. Is the workload long-running?
                  ↓
7. Standard or Express?
                  ↓
8. Can direct AWS integration remove Lambda?
                  ↓
9. Does the workflow need human approval?
                  ↓
10. How will bursts/throttling be controlled?
                  ↓
11. What IAM permissions are required?
                  ↓
12. How will the workflow be monitored and recovered?
```

---

# 66. Final Exam Formula

```text
Correct State Pattern
        +
Failure Handling
        +
Idempotency
        +
Least Privilege
        +
Controlled Concurrency
        +
Appropriate Workflow Type
        +
Observability
        +
Cost Awareness
        =
Strong SAP-C02 Step Functions Architecture
```

## One-line memory aid

> **Step Functions = orchestrate; Lambda/ECS/Batch = execute; Choice = decide; Parallel = branch; Map = repeat; Retry = recover; Catch = handle; Wait/Callback = pause; Saga = compensate.**
