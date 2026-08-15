# Architecture Patterns – Other Optimizations
## SAP-C02 Exam Revision README

> **Target exam:** AWS Certified Solutions Architect – Professional (SAP-C02)
>
> **Course:** Architecture Patterns – Other Optimizations
>
> **Duration:** 20 minutes
>
> **Level:** Advanced
>
> **Focus:** Complementary Generative AI inference optimization techniques beyond model selection, quantization, parallelization, and distillation. Focus on latency, responsiveness, throughput, resource utilization, caching, batching, prompt optimization, speculative decoding, scaling, and cost/performance trade-offs.

---

# 1. SAP-C02 Exam Focus

GenAI optimization is not only about selecting a smaller model.

A production GenAI system can be optimized at multiple layers:

```text
User Experience
      ↓
Application
      ↓
Prompt / Context
      ↓
Inference Runtime
      ↓
Model
      ↓
Hardware
      ↓
Scaling
```

The goal is to balance:

```text
Quality
+
Latency
+
Throughput
+
Scalability
+
Availability
+
Cost
```

---

# 2. Core Mental Model

When an SAP-C02 question asks:

> "How can the organization improve GenAI performance?"

Do not immediately choose larger hardware.

Use this sequence:

```text
Identify bottleneck
      ↓
Reduce unnecessary work
      ↓
Optimize request/context
      ↓
Reuse previous work
      ↓
Improve inference efficiency
      ↓
Improve hardware utilization
      ↓
Scale appropriately
      ↓
Measure
```

---

# 3. Main Optimization Categories

Think of GenAI optimizations as:

```text
                GenAI Optimization
                       |
       ┌───────────────┼────────────────┐
       ↓               ↓                ↓
   Latency         Throughput          Cost
       |               |                |
   Caching          Batching        Right-sizing
   Streaming        Concurrency     Reuse
   Prompting        Scheduling      Autoscaling
   Speculation      Queueing        Efficient model
```

---

# 4. Perceived Latency vs Actual Latency

A critical exam concept:

**Perceived latency** is how quickly the user sees useful output.

**Actual latency** is how long the entire request takes.

Example:

```text
Request
   ↓
Token 1
   ↓
Token 2
   ↓
...
   ↓
Final token
```

Streaming can make the application feel much faster even when total generation time is unchanged.

---

# 5. Streaming Responses

Streaming returns generated tokens progressively instead of waiting for the complete response.

```text
Model
  |
  +--> Token 1 → User
  |
  +--> Token 2 → User
  |
  +--> Token 3 → User
  |
  +--> Token 4 → User
```

### Benefits

- Better perceived responsiveness
- User sees progress immediately
- Particularly useful for chat applications
- Can reduce perceived waiting time

### Important

Streaming does **not necessarily reduce total model computation**.

### SAP-C02 clue

If the requirement says:

> "Improve user-perceived responsiveness without changing the model."

Think:

**Streaming.**

---

# 6. Time to First Token (TTFT)

TTFT is the time between receiving the request and returning the first generated token.

```text
Request
   |
   |------ TTFT ------|
                       ↓
                    Token 1
```

Reducing TTFT improves perceived responsiveness.

Possible contributors:

- Large prompts
- Model loading
- Queueing
- Network latency
- Prefill computation
- Cold starts

---

# 7. Prefill vs Decode

GenAI inference can be conceptually divided into:

```text
Prompt
  ↓
Prefill
  ↓
Decode
  ↓
Generated tokens
```

### Prefill

Processes the input/context.

### Decode

Generates output tokens sequentially.

Different optimizations can affect each phase differently.

---

# 8. Prompt Optimization

Long prompts require more processing.

Example:

```text
10,000 token prompt
        ↓
More prefill work
        ↓
Higher latency + cost
```

Reduce unnecessary:

- Instructions
- Repeated context
- Historical messages
- Irrelevant documents
- Duplicate information

---

# 9. Context Optimization

Do not send every piece of available information to the model.

Instead:

```text
Large Knowledge Base
       ↓
Retrieve relevant information
       ↓
Small relevant context
       ↓
Model
```

This can reduce:

- Input tokens
- Latency
- Memory usage
- Cost

---

# 10. Retrieval-Augmented Generation

RAG can avoid putting an entire knowledge base into every prompt.

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

Instead of:

