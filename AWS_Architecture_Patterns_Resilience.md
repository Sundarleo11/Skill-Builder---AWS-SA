# AWS Architecture Patterns – Resilience
## SAP-C02 Exam Revision README

> **Target exam:** AWS Certified Solutions Architect – Professional (SAP-C02)
>
> **Course:** Architecture Patterns - Resilience
>
> **Course duration:** 40 minutes
>
> **Level:** Advanced
>
> **Focus:** Resilient architecture, failure isolation, high availability, disaster recovery, retries, queues, graceful degradation, observability, and applying established reliability patterns to Generative AI workloads.

---

# 1. Exam Focus

SAP-C02 resilience questions are usually scenario-based.

The exam expects you to choose architectures that:

- Continue operating during component failures
- Automatically recover from failures
- Reduce blast radius
- Avoid single points of failure
- Handle traffic spikes
- Protect downstream services
- Recover from regional failures when required
- Meet stated RTO/RPO requirements
- Degrade gracefully instead of completely failing

For GenAI systems, the same reliability principles apply to:

```text
User
 ↓
API / Application
 ↓
Orchestration
 ↓
LLM / Foundation Model
 ↓
Tools / APIs
 ↓
RAG
 ↓
Vector / Data Stores
 ↓
Response
```

Every component can fail independently.

---

# 2. Reliability vs Resilience vs Availability

## Availability

How often the system is operational.

```text
Availability =
Uptime / Total Time
```

## Reliability

Ability to perform correctly over a period of time.

## Resilience

Ability to:

```text
Withstand failure
      +
Recover quickly
      +
Continue providing service
```

### SAP-C02 mindset

A highly available architecture is not automatically fully resilient.

Resilience also requires:

- Failure detection
- Recovery
- Isolation
- Automation
- Graceful degradation

---

# 3. Core Resilience Principle

> **Assume components will fail. Design the system so that failure of one component does not cause total system failure.**

Think:

```text
Failure is inevitable
        ↓
Detect
        ↓
Isolate
        ↓
Recover
        ↓
Continue serving
```

---

# 4. Eliminate Single Points of Failure

Bad architecture:

```text
Users
  ↓
One Server
  ↓
One Database
```

If the server fails:

```text
TOTAL OUTAGE
```

Better:

```text
Users
  ↓
Load Balancer
  ↓
┌─────────────┐
│ AZ-A        │
│ Application │
└─────────────┘
        +
┌─────────────┐
│ AZ-B        │
│ Application │
└─────────────┘
```

The system can continue when one AZ/component fails.

---

# 5. Multi-AZ

Multi-AZ protects against:

- Availability Zone failure
- Instance failure
- Local infrastructure problems

Typical pattern:

```text
Region
│
├── AZ-A
│    └── Application
│
└── AZ-B
     └── Application
```

### SAP-C02 clue

If the requirement is:

> High availability within one AWS Region

Think:

**Multi-AZ**

---

# 6. Multi-Region

Multi-Region provides resilience against:

- Regional outage
- Large-scale regional infrastructure failure
- Regional disaster

Example:

```text
Region A
   │
   └── Application

        ↕

Region B
   │
   └── Application
```

### Important

Multi-Region is more complex and expensive than Multi-AZ.

Do not choose Multi-Region unless the requirements justify it.

---

# 7. Failure Domains

A failure domain is a boundary within which a failure can occur.

Examples:

```text
Instance
AZ
Region
Account
Service
Network
```

Good architecture limits the impact of failures.

### Principle

> **Reduce the blast radius.**

---

# 8. Blast Radius

Blast radius = how much of the system is affected by one failure.

Bad:

```text
One component
     ↓
Entire application
```

Better:

```text
Component failure
     ↓
One workload partition
     ↓
Other workloads continue
```

Examples:

- Separate accounts
- Separate OUs
- Separate queues
- Separate AZs
- Cell-based architecture
- Tenant isolation