```text
Entire Knowledge Base
        ↓
Huge Prompt
        ↓
Model
```

Use only relevant context.

---

# 11. RAG Optimization

A good RAG pipeline optimizes:

```text
Query
 ↓
Retrieval quality
 ↓
Number of documents
 ↓
Chunk size
 ↓
Context size
 ↓
Prompt
 ↓
Inference
```

Retrieving too many documents can increase token count and latency.

Retrieving too few may reduce answer quality.

---

# 12. Semantic Caching

Semantic caching reuses a previous response when a new request is sufficiently similar to a cached request.

Concept:

```text
New Query
   ↓
Similarity Check
   ↓
Similar previous query?
   |
  YES
   ↓
Cached response
```

Instead of:

```text
Every request
      ↓
LLM inference
```

Use:

```text
Request
  ↓
Cache
  ↓
Hit → Return result
  ↓
Miss
  ↓
LLM
```

---

# 13. Exact Cache vs Semantic Cache

### Exact cache

Matches the same key/request.

```text
"Weather Chennai"
=
"Weather Chennai"
```

### Semantic cache

Recognizes similar meaning.

```text
"What is the weather in Chennai?"
≈
"Tell me Chennai's weather"
```

Semantic caching can increase cache hit rates for equivalent requests.

---

# 14. Cache Trade-offs

Caching can improve:

- Latency
- Throughput
- Cost
- Backend load

But cached responses may become stale.

Therefore consider:

```text
Cache
+
TTL
+
Invalidation
+
Correctness requirements
```

### SAP-C02 clue

If data changes frequently and freshness is critical, be careful with long cache TTLs.

---

# 15. Prompt Caching

Prompt caching reuses previously processed prompt/context portions where supported by the inference architecture/service.

Useful when requests repeatedly contain:

```text
Large system prompt
+
Large static context
+
Small changing user input
```

Concept:

```text
Static Context
      ↓
Cache / reuse
      +
Dynamic Request
      ↓
Model
```

This can reduce repeated computation and improve latency/cost depending on the service and workload.

---

# 16. Dynamic vs Static Context

Example:

```text
Static:
- System instructions
- Product catalog
- Policy document

Dynamic:
- User question
- Current order
- Current date
```

Optimization opportunity:

```text
Cache/reuse static portion
+
Process dynamic portion
```

---

# 17. Batching

Batching combines multiple requests into one inference workload.

```text
Request 1 ─┐
Request 2 ─┼──> Batch ──> Model
Request 3 ─┘
```

### Benefits

- Better accelerator utilization
- Higher throughput
- Better resource efficiency

### Trade-off

Requests may wait for a batch to fill.

Therefore:

```text
Batching
→ Throughput ↑
→ Utilization ↑
→ Potential latency ↑
```

---

# 18. Dynamic Batching

Dynamic batching collects requests for a short period and processes them together.

```text
Request 1
Request 2
Request 3
   ↓
Short wait
   ↓
Dynamic Batch
   ↓
Inference
```

Use when:

- Throughput matters
- Traffic is high
- A small batching delay is acceptable

Avoid aggressive batching for strict ultra-low-latency requirements.

---

# 19. Continuous Batching

Continuous batching allows requests to enter/leave the inference workload dynamically rather than waiting for one fixed batch to finish.

Concept:

```text
Request A ──────────────┐
Request B ────────┐     │
Request C ────────┼─────┼→ Inference
Request D ────────┘     │
Request E ──────────────┘
```

This can improve accelerator utilization for variable-length generative workloads.

---

# 20. Batching Decision

```text
Need maximum throughput?
        ↓
Batching may help
        ↓
Can tolerate additional waiting?
        |
       YES
        ↓
Dynamic/continuous batching
```

For interactive applications:

```text
Latency requirement very strict
        ↓
Use conservative batching
```

---

# 21. Concurrency

Concurrency is the number of requests being processed simultaneously.

Higher concurrency can improve utilization, but eventually creates:

- Memory pressure
- Queueing
- Higher latency
- KV-cache pressure

Therefore:

```text
Concurrency ↑
      ↓
Utilization ↑
      ↓
Until saturation
      ↓
Latency ↑
```

---

# 22. Queue Management

When requests arrive faster than inference capacity:

```text
Requests
   ↓
Queue
   ↓
Inference
```