---

# 9. Cell-Based Architecture

A cell is an isolated unit of the application.

Example:

```text
Users
 │
 ├── Cell A
 │
 ├── Cell B
 │
 └── Cell C
```

If Cell B fails:

```text
Cell A → Works
Cell B → Failed
Cell C → Works
```

This dramatically reduces blast radius.

### SAP-C02 clue

If the question emphasizes:

> Very large scale + failure isolation + prevent one failure from affecting all users

Think:

**Cell-based architecture**

---

# 10. Stateless Application Design

Stateless applications are easier to scale and recover.

Instead of storing session state locally:

```text
Server
 ↓
Local Session
```

Store state externally:

```text
Application
 ↓
Shared State Store
```

Examples:

- DynamoDB
- ElastiCache
- S3
- Database

Then instances can be replaced without losing session information.

---

# 11. Horizontal Scaling

Horizontal scaling:

```text
1 instance
 ↓
2 instances
 ↓
10 instances
```

Instead of making one server larger.

Common AWS pattern:

```text
ALB
 ↓
Auto Scaling Group
 ↓
Multiple AZs
```

Benefits:

- Fault tolerance
- Elasticity
- Load distribution
- Instance replacement

---

# 12. Auto Scaling as Resilience

Auto Scaling can:

- Replace unhealthy instances
- Add capacity
- Remove unhealthy capacity
- Respond to demand

Pattern:

```text
Health Check
    ↓
Unhealthy Instance
    ↓
Terminate
    ↓
Launch Replacement
```

This provides automated recovery.

---

# 13. Health Checks

Health checks detect unhealthy components.

Examples:

- ALB health checks
- Route 53 health checks
- ECS health checks
- Kubernetes probes
- Application-level checks

### Important

A process being alive does not necessarily mean the application is healthy.

Prefer meaningful health checks.

---

# 14. Self-Healing Architecture

Self-healing means the system automatically detects and recovers from failures.

Example:

```text
Failure
  ↓
Detection
  ↓
Automation
  ↓
Replacement / Recovery
  ↓
Healthy State
```

AWS examples:

- Auto Scaling
- ECS service scheduler
- EKS controllers
- Route 53 health checks
- AWS Systems Manager automation
- EventBridge automation

---

# 15. Timeouts

Never allow a dependency to hang indefinitely.

Bad:

```text
Application
   ↓
Dependency
   ↓
Wait forever
```

Better:

```text
Application
   ↓
Dependency
   ↓
Timeout
   ↓
Fallback / Error
```

Timeouts prevent resource exhaustion and cascading failures.

---

# 16. Retries

Transient failures can sometimes be recovered through retries.

Examples:

- Temporary network failure
- Service throttling
- Temporary dependency failure

Pattern:

```text
Request
 ↓
Failure
 ↓
Retry
 ↓
Success
```

But retries must be controlled.

---

# 17. Exponential Backoff

Instead of retrying immediately:

```text
Retry 1 → short delay
Retry 2 → longer delay
Retry 3 → longer delay
```

Example:

```text
1s
2s
4s
8s
```

Benefits:

- Reduces pressure on failing services
- Avoids retry storms
- Helps recover from temporary failures

---

# 18. Jitter

If thousands of clients retry at exactly the same time:

```text
Client A ─┐
Client B ─┤
Client C ─┼──→ Dependency
Client D ─┤
Client E ─┘
```

This can create a retry storm.

Jitter randomizes retry timing.

```text
Client A → 1.2 sec
Client B → 1.8 sec
Client C → 1.4 sec
Client D → 2.1 sec
```

### SAP-C02 clue

**Exponential backoff + jitter** helps prevent synchronized retries.

---

# 19. Retry Storm

A retry storm occurs when many clients retry a failed dependency simultaneously.

Result:

```text
Dependency fails
      ↓
Clients retry
      ↓
Traffic increases
      ↓
Dependency becomes more overloaded
      ↓
More failures
      ↓
More retries
```