A growing queue means:

```text
Arrival rate > Service rate
```

Possible solutions:

- Scale out
- Optimize model
- Reduce context
- Improve batching
- Increase capacity
- Apply admission control

---

# 23. Admission Control

When the system is overloaded, accepting unlimited work can make the entire system slower.

Admission control can:

- Reject excess requests
- Apply rate limits
- Prioritize important requests
- Protect the inference service

Example:

```text
Normal traffic
   ↓
Process normally

Overload
   ↓
Rate limit / queue / reject
```

---

# 24. Rate Limiting

Rate limiting protects systems from excessive traffic.

```text
Client
  ↓
Rate Limiter
  ↓
Allowed requests
  ↓
Inference
```

Benefits:

- Protects capacity
- Prevents overload
- Improves stability
- Helps control costs

---

# 25. Prioritization

Not every request has the same business importance.

Example:

```text
Priority 1 → Customer transaction
Priority 2 → Internal assistant
Priority 3 → Batch analytics
```

Under load:

```text
High priority
      ↓
Process first
```

This is useful when workloads have different service-level objectives.

---

# 26. Request Coalescing

If multiple users request the same expensive operation at nearly the same time:

```text
Request A ─┐
Request B ─┼→ Same underlying operation
Request C ─┘
```

Instead of performing the same work repeatedly, the application can share the result when appropriate.

This is especially useful for identical or highly reusable operations.

---

# 27. Speculative Decoding

Speculative decoding uses a smaller draft model to generate candidate tokens.

```text
User Request
      ↓
Small Draft Model
      ↓
Candidate Tokens
      ↓
Large Target Model
      ↓
Validate
      ↓
Output
```

Potential benefit:

- Lower generation latency
- Better utilization of expensive target-model compute

The key idea:

```text
Cheap model proposes
+
Expensive model verifies
```

---

# 28. When Speculative Decoding Helps

Most useful when:

- Target model is expensive
- Output is generated autoregressively
- Draft model can produce useful candidates
- Verification is efficient

It is not automatically beneficial for every model/workload.

Benchmark it.

---

# 29. Distillation

Knowledge distillation creates a smaller model that learns from a larger model.

```text
Large Teacher Model
        ↓
Knowledge
        ↓
Small Student Model
```

Benefits:

- Lower inference cost
- Lower latency
- Lower memory
- Easier scaling

Trade-off:

- Potential quality loss

---

# 30. Model Routing

Use different models for different request types.

```text
User Request
      ↓
Router
   ┌──┴──────────┐
   ↓             ↓
Simple         Complex
   ↓             ↓
Small model   Large model
```

This can reduce cost while preserving quality for difficult requests.

---

# 31. Intelligent Model Routing

Example:

```text
Simple FAQ
   ↓
Small / cheap model

Complex reasoning
   ↓
Large model

Classification
   ↓
Specialized model
```

This avoids sending every request to the most expensive model.

---

# 32. Model Cascading

A cascade starts with a cheaper model.

```text
Request
  ↓
Small Model
  ↓
Confidence sufficient?
  |
 YES → Answer
  |
 NO
  ↓
Large Model
```

Useful when most requests are simple but a small percentage require advanced reasoning.

---

# 33. Model Routing vs Cascading

| Pattern | Idea |
|---|---|
| Routing | Choose model based on request |
| Cascading | Start cheap, escalate if needed |
| Ensemble | Multiple models contribute |
| Distillation | Train smaller model from larger model |

---

# 34. Prompt Compression

Prompt compression reduces unnecessary tokens while preserving important information.

```text
Large Context
      ↓
Compression
      ↓
Smaller Context
      ↓
Model
```

Potential benefits:

- Lower input cost
- Lower prefill latency
- Less memory pressure

Trade-off:

- Information may be lost

---

# 35. Conversation History Optimization

Long chat histories can become expensive.

Instead of sending:

```text
Entire conversation
```

Use:

```text
Recent messages
+
Conversation summary
+
Relevant history
```

Example:

```text
100 messages
    ↓
Summary
+
Last 10 messages
    ↓
Model
```

This reduces token usage.

---

# 36. Summarization as an Optimization

For long-running conversations:

```text
Conversation
     ↓
Summary
     ↓
Compact context
```

The summary must preserve information needed for future responses.