Solution:

```text
Backoff
+
Jitter
+
Retry limits
+
Circuit breaker
```

---

# 20. Circuit Breaker

Circuit breakers prevent repeated requests to a failing dependency.

Concept:

```text
Normal
  ↓
Failures increase
  ↓
Circuit OPEN
  ↓
Stop requests temporarily
  ↓
Dependency recovers
  ↓
Circuit HALF-OPEN
  ↓
Test request
  ↓
Healthy → CLOSED
```

Benefits:

- Prevents cascading failure
- Protects downstream service
- Reduces unnecessary load

---

# 21. Graceful Degradation

When a dependency fails, provide reduced functionality rather than total failure.

Example:

```text
Primary Recommendation Service
          ↓
        FAILS
          ↓
Cached Recommendations
          ↓
Continue serving users
```

For GenAI:

```text
Primary LLM
    ↓
Unavailable
    ↓
Fallback model / cached response / reduced feature
```

---

# 22. Failover

Failover means switching to an alternative healthy resource.

Examples:

```text
Primary DB
    ↓
Failure
    ↓
Standby DB
```

or:

```text
Region A
    ↓
Failure
    ↓
Region B
```

---

# 23. Active-Passive

Primary environment serves traffic.

Secondary environment waits for failover.

```text
Users
  ↓
Primary
  ↓
Secondary
```

During failure:

```text
Users
  ↓
Secondary
```

Advantages:

- Lower cost than active-active
- Easier operational model

Disadvantages:

- Failover time
- Secondary may not have full active capacity

---

# 24. Active-Active

Both environments serve traffic.

```text
Users
  ↓
Traffic Routing
 ┌───────┴───────┐
 ↓               ↓
Region A       Region B
```

Advantages:

- Fast failover
- Both regions provide capacity
- Better utilization

Disadvantages:

- Higher cost
- More complex data synchronization
- More complex operations

---

# 25. Queue-Based Resilience

Queues decouple producers and consumers.

Without queue:

```text
Producer
   ↓
Consumer
```

If consumer fails:

```text
Producer → Failure
```

With queue:

```text
Producer
   ↓
SQS
   ↓
Consumer
```

If consumer is temporarily unavailable:

```text
Messages remain in queue
```

---

# 26. Buffering

Queues act as shock absorbers.

Example:

```text
Traffic Spike
      ↓
SQS
      ↓
Workers process gradually
```

Benefits:

- Smooth traffic spikes
- Decouple services
- Prevent overload
- Enable retries
- Improve resilience

---

# 27. Dead-Letter Queues

Messages that repeatedly fail processing can be moved to a DLQ.

```text
SQS
 ↓
Consumer
 ↓
Processing Failure
 ↓
Retry
 ↓
Retry
 ↓
DLQ
```

Benefits:

- Prevent poison messages from blocking processing
- Enable investigation
- Preserve failed messages

---

# 28. Idempotency

An operation is idempotent when repeating it produces the same intended result.

This is extremely important when retries are used.

Example:

```text
Payment Request
     ↓
Timeout
     ↓
Retry
```

Without idempotency:

```text
Customer charged twice
```

With idempotency:

```text
Same request ID
     ↓
Only one successful operation
```

### SAP-C02 rule

> **Retries + non-idempotent operation = potential duplicate side effects.**

---

# 29. Transactional Outbox / Event Reliability

When an application must reliably update state and publish an event, transaction coordination matters.

Concept:

```text
Application
   ↓
Database Transaction
   ↓
Outbox
   ↓
Event Processing
```

This can reduce the risk of:

```text
Database updated
BUT
Event never published
```

---

# 30. GenAI Workload Architecture

A production GenAI application may look like:

```text
User
 ↓
CloudFront / WAF
 ↓
API Gateway / ALB
 ↓
Application / Orchestrator
 ↓
┌───────────────┬────────────────┐
│               │                │
LLM          RAG Pipeline     Tools/APIs
│               │                │
│          Vector Store          │
│               │                │
└───────────────┴────────────────┘
 ↓
Response
```

Every dependency must have a resilience strategy.

---

# 31. GenAI Failure Domains

Potential failure points:

```text
Frontend
API
Application
LLM endpoint
Model quota
Vector database
Embedding service
Object storage
Database
External API
Network
Region
```

Do not assume the model is the only failure point.

---

# 32. LLM / Model Resilience

Potential model-related failures:

- Throttling
- Quota exhaustion
- Service interruption
- Latency spikes
- Model availability issues
- Invalid responses
- Dependency timeout

Possible strategies:

```text
Retry with backoff
+
Jitter
+
Timeout
+
Circuit breaker
+
Fallback model
+
Graceful degradation
```

---

# 33. Model Fallback

Example:

```text
Primary Model
      ↓
Unavailable / Throttled
      ↓
Fallback Model
      ↓
Response
```

Possible reasons:

- Availability
- Quota
- Cost
- Latency
- Capacity

### Important

Fallback should preserve acceptable business behavior and quality requirements.

---

# 34. Multi-Model Strategy

A resilient GenAI system can use different models for different requirements.

Example:

```text
High-quality requests
       ↓
Large model

Simple requests
       ↓
Smaller / faster model

Primary model failure
       ↓
Fallback model
```

This can improve:

- Availability
- Cost efficiency
- Latency
- Capacity management

---

# 35. RAG Resilience

RAG architecture:

```text
User
 ↓
Retriever
 ↓
Vector Store
 ↓
Documents
 ↓
LLM
```

Potential failures:

```text
Vector DB unavailable
Embedding service unavailable
S3 unavailable
LLM unavailable
```

Use:

- Caching
- Retries
- Timeouts
- Circuit breakers
- Fallback retrieval
- Cached documents
- Graceful degradation

---

# 36. Vector Store Resilience

For critical workloads:

```text
Vector Store
     ↓
Replication / Backup
     ↓
Recovery
```

Consider:

- Multi-AZ deployment
- Replication
- Backup
- Restore testing
- Capacity scaling
- Monitoring

Do not assume a vector database is automatically resilient.

---

# 37. Caching

Caching can improve both performance and resilience.

Example:

```text
User
 ↓
Cache
 ↓
Cached Result
```

If the backend is temporarily unavailable:

```text
Backend Failure
      ↓
Cached Data
      ↓
Continue serving
```

GenAI examples:

- Frequently requested answers
- Embeddings
- Retrieval results
- Prompt templates
- Model responses where business requirements allow

---

# 38. Streaming Responses

GenAI applications often use streaming to improve perceived latency.

```text
User
 ↓
LLM
 ↓
Token Stream
 ↓
User
```

Resilience considerations:

- Connection interruption
- Timeout
- Partial responses
- Client reconnect
- Cancellation
- Backpressure

The application should handle incomplete generations gracefully.

---

# 39. Token / Request Limits

GenAI services can have limits involving:

- Requests per minute
- Tokens per minute
- Concurrent requests
- Model-specific quotas

At scale:

```text
Traffic Spike
     ↓
Quota
     ↓
Throttling
```

Mitigate using:

- Backoff
- Jitter
- Queues
- Rate limiting
- Load shedding
- Model fallback
- Quota management

---

# 40. Rate Limiting

Protect downstream services by controlling incoming requests.

```text
Clients
  ↓
API Gateway
  ↓
Rate Limit
  ↓
Application
```

Benefits:

- Protect model endpoints
- Prevent overload
- Improve fairness
- Control costs

---

# 41. Load Shedding

When the system is overloaded, intentionally reject lower-priority work to protect critical operations.

Example:

```text
System overloaded
      ↓
Reject low-priority requests
      ↓
Protect critical requests
```

This is preferable to allowing the entire system to collapse.

---