Poor summaries can reduce quality.

---

# 37. Token Optimization

Input and output tokens both affect performance and cost.

Reduce:

- Repeated instructions
- Unnecessary examples
- Irrelevant retrieved documents
- Excessive output requirements
- Duplicate context

Think:

```text
Fewer useful tokens
=
Less work
=
Lower latency/cost
```

---

# 38. Output Length Control

If the application only needs:

```text
3 bullet points
```

Do not request:

```text
A 5,000-word essay
```

Use:

- Output limits
- Clear response format
- Structured output requirements

This can reduce generation time and cost.

---

# 39. Structured Outputs

If the application needs machine-readable output:

```text
JSON
```

instead of:

```text
Long natural-language response
```

Benefits:

- Less unnecessary text
- Easier application processing
- Predictable output shape

---

# 40. Parallelization

Independent operations can run simultaneously.

Example:

```text
Request
  |
  +--> Search A ──┐
  +--> Search B ──┼→ Combine
  +--> Search C ──┘
```

Instead of:

```text
Search A
  ↓
Search B
  ↓
Search C
```

Parallel execution can reduce overall orchestration latency.

---

# 41. Asynchronous Processing

If the user does not need an immediate result:

```text
Request
  ↓
Queue
  ↓
Worker
  ↓
Long-running inference
  ↓
Result
```

Useful for:

- Batch processing
- Document processing
- Large offline workloads
- Non-interactive generation

This removes strict synchronous latency requirements.

---

# 42. Event-Driven GenAI

Example:

```text
S3 Upload
   ↓
Event
   ↓
Lambda / Workflow
   ↓
GenAI processing
   ↓
Store result
```

Useful when AI processing is triggered by events rather than direct user requests.

---

# 43. Serverless for Orchestration

Use serverless services for surrounding application logic when appropriate:

```text
API Gateway
     ↓
Lambda
     ↓
Bedrock
```

Benefits:

- No server management
- Automatic scaling
- Good for variable traffic

But remember:

The inference service itself has its own performance/scaling characteristics.

---

# 44. Asynchronous vs Synchronous

| Requirement | Pattern |
|---|---|
| Immediate chatbot response | Synchronous + streaming |
| Long document processing | Asynchronous |
| Batch generation | Asynchronous |
| User must wait | Synchronous |
| Large workload with flexible completion time | Asynchronous |

---

# 45. Connection Pooling and Reuse

For application-to-service communication, repeatedly creating connections can add overhead.

Use appropriate:

- Connection reuse
- HTTP keep-alive
- SDK clients
- Connection pooling

This is a general application optimization rather than a model optimization.

---

# 46. Network Optimization

Latency can also come from network paths.

Consider:

```text
Application
   ↓
AWS Region
   ↓
AI service
```

Reduce unnecessary:

- Cross-region traffic
- Network hops
- Data transfers
- Large payloads

Keep tightly coupled components close to each other when architecture requirements allow.

---

# 47. Payload Optimization

Do not send unnecessarily large payloads.

Instead of:

```text
Huge document
```

send:

```text
Relevant extracted content
```

Possible techniques:

- Compression
- Chunking
- Filtering
- Summarization
- Retrieval

---

# 48. Resource Utilization

Look for idle resources.

Example:

```text
Accelerator utilization = 30%
```

Potential issue:

```text
Too little concurrency
OR
Inefficient batching
OR
CPU/network bottleneck
OR
Poor model serving
```

Do not assume the accelerator is the bottleneck.

---

# 49. CPU vs Accelerator Bottleneck

GenAI inference can have bottlenecks outside the accelerator.

Possible bottlenecks:

```text
CPU preprocessing
      ↓
Network
      ↓
Tokenizer
      ↓
Accelerator
      ↓
Memory
      ↓
Postprocessing
```

Optimize the actual bottleneck.

---

# 50. Tokenization Bottleneck

Large input workloads may spend meaningful time processing/tokenizing input.

Potential optimizations:

- Reduce input tokens
- Reuse/cache repeated content
- Optimize preprocessing
- Increase appropriate compute capacity

---

# 51. Memory Bandwidth

Autoregressive generation can be sensitive to memory movement.

Therefore:

```text
More compute
≠
Always lower latency
```

The workload may be limited by:

- Memory bandwidth
- Memory capacity
- KV cache
- Data movement

---

# 52. KV Cache Optimization

KV cache stores attention state during generation.

As:

```text
Context length ↑
+
Concurrency ↑
```

then:

```text
KV Cache ↑
```

Potential effects:

- Memory pressure
- Lower concurrency
- Higher latency
- More scaling requirements

Monitor KV-cache behavior where supported.

---

# 53. Context Window Optimization

A large context window does not mean every request should use the maximum.

Use:

```text
Only relevant context
```

instead of:

```text
Maximum possible context
```

This improves:

- Cost
- Latency
- Memory utilization

---

# 54. Caching Layers

GenAI applications can use multiple caching layers:

```text
L1: Exact response cache
        ↓
L2: Semantic cache
        ↓
L3: Retrieval/document cache
        ↓
L4: Prompt/context reuse
        ↓
LLM inference
```

The closer the request is served to the application, the less expensive inference becomes.

---

# 55. Cache Invalidation

Caching introduces correctness questions.

Ask:

```text
How frequently does data change?
```

If data changes frequently:

```text
Short TTL
+
Event-driven invalidation
```

If data is stable:

```text
Longer TTL
```

---

# 56. Availability vs Caching

Do not cache sensitive or personalized results incorrectly.

Example:

```text
User A response
      ↓
Cache
      ↓
User B receives User A data
```

This is a serious architecture failure.

Cache keys must include appropriate identity/context where necessary.

---

# 57. Security and Optimization

Performance optimization must not bypass security.

Examples:

- Do not cache private responses globally
- Protect prompt data
- Encrypt sensitive cached data
- Apply IAM permissions
- Validate tenant isolation

### SAP-C02 principle

**Never sacrifice security for performance.**

---

# 58. Multi-Tenant GenAI

For SaaS applications:

```text
Tenant A
   ↓
Tenant-specific context

Tenant B
   ↓
Tenant-specific context
```

Optimization must preserve tenant isolation.

Never use a shared cache key that can return another tenant's information.

---

# 59. Cost Optimization

A practical sequence:

```text
1. Reduce unnecessary tokens
        ↓
2. Cache reusable results
        ↓
3. Use appropriate model
        ↓
4. Route simple requests to cheaper model
        ↓
5. Quantize where appropriate
        ↓
6. Improve batching/utilization
        ↓
7. Autoscale
        ↓
8. Benchmark hardware
```

---

# 60. Throughput Optimization

For throughput-focused workloads:

```text
Batching
+
Continuous batching
+
Higher concurrency
+
Efficient model
+
Hardware utilization
+
Horizontal scaling
```

But watch:

```text
Latency
+
Memory
+
Queue length
```

---

# 61. Latency Optimization

For latency-focused workloads:

```text
Short prompts
+
Relevant context
+
Caching
+
Streaming
+
Low TTFT
+
Optimized runtime
+
Warm capacity
+
Appropriate model
```

Avoid unnecessarily aggressive batching.

---

# 62. Cost vs Latency

Typical trade-off:

```text
More capacity
   ↓
Lower latency
   ↓
Higher cost
```

or:

```text
More batching
   ↓
Better utilization
   ↓
Lower cost
   ↓
Potentially higher latency
```

SAP-C02 questions often require finding the correct balance.

---

# 63. Warm Capacity

Keeping capacity warm can reduce startup latency.

```text
Warm instance
     ↓
Request
     ↓
Fast processing
```

But:

```text
Warm capacity
=
Idle cost
```

Use it when latency requirements justify the cost.

---

# 64. Pre-Warming

For predictable traffic:

```text
Traffic spike at 09:00
        ↓
Pre-warm capacity at 08:45
```

This can reduce cold-start impact.

Combine with autoscaling when appropriate.

---

# 65. Load Testing

Before production:

```text
Low load
Medium load
High load
Spike
Sustained load
```

Measure:

- P50
- P90
- P95
- P99
- TTFT
- Throughput
- Error rate
- Queue length
- Cost
- Memory

---

# 66. Benchmarking

Use representative:

```text
Prompts
Context lengths
Output lengths
Concurrency
Traffic patterns
Models
Hardware
```

Do not benchmark only one short prompt.

---

# 67. A/B Testing Models

When comparing models:

```text
Model A
vs
Model B
```

Evaluate:

- Quality
- Latency
- Cost
- Throughput
- Failure rate

The technically largest model is not necessarily the best production choice.

---

# 68. Canary Deployment

Deploy a new model/configuration to a small percentage of traffic.

```text
95% → Existing
5%  → New
```

Monitor:

- Latency
- Quality
- Errors
- Cost

Then gradually increase traffic.

---

# 69. Rollback

If performance degrades:

```text
New model
   ↓
Bad metrics
   ↓
Rollback
   ↓
Previous stable version
```

Optimization should not create an unstable production system.

---

# 70. Observability

Monitor the entire pipeline:

```text
Request
 ↓
API
 ↓
Retrieval
 ↓
Prompt construction
 ↓
Inference
 ↓
Response
```

Measure latency at each stage.

---

# 71. Useful Metrics

### Application

- Request rate
- Error rate
- End-to-end latency

### Retrieval

- Retrieval latency
- Number of documents
- Context size

### Inference

- TTFT
- Tokens/sec
- Queue length
- Concurrent requests

### Infrastructure

- CPU
- Memory
- Accelerator utilization
- Network
- KV-cache usage where available

### Business

- Cost/request
- Quality
- User satisfaction

---

# 72. Optimization Workflow

```text
Measure
  ↓
Identify bottleneck
  ↓
Form hypothesis
  ↓
Apply one optimization
  ↓
Benchmark
  ↓
Validate quality
  ↓
Validate security
  ↓
Validate cost
  ↓
Deploy gradually
  ↓
Monitor
```

Never optimize blindly.

---

# 73. Scenario: Users Say Chatbot Feels Slow

Requirement:

> Improve perceived responsiveness without changing the model.

Consider:

```text
Streaming
+
Reduce TTFT
+
Reduce prompt/context
+
Cache reusable context
+
Keep capacity warm
```

---

# 74. Scenario: Same Questions Repeated Frequently

Requirement:

> The same or semantically equivalent questions occur repeatedly.

Consider:

```text
Exact cache
OR
Semantic cache
```

This can avoid unnecessary model inference.

---

# 75. Scenario: Long Chat Sessions Become Expensive

Requirement:

> Conversation history is growing continuously.

Consider:

```text
Conversation summarization
+
Relevant-history retrieval
+
Recent messages
```

Avoid sending the entire conversation every time.

---

# 76. Scenario: High Throughput Batch Workload

Requirement:

> Latency is less important than maximum throughput.

Consider:

```text
Batching
+
Continuous batching
+
Higher concurrency
+
Efficient accelerator utilization
+
Asynchronous processing
```

---

# 77. Scenario: Low Traffic but Huge Spikes

Requirement:

> Most of the day has low traffic but occasional unpredictable spikes.

Consider:

```text
Autoscaling
+
Efficient baseline capacity
+
Appropriate warm capacity
+
Queue protection
```

Avoid permanently provisioning peak capacity unless required.

---

# 78. Scenario: Simple + Complex Requests

Requirement:

> Most requests are simple, but some require advanced reasoning.

Consider:

```text
Router
   ↓
Simple → Small model
Complex → Large model
```

or:

```text
Small model
   ↓
Confidence?
   ↓
No → Large model
```

---

# 79. Scenario: Accelerator Utilization Is Low

Do not immediately reduce accelerator size.

Investigate:

```text
Batching
Concurrency
CPU preprocessing
Network
Tokenization
Queueing
Model serving
```

The accelerator may be waiting for another component.

---

# 80. Scenario: High Cost With Acceptable Quality

Consider:

```text
Smaller model
+
Model routing
+
Caching
+
Prompt compression
+
Output limits
+
Quantization
+
Autoscaling
```

Measure quality after every major optimization.

---

# 81. Scenario: Strict Data Freshness

Requirement:

> Responses must reflect current information.

Be careful with:

```text
Long-lived response cache
```

Prefer:

```text
Short TTL
+
Event-driven invalidation
+
Fresh retrieval
```

---

# 82. Scenario: Multi-Tenant SaaS

Requirement:

> Optimize repeated requests while maintaining tenant isolation.

Use:

```text
Tenant-aware cache keys
+
Tenant-aware retrieval
+
IAM authorization
+
Data isolation
```

Never allow optimization caches to cross security boundaries.

---