# 42. Backpressure

Backpressure prevents producers from overwhelming consumers.

Example:

```text
Fast Producer
      ↓
Queue
      ↓
Slow Consumer
```

The queue absorbs temporary differences.

For GenAI workloads, this can help when:

```text
Incoming Requests > Model Processing Capacity
```

---

# 43. Observability

A resilient system must detect failures quickly.

Three major areas:

```text
Metrics
Logs
Traces
```

---

# 44. Metrics

Useful metrics include:

- Request latency
- Error rate
- Throttling
- Queue depth
- Model invocation count
- Token usage
- CPU/memory
- Database latency
- Cache hit ratio

### GenAI-specific

Consider:

- Time to first token
- Generation latency
- Token consumption
- Model error rate
- Retrieval latency
- Retrieval failure rate

---

# 45. Logs

Logs should capture useful diagnostic information without exposing sensitive data.

Examples:

```text
Request ID
Correlation ID
Model used
Latency
Error type
Retry count
Fallback invoked
```

Avoid logging:

- Secrets
- API keys
- Sensitive user data
- Unnecessary prompt contents

---

# 46. Distributed Tracing

For complex GenAI applications:

```text
User Request
   ↓
API
   ↓
Orchestrator
   ↓
Retriever
   ↓
Vector DB
   ↓
LLM
   ↓
Tool
```

Distributed tracing helps identify where latency/failure occurred.

---

# 47. Fault Injection / Resilience Testing

Do not assume resilience because the architecture diagram looks correct.

Test:

```text
AZ failure
Service failure
Database failure
LLM throttling
Network failure
Queue backlog
Dependency timeout
Region failure
```

Verify:

```text
Detection
+
Failover
+
Recovery
+
RTO
```

---

# 48. Disaster Recovery

Disaster recovery focuses on restoring service after major failures.

Key metrics:

## RTO

Recovery Time Objective:

> How quickly must the system recover?

## RPO

Recovery Point Objective:

> How much data loss is acceptable?

### Example

```text
RTO = 15 minutes
RPO = 5 minutes
```

The architecture must meet both requirements.

---

# 49. DR Strategies

| Strategy | Cost | Recovery Speed |
|---|---:|---:|
| Backup & Restore | Low | Slow |
| Pilot Light | Medium | Faster |
| Warm Standby | Higher | Fast |
| Active-Active | Highest | Very Fast |

### SAP-C02 shortcut

```text
Lowest cost
→ Backup & Restore

Very fast recovery
→ Warm Standby / Active-Active

Near-zero downtime
→ Active-Active
```

Always validate against the exact RTO/RPO and workload requirements.

---

# 50. Backup and Restore

Architecture:

```text
Primary
  ↓
Backup
  ↓
Disaster
  ↓
Restore
```

Best when:

- Cost is important
- RTO can tolerate longer recovery
- Workload is not mission-critical

---

# 51. Pilot Light

Maintain minimal infrastructure in the recovery environment.

```text
Primary
   ↓
Minimal DR Infrastructure
```

During disaster:

```text
Scale DR
   ↓
Restore / Activate
   ↓
Serve traffic
```

---

# 52. Warm Standby

A scaled-down but operational environment continuously runs.

```text
Primary Region
     +
Warm Standby Region
```

During failure:

```text
Scale up
 ↓
Route traffic
```

Faster recovery than pilot light.

---

# 53. Active-Active DR

Both environments are fully operational.

```text
Users
  ↓
Global Routing
 ┌───────┴───────┐
 ↓               ↓
Region A       Region B
```

Best for very demanding:

- Availability
- RTO
- Global workloads

But more complex and expensive.

---

# 54. Route 53 Failover

A common DNS-based failover pattern:

```text
Route 53
  ↓
Health Check
  ↓
Primary
```

If primary fails:

```text
Route 53
  ↓
Secondary
```

For global workloads, routing strategies can include:

- Failover
- Weighted
- Latency-based
- Geolocation
- Geoproximity

---

# 55. Global Accelerator

AWS Global Accelerator can improve global application availability and performance.

Concept:

```text
Users
 ↓
AWS Global Accelerator
 ↓
Healthy AWS endpoints
```

It can route users toward healthy regional endpoints.

### SAP-C02 clue

If the requirement involves:

- Global users
- Static anycast IPs
- Fast failover
- Regional endpoint health

Think:

**AWS Global Accelerator**

---

# 56. Database Resilience

Common patterns:

```text
Multi-AZ
Read Replicas
Replication
Backups
Point-in-Time Recovery
Global Tables
Cross-Region Replication
```

Choose based on:

- RTO
- RPO
- Read/write requirements
- Regional requirements
- Cost

---

# 57. DynamoDB Resilience

Important patterns:

```text
DynamoDB
  ↓
Multi-AZ managed service
```

For multi-Region active-active requirements:

```text
DynamoDB Global Tables
```

Global Tables can provide multi-Region replication for DynamoDB workloads.

---

# 58. S3 Resilience

S3 is highly durable and designed for high availability.

For stronger cross-Region requirements:

```text
S3
 ↓
Cross-Region Replication
 ↓
Secondary Region
```

Use replication when business requirements require independent regional copies.

---

# 59. Dependency Isolation

If one dependency fails, isolate it.

Example:

```text
Application
 ├── Payment Service
 ├── Recommendation Service
 └── Notification Service
```

If Notification fails:

```text
Payment → Continue
Recommendation → Continue
Notification → Degraded
```

Avoid making every feature synchronously dependent on every other feature.

---

# 60. Asynchronous Architecture

Synchronous:

```text
A → B → C → D
```

If D fails:

```text
A may fail
```

Asynchronous:

```text
A → Queue
      ↓
      B
      ↓
    Queue
      ↓
      C
```

Failures are isolated.

---

# 61. Event-Driven Resilience

Event-driven systems reduce tight coupling.

Example:

```text
Order Service
      ↓
EventBridge / SQS
      ↓
Inventory
      ↓
Notification
```

Consumers can recover independently.

---

# 62. Graceful Failure for GenAI

Suppose the LLM is unavailable.

Bad:

```text
LLM failure
 ↓
Entire application unavailable
```

Better:

```text
LLM failure
 ↓
Fallback
 ├── Cached answer
 ├── Smaller model
 ├── Search-only experience
 └── Human escalation
```

The appropriate fallback depends on business requirements.

---

# 63. Human-in-the-Loop Fallback

For high-stakes GenAI applications:

```text
AI
 ↓
Confidence / Validation
 ↓
Low confidence or failure
 ↓
Human Review
```

This improves resilience and safety for critical decisions.

---

# 64. Idempotency + Queues

This combination is very important.

```text
Producer
 ↓
SQS
 ↓
Consumer
 ↓
Processing
```

Messages can be delivered more than once depending on the delivery model.

Therefore:

```text
Consumer
 ↓
Idempotency check
 ↓
Process once
```

Avoid duplicate side effects.

---

# 65. Queue Poison Message

A malformed message can repeatedly fail.

Without DLQ:

```text
Message
 ↓
Fail
 ↓
Retry
 ↓
Fail
 ↓
Retry
```

With DLQ:

```text
Message
 ↓
Retries
 ↓
DLQ
 ↓
Investigation
```

This protects overall processing.

---

# 66. Resilience Testing Checklist

Test:

- [ ] Instance failure
- [ ] AZ failure
- [ ] Region failure
- [ ] Network failure
- [ ] Database failure
- [ ] Queue backlog
- [ ] Dependency timeout
- [ ] LLM throttling
- [ ] Model endpoint failure
- [ ] Vector store failure
- [ ] Cache failure
- [ ] Credential failure
- [ ] Invalid response
- [ ] Traffic spike
- [ ] Retry storm
- [ ] Recovery procedure