# 83. Common SAP-C02 Exam Traps

## Trap 1 — Streaming reduces total compute

**Wrong.**

Streaming mainly improves perceived responsiveness.

---

## Trap 2 — Batching always improves latency

**Wrong.**

Batching usually targets utilization/throughput and can introduce waiting.

---

## Trap 3 — Cache everything

**Wrong.**

Caching can cause stale or incorrect responses and can create security issues.

---

## Trap 4 — Semantic cache is identical to exact cache

**Wrong.**

Semantic cache uses similarity/meaning rather than exact key equality.

---

## Trap 5 — Bigger context always improves quality

**Wrong.**

More irrelevant context can increase cost, latency, and potentially reduce effective quality.

---

## Trap 6 — Autoscaling means zero cold starts

**Wrong.**

New capacity may require initialization and model loading.

---

## Trap 7 — More concurrency always improves throughput

**Wrong.**

Beyond saturation, memory pressure and queueing can increase latency.

---

## Trap 8 — Larger model is always better

**Wrong.**

Use the smallest model that meets the business quality requirement.

---

## Trap 9 — Caching can ignore tenant identity

**Wrong.**

Tenant/user-specific data must not leak through shared caches.

---

## Trap 10 — Asynchronous processing is best for every workload

**Wrong.**

Interactive applications may require synchronous/streaming responses.

---

## Trap 11 — Reduce tokens at any cost

**Wrong.**

Excessive compression can remove information and degrade quality.

---

## Trap 12 — Optimize hardware before software

**Wrong.**

Often the best optimization is reducing unnecessary model work.

---

# 84. Decision Tree – Latency

```text
Latency problem
      ↓
Is perceived responsiveness the issue?
      |
     YES
      ↓
Streaming
      ↓
Is TTFT high?
      |
     YES
      ↓
Reduce prompt/context
+
Caching
+
Warm capacity
+
Inference optimization
```

---

# 85. Decision Tree – Throughput

```text
Throughput problem
      ↓
Can requests be batched?
      |
     YES
      ↓
Batch / continuous batching
      ↓
Can concurrency increase?
      |
     YES
      ↓
Increase safely
      ↓
Still insufficient?
      ↓
Scale horizontally
```

---

# 86. Decision Tree – Cost

```text
Cost too high
      ↓
Reduce unnecessary tokens
      ↓
Cache repeated work
      ↓
Route simple requests
      ↓
Use appropriate model
      ↓
Quantize if appropriate
      ↓
Improve utilization
      ↓
Autoscale
```

---

# 87. Decision Tree – Cache

```text
Repeated requests?
      |
     YES
      ↓
Can response be reused safely?
      |
     YES
      ↓
Cache
      ↓
Is exact match enough?
  |             |
 YES           NO
  ↓             ↓
Exact cache   Semantic cache
```

---

# 88. Decision Tree – Long Conversations

```text
Large conversation
       ↓
Do you need all history?
       |
      NO
       ↓
Summarize
+
Retrieve relevant history
+
Keep recent messages
       ↓
Compact prompt
```

---

# 89. Optimization Comparison

| Technique | Primary Goal | Main Benefit | Main Trade-off |
|---|---|---|---|
| Streaming | Perceived latency | Faster visible response | Does not reduce total compute |
| Exact caching | Latency/cost | Avoid repeated inference | Staleness |
| Semantic caching | Latency/cost | Reuse similar requests | Similarity/accuracy risk |
| Prompt compression | Latency/cost | Fewer tokens | Information loss |
| RAG | Context efficiency | Relevant context | Retrieval complexity |
| Batching | Throughput | Better utilization | Waiting/latency |
| Continuous batching | Throughput | Better utilization | Runtime complexity |
| Model routing | Cost/latency | Right model per request | Router complexity |
| Cascading | Cost | Escalate only when needed | Additional decision step |
| Speculative decoding | Latency | Faster generation | Extra model/complexity |
| Summarization | Context optimization | Smaller prompts | Summary loss |
| Async processing | Scalability | Decouple workload | Not immediate |
| Rate limiting | Stability | Protect capacity | Requests may be delayed/rejected |
| Warm capacity | Latency | Reduces startup delay | Idle cost |
| Pre-warming | Latency | Handles known spikes | Additional capacity cost |
| Parallel execution | Latency | Reduces serial waiting | More concurrent resources |