---

# 67. SAP-C02 Scenario Mapping

| Scenario | Think |
|---|---|
| High availability in one Region | Multi-AZ |
| Regional disaster | Multi-Region |
| Reduce blast radius | Cell-based / isolation |
| Temporary dependency failure | Retry + backoff |
| Prevent retry storm | Exponential backoff + jitter |
| Downstream service overloaded | Circuit breaker |
| Smooth traffic spikes | SQS / queue |
| Failed messages | DLQ |
| Safe retries | Idempotency |
| Continue with reduced functionality | Graceful degradation |
| Fast global failover | Global Accelerator / Route 53 |
| Very low RTO | Warm standby / Active-active |
| Lowest DR cost | Backup & restore |
| LLM throttling | Backoff + queue + fallback |
| LLM unavailable | Model fallback / graceful degradation |
| RAG dependency failure | Cache / fallback / isolation |
| Centralized failure detection | Observability |
| Reduce failure blast radius | Cells / isolation |
| Protect critical traffic | Rate limiting / load shedding |

---

# 68. Common SAP-C02 Exam Traps

## Trap 1 — Multi-Region for every HA requirement

Not necessarily.

If the requirement is simply:

> High availability within a Region

Use:

**Multi-AZ**

---

## Trap 2 — Retry forever

Wrong.

Use:

```text
Timeout
+
Limited retries
+
Exponential backoff
+
Jitter
```

---

## Trap 3 — Retry without idempotency

Can create duplicate side effects.

---

## Trap 4 — Synchronous architecture everywhere

Can create cascading failures.

Use asynchronous processing where appropriate.

---

## Trap 5 — Active-active is always best

Wrong.

It provides excellent availability but costs more and introduces data consistency and operational complexity.

Choose based on:

```text
RTO
RPO
Cost
Complexity
Business criticality
```

---

## Trap 6 — Queue solves everything

A queue improves decoupling and buffering, but consumers still need:

- Retry handling
- Idempotency
- DLQ
- Monitoring
- Capacity management

---

## Trap 7 — Cache is always the source of truth

Usually not.

Cache is generally a performance/resilience mechanism, not necessarily the authoritative data store.

---

## Trap 8 — LLM fallback means use any model

Not necessarily.

Fallback must meet:

- Business requirements
- Quality requirements
- Security requirements
- Latency requirements
- Cost constraints

---

# 69. Resilience Decision Tree

```text
Need high availability?
        │
        ↓
One Region enough?
   │            │
  YES           NO
   ↓             ↓
Multi-AZ      Multi-Region
   │             │
   ↓             ↓
Dependency can fail?
        │
       YES
        ↓
Timeout
   +
Retry
   +
Backoff/Jitter
        │
        ↓
Still failing?
        │
       YES
        ↓
Circuit Breaker
        │
        ↓
Need decoupling?
        │
       YES
        ↓
Queue / Event
        │
        ↓
Need failed-message handling?
        │
       YES
        ↓
DLQ
        │
        ↓
Retries can duplicate work?
        │
       YES
        ↓
Idempotency
```

---

# 70. DR Decision Tree

```text
Need disaster recovery?
        ↓
What RTO/RPO?
        ↓
Low cost / slower recovery
        ↓
Backup & Restore

Faster recovery
        ↓
Pilot Light

Very fast recovery
        ↓
Warm Standby

Near-continuous availability
        ↓
Active-Active
```

---

# 71. GenAI Resilience Decision Tree

```text
GenAI workload
      ↓
Model failure?
   │         │
  NO        YES
   │         ↓
   │     Retry + Backoff
   │         ↓
   │     Still failing?
   │         ↓
   │     Circuit Breaker
   │         ↓
   │     Fallback Model?
   │         ↓
   │     Graceful Degradation
   │
   ↓
RAG dependency?
      ↓
Vector Store Failure?
      ↓
Cache / Backup / Fallback
      ↓
External Tool Failure?
      ↓
Timeout + Retry + Isolation
```