---

# 90. SAP-C02 Golden Rules

1. **Identify the actual bottleneck before optimizing.**
2. **Streaming improves perceived responsiveness, not necessarily total inference time.**
3. **TTFT is a key metric for interactive GenAI applications.**
4. **Long prompts increase processing and can increase latency and cost.**
5. **Retrieve only relevant context instead of sending an entire knowledge base.**
6. **Exact caching matches exact requests/keys.**
7. **Semantic caching can reuse results for semantically similar requests.**
8. **Caching requires TTL/invalidation decisions when data changes.**
9. **Never allow shared caching to break tenant or user isolation.**
10. **Prompt caching/context reuse can reduce repeated processing where supported.**
11. **Batching generally improves throughput and utilization but can add latency.**
12. **Continuous batching can improve utilization for variable-length generative workloads.**
13. **Higher concurrency is beneficial only until the system reaches saturation.**
14. **Growing queues indicate demand is exceeding processing capacity.**
15. **Rate limiting and admission control protect overloaded systems.**
16. **Speculative decoding uses a smaller draft model to help accelerate a larger target model.**
17. **Model routing can send simple requests to cheaper models and complex requests to stronger models.**
18. **Model cascading can escalate difficult requests to larger models only when necessary.**
19. **Conversation summarization can reduce long-context token usage.**
20. **Output limits can reduce unnecessary generation and cost.**
21. **Parallelize independent operations to reduce orchestration latency.**
22. **Asynchronous processing is useful when the user does not need an immediate result.**
23. **Warm capacity can reduce cold-start latency but increases idle cost.**
24. **For predictable traffic, pre-warming/scheduled capacity can reduce scale-out delay.**
25. **Optimize the actual bottleneck: CPU, tokenizer, network, memory, accelerator, queue, or model runtime.**
26. **More concurrency does not always mean more throughput.**
27. **More context does not automatically mean better responses.**
28. **Cost optimization should consider model choice, tokens, caching, utilization, and scaling together.**
29. **Every optimization should be validated for quality, security, latency, throughput, and cost.**
30. **Never trade security or tenant isolation for performance.**

---

# 91. 30-Second Revision

```text
GENAI OPTIMIZATION

Latency
 ↓
Streaming
 ↓
Reduce prompt/context
 ↓
Caching
 ↓
Warm capacity
 ↓
Optimize inference

Throughput
 ↓
Batching
 ↓
Continuous batching
 ↓
Concurrency
 ↓
Horizontal scaling

Cost
 ↓
Fewer tokens
 ↓
Caching
 ↓
Model routing
 ↓
Smaller model
 ↓
Quantization
 ↓
Autoscaling

Long context
 ↓
RAG
 ↓
Summarization
 ↓
Relevant history
 ↓
Prompt compression

Repeated requests
 ↓
Exact cache
 OR
Semantic cache

Simple + complex requests
 ↓
Routing / cascading

Strict interactive workload
 ↓
Streaming + low TTFT

Batch workload
 ↓
Async + batching
```

---

# 92. Ultimate SAP-C02 Mental Model

When you see an optimization scenario:

```text
What is slow/expensive?
        ↓
Latency?
Throughput?
Cost?
Memory?
Scalability?
        ↓
Reduce unnecessary work
        ↓
Reduce tokens
        ↓
Reduce context
        ↓
Reuse previous work
        ↓
Optimize model serving
        ↓
Batch where appropriate
        ↓
Parallelize independent work
        ↓
Route requests intelligently
        ↓
Scale based on demand
        ↓
Protect system with queues/rate limits
        ↓
Measure quality + latency + throughput + cost
```

## Final Formula

```text
Efficient GenAI
=
Relevant Context
+
Right Model
+
Reusable Work
+
Efficient Inference
+
Appropriate Batching
+
Smart Routing
+
Correct Scaling
+
Continuous Measurement
```

> **SAP-C02 takeaway:** When optimizing GenAI, do not assume that adding larger infrastructure is the answer. First reduce unnecessary work through **prompt/context optimization, retrieval, caching, summarization, and model routing**. Then improve **inference utilization with batching, parallelization, and speculative techniques**, and finally scale according to real workload signals. Always validate the result against **quality, latency, throughput, cost, security, and tenant isolation**.