---

# 72. High-Yield Comparison

| Pattern | Primary Purpose |
|---|---|
| Multi-AZ | AZ failure resilience |
| Multi-Region | Regional failure resilience |
| Auto Scaling | Capacity + self-healing |
| Load Balancer | Traffic distribution |
| Retry | Recover transient failures |
| Backoff | Reduce retry pressure |
| Jitter | Avoid synchronized retries |
| Circuit Breaker | Protect failing dependency |
| Queue | Decouple and buffer |
| DLQ | Isolate failed messages |
| Idempotency | Safe repeated operations |
| Cache | Reduce dependency load / improve latency |
| Rate Limiting | Protect capacity |
| Load Shedding | Protect critical workloads |
| Active-Passive | Lower-cost DR |
| Active-Active | Fast failover / high availability |
| CloudWatch | Metrics/monitoring |
| CloudTrail | API audit |
| Route 53 | DNS-based routing/failover |
| Global Accelerator | Global traffic optimization/failover |

---

# 73. Must-Memorize SAP-C02 Rules

1. **Assume components will fail.**
2. **Eliminate single points of failure.**
3. **Use Multi-AZ for regional high availability within one AWS Region.**
4. **Use Multi-Region when regional failure resilience is a requirement.**
5. **Reduce blast radius with isolation and cell-based architecture.**
6. **Use timeouts for unreliable dependencies.**
7. **Use limited retries for transient failures.**
8. **Use exponential backoff to reduce pressure on dependencies.**
9. **Use jitter to prevent synchronized retry storms.**
10. **Use circuit breakers to stop repeated calls to failing dependencies.**
11. **Use queues to decouple producers and consumers.**
12. **Use DLQs for messages that repeatedly fail processing.**
13. **Make retried operations idempotent.**
14. **Use graceful degradation when full functionality is unavailable.**
15. **Use rate limiting to protect downstream services.**
16. **Use load shedding to preserve critical functionality during overload.**
17. **Choose DR strategy based on RTO, RPO, cost, and complexity.**
18. **Backup and restore is usually the lowest-cost DR strategy but has slower recovery.**
19. **Active-active generally provides the fastest failover but has greater cost and complexity.**
20. **GenAI workloads require the same reliability engineering principles as traditional production applications.**
21. **Model throttling requires quota-aware design, backoff, and potentially fallback capacity.**
22. **RAG systems must account for vector-store, embedding, document-store, and model failures.**
23. **Observability is essential for detecting and diagnosing failures.**
24. **Resilience should be tested, not assumed.**
25. **The best SAP-C02 answer usually satisfies the stated reliability requirement without unnecessary cost or complexity.**

---

# 74. Final SAP-C02 Mental Model

When the exam gives you a resilience scenario, ask:

```text
What can fail?
      ↓
What is the failure domain?
      ↓
What is the blast radius?
      ↓
Can the system continue operating?
      ↓
How is failure detected?
      ↓
How is the dependency isolated?
      ↓
How does the system recover?
      ↓
What happens to in-flight work?
      ↓
Can retries create duplicates?
      ↓
What are the RTO/RPO requirements?
      ↓
Does the architecture meet the requirement?
      ↓
What is the simplest cost-effective AWS solution?
```

### Ultimate resilience formula

```text
Resilience
=
Redundancy
+
Failure Isolation
+
Detection
+
Automated Recovery
+
Graceful Degradation
+
Observability
+
Tested DR
```

> **Key takeaway:** SAP-C02 resilience questions are about designing systems that continue to provide business value when components fail. For GenAI workloads, apply the same proven cloud patterns—Multi-AZ/Multi-Region, queues, retries with backoff and jitter, circuit breakers, idempotency, graceful degradation, isolation, observability, and tested disaster recovery—to every component from the API layer through the model, RAG pipeline, data stores, and external tools.
