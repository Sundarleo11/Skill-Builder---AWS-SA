# AWS Architecture Patterns – Application Runtime Optimization
## SAP-C02 Exam Revision README

> **Target exam:** AWS Certified Solutions Architect – Professional (SAP-C02)
>
> **Course:** Architecture Patterns - Application Runtime Optimization
>
> **Course duration:** 40 minutes
>
> **Level:** Advanced
>
> **Focus:** GenAI runtime performance, inference optimization, model selection, Amazon Bedrock, SageMaker, Inferentia2, Trainium, quantization, token optimization, caching, scaling, latency, throughput, and cost/performance trade-offs.

---

# 1. Exam Focus

SAP-C02 optimization questions are usually scenario-based.

The exam may ask you to design a system that:

- Reduces application latency
- Increases throughput
- Handles large request volumes
- Controls GenAI inference costs
- Maintains response quality
- Scales automatically
- Uses appropriate compute
- Avoids unnecessary infrastructure management
- Meets performance SLAs
- Optimizes RAG workloads

For GenAI workloads, performance bottlenecks can occur at multiple layers:

```text
User
 ↓
API
 ↓
Application
 ↓
Prompt Processing
 ↓
Retrieval / RAG
 ↓
Model Inference
 ↓
Post-processing
 ↓
Response
```

The key SAP-C02 skill is identifying **where the bottleneck is before choosing an optimization**.

---

# 2. Runtime Optimization Mindset

Do not immediately choose the biggest or fastest infrastructure.

First ask:

```text
What is the bottleneck?
       ↓
Latency?
Throughput?
Memory?
Compute?
Network?
Model?
Retrieval?
Tokens?
Concurrency?
       ↓
Choose the smallest effective optimization
```

### Golden principle

> **Optimize the workload, not just the infrastructure.**

---

# 3. Key Performance Metrics

Understand these terms.

## Latency

Time required to complete a request.

```text
Request → Response
```

Lower is generally better.

## Throughput

Amount of work completed per unit time.

Examples:

```text
Requests / second
Tokens / second
Transactions / second
```

## Concurrency

Number of requests being processed simultaneously.

## Utilization

How much of the available compute/resource capacity is being used.

---

# 4. GenAI-Specific Latency

GenAI applications have additional latency considerations.

Important metrics include:

### Time to First Token (TTFT)

Time between request and first generated token.

```text
Request
 ↓
Processing
 ↓
First Token
```

### Time Per Output Token

How quickly subsequent tokens are generated.

### Total Generation Latency

Time until the complete response is generated.

---

# 5. GenAI Performance Model

A simplified view:

```text
Total Latency
=
Network
+
Application
+
Retrieval
+
Prompt Processing
+
Model Inference
+
Post-processing
```

Optimizing only model inference may not solve the actual bottleneck.

Example:

```text
Model = 200 ms
RAG retrieval = 2 seconds

Optimizing model to 150 ms
→ only saves 50 ms
```

The retrieval layer should be optimized first.

---

# 6. Model Selection

Model selection is one of the highest-value optimizations.

Do not automatically use the largest model.

Consider:

- Accuracy
- Latency
- Cost
- Context requirements
- Reasoning requirements
- Token limits
- Throughput
- Availability

Example:

```text
Simple classification
       ↓
Smaller model

Complex reasoning
       ↓
More capable model
```

---

# 7. Model Routing

Different requests can use different models.

```text
Incoming Request
       ↓
Classifier / Router
       ↓
┌───────────────┬────────────────┐
│               │                │
Simple          Complex          Specialized
Request         Request          Request
│               │                │
Smaller Model   Large Model      Specialized Model
```

Benefits:

- Lower average cost
- Better latency
- Appropriate quality
- Higher overall throughput

---

# 8. Amazon Bedrock

Amazon Bedrock provides access to foundation models through managed AWS APIs.

Advantages:

- No model infrastructure management
- Multiple foundation model choices
- Managed service
- Security integration with AWS
- Easier application integration
- Flexible model selection

### SAP-C02 clue

If the requirement says:

> Use foundation models without managing model infrastructure.

Think:

**Amazon Bedrock**

---

# 9. Amazon SageMaker

Amazon SageMaker provides broader ML development and deployment capabilities.

It is useful when organizations need:

- Custom models
- Model training
- Fine-tuning workflows
- Custom inference
- Greater control over model infrastructure
- Specialized deployment configurations

### Quick distinction

```text
Managed foundation model access
→ Bedrock

Custom ML/model development and greater infrastructure control
→ SageMaker
```

---

# 10. Bedrock vs SageMaker

| Requirement | Better Fit |
|---|---|
| Access foundation models through managed APIs | Bedrock |
| Multiple foundation model providers | Bedrock |
| Minimize infrastructure management | Bedrock |
| Build custom ML workflows | SageMaker |
| Custom model hosting | SageMaker |
| Fine-grained inference infrastructure control | SageMaker |
| Specialized ML deployment | SageMaker |

### Exam trap

Do not assume SageMaker is automatically faster.

The correct choice depends on the workload and management requirements.

---

# 11. Model Size

Large models generally require:

- More memory
- More compute
- Higher inference cost
- Potentially higher latency

But they may provide:

- Better reasoning
- Better quality
- Greater capability

Therefore:

```text
Largest model ≠ Automatically best architecture
```

Choose based on business requirements.

---

# 12. Model Distillation

Knowledge distillation creates a smaller model that approximates a larger model.

Concept:

```text
Large Teacher Model
        ↓
Knowledge Transfer
        ↓
Smaller Student Model
```

Benefits:

- Lower inference cost
- Lower memory requirements
- Lower latency
- Higher throughput

Trade-off:

- Potential quality reduction

---

# 13. Quantization

Quantization reduces numerical precision used by model parameters.

Concept:

```text
Higher precision
      ↓
Lower precision
      ↓
Less memory
      ↓
Potentially faster inference
```

Benefits:

- Lower memory footprint
- Potentially lower cost
- Potentially faster inference

Trade-off:

- Possible quality/accuracy degradation

### SAP-C02 clue

If the scenario emphasizes:

> Large model + memory constraints + inference optimization

Think:

**Quantization**

---

# 14. Model Compression

General model compression techniques include:

- Quantization
- Pruning
- Distillation

Goal:

```text
Smaller model
+
Lower resource consumption
+
Acceptable quality
```

---

# 15. Pruning

Pruning removes less-important model parameters or structures.

Concept:

```text
Original Model
      ↓
Remove unnecessary components
      ↓
Smaller / more efficient model
```

Potential benefits:

- Lower compute
- Lower memory
- Faster inference

Potential trade-off:

- Reduced model quality

---

# 16. Prompt Optimization

Prompts directly affect token consumption.

Bad:

```text
Very long instructions
+
Repeated context
+
Unnecessary examples
+
Large documents
```

Better:

```text
Concise instructions
+
Relevant context
+
Required examples only
```

Benefits:

- Lower latency
- Lower cost
- Less context processing
- Potentially higher throughput

---

# 17. Token Optimization

GenAI cost and performance are strongly affected by token usage.

Optimize:

- Input tokens
- Output tokens
- Retrieved context
- Conversation history
- System prompts

Concept:

```text
Unnecessary Tokens
       ↓
Remove
       ↓
Lower Cost
+
Lower Latency
```

---

# 18. Context Window Optimization

A large context window does not mean the application should send all available information.

Bad:

```text
User Query
+
Entire Document Repository
+
Entire Conversation History
```

Better:

```text
User Query
+
Relevant Context
+
Minimal Required History
```

Use retrieval to provide relevant information.

---

# 19. RAG Optimization

Typical RAG:

```text
User Query
    ↓
Embedding
    ↓
Vector Search
    ↓
Relevant Documents
    ↓
Prompt
    ↓
LLM
```

Performance bottlenecks may occur in:

- Embedding generation
- Vector search
- Network
- Document retrieval
- Reranking
- Prompt construction
- LLM inference

---

# 20. Retrieval Optimization

Do not retrieve too many documents.

Example:

```text
Top 100 documents
       ↓
Huge prompt
       ↓
High latency
       ↓
High cost
```

Better:

```text
Top relevant documents
       ↓
Smaller context
       ↓
Lower latency
       ↓
Lower cost
```

Balance relevance and recall against latency and token cost.

---

# 21. Reranking

A reranker can improve the relevance of retrieved results.

Concept:

```text
Vector Search
     ↓
Candidate Documents
     ↓
Reranker
     ↓
Best Documents
     ↓
LLM
```

Trade-off:

```text
Higher retrieval quality
       vs
Additional latency/cost
```

Use it when improved relevance justifies the additional processing.

---

# 22. Embedding Optimization

Embedding generation can become a bottleneck in large RAG systems.

Strategies:

- Batch embedding requests
- Cache embeddings
- Process documents asynchronously
- Avoid regenerating unchanged embeddings
- Choose an appropriate embedding model

Example:

```text
Document
 ↓
Hash / Change Detection
 ↓
Already Embedded?
 ├── YES → Reuse
 └── NO  → Generate embedding
```

---

# 23. Caching

Caching can significantly improve runtime performance.

Potential cache targets:

- Model responses
- Embeddings
- Retrieval results
- Prompt templates
- Frequently accessed documents
- Session information

Concept:

```text
Request
 ↓
Cache
 ├── Hit → Return
 └── Miss → Backend / Model
```

---

# 24. Semantic Caching

Traditional caching usually matches exact requests.

Semantic caching can identify requests with similar meaning.

Example:

```text
"What is AWS Lambda?"
        ≈
"Explain AWS Lambda"
```

A semantic cache may reuse a suitable response if business correctness allows it.

### Important

Do not use semantic caching where stale or approximate answers are unacceptable.

---

# 25. Cache Invalidation

Caching introduces a critical question:

> When should cached data be considered invalid?

Strategies include:

- TTL
- Version-based invalidation
- Event-driven invalidation
- Explicit invalidation

### SAP-C02 trap

Caching improves performance but can create stale data.

---

# 26. Batch Processing

Batching combines multiple requests into a single processing operation.

Example:

```text
Request 1 ─┐
Request 2 ─┤
Request 3 ─┼→ Batch → Model
Request 4 ─┘
```

Benefits:

- Better hardware utilization
- Higher throughput
- Potentially lower cost

Trade-off:

- Can increase individual request latency

---

# 27. Continuous Batching

Continuous batching dynamically adds incoming requests to an active inference batch.

Useful for high-throughput model serving.

Concept:

```text
Active Inference
   ↓
New request arrives
   ↓
Add to available batch
```

Can improve accelerator utilization.

---

# 28. Latency vs Throughput

This is an important optimization trade-off.

### Optimize for latency

Prefer:

- Smaller model
- Smaller prompts
- Caching
- Fewer retrieval operations
- Fast inference
- Reduced network hops

### Optimize for throughput

Prefer:

- Batching
- Continuous batching
- Efficient accelerators
- Parallel processing
- Higher concurrency

### Important

The best throughput optimization may increase individual request latency.

---

# 29. Concurrency

Concurrency controls how many requests execute at the same time.

Too low:

```text
Capacity unused
```

Too high:

```text
Resource saturation
 ↓
Throttling
 ↓
Latency increase
```

Tune concurrency based on actual capacity.

---

# 30. Autoscaling

Autoscaling adjusts compute capacity according to demand.

Concept:

```text
Traffic ↑
 ↓
Capacity ↑

Traffic ↓
 ↓
Capacity ↓
```

Benefits:

- Handles variable demand
- Controls costs
- Prevents resource exhaustion

---

# 31. Scaling Signals

Useful signals may include:

- CPU utilization
- Memory utilization
- Request count
- Queue depth
- Request latency
- Concurrent requests
- Accelerator utilization

For GenAI, generic CPU utilization may not represent the true bottleneck.

---

# 32. Queue-Based Scaling

A queue can provide a strong scaling signal.

```text
Requests
   ↓
SQS
   ↓
Queue Depth
   ↓
Scale Workers
```

If queue depth increases:

```text
More workers
```

If queue depth decreases:

```text
Reduce workers
```

---

# 33. Provisioned Capacity vs On-Demand

Some managed inference services support different capacity models.

### On-demand

Useful for:

- Variable workloads
- Unpredictable traffic
- Lower operational commitment

### Provisioned capacity

Useful for:

- Predictable high-volume traffic
- Consistent latency requirements
- Reserved/committed capacity requirements

Always evaluate cost and utilization.

---

# 34. Provisioned Throughput

Provisioned throughput can provide dedicated model capacity for supported managed model workloads.

Think:

```text
Predictable high-volume workload
+
Consistent performance requirement
        ↓
Provisioned capacity option
```

### Exam clue

If traffic is predictable and sustained and the requirement emphasizes consistent throughput/latency, investigate provisioned capacity rather than relying entirely on on-demand behavior.

---

# 35. AWS Inferentia2

AWS Inferentia2 is designed for machine learning inference workloads.

Benefits can include:

- High inference performance
- Improved price/performance for supported workloads
- Purpose-built ML acceleration

Concept:

```text
Model Inference
      ↓
Inferentia2
      ↓
Accelerated inference
```

### SAP-C02 clue

If the question emphasizes:

> Cost-effective high-performance inference on AWS-designed accelerators

Think:

**Inferentia2**

---

# 36. AWS Trainium

AWS Trainium is designed primarily for ML model training and related deep-learning workloads.

Concept:

```text
Model Training
      ↓
Trainium
      ↓
Accelerated training
```

### Memory

```text
Inferentia → Inference
Trainium   → Training
```

This is a useful exam shortcut, while remembering that AWS accelerator capabilities can evolve.

---

# 37. GPU vs Inferentia2

| Requirement | Consider |
|---|---|
| General/custom GPU workloads | GPU |
| Supported high-performance inference | Inferentia2 |
| Specialized ML inference optimization | Inferentia2 |
| Broad ecosystem compatibility | GPU may be preferable |
| Existing CUDA-heavy workload | GPU may simplify migration |

Choose based on:

- Model/framework support
- Performance
- Cost
- Engineering effort
- Availability

---

# 38. Trainium vs Inferentia2

| Trainium | Inferentia2 |
|---|---|
| Primarily training | Primarily inference |
| Training acceleration | Inference acceleration |
| Model development | Model serving |
| Optimize training workloads | Optimize inference workloads |

### Memorize

```text
Trainium → Train
Inferentia → Infer
```

---

# 39. SageMaker Inference Optimization

SageMaker can provide configurable model deployment patterns.

Optimization areas include:

- Instance selection
- Autoscaling
- Model compilation/optimization where supported
- Batching
- Endpoint configuration
- Multi-model deployment where appropriate
- Asynchronous inference for long-running jobs

---

# 40. Real-Time Inference

Real-time inference is appropriate when users need low-latency responses.

```text
Request
 ↓
Endpoint
 ↓
Immediate Response
```

Use for:

- Interactive applications
- APIs
- Customer-facing predictions
- Low-latency GenAI workflows

---

# 41. Asynchronous Inference

Asynchronous inference is useful when requests:

- Take longer
- Have large payloads
- Do not require immediate response
- Have variable processing time

Concept:

```text
Request
 ↓
Queue
 ↓
Inference
 ↓
Result
```

Benefits:

- Better handling of long-running jobs
- Queue-based buffering
- Reduced synchronous pressure

---

# 42. Serverless vs Provisioned

Serverless-style services are useful for:

- Variable traffic
- Event-driven workloads
- Minimal infrastructure management

Provisioned infrastructure can be preferable for:

- Predictable high utilization
- Strict latency requirements
- Specialized hardware
- Long-running workloads

### SAP-C02 rule

Choose based on workload characteristics rather than assuming serverless is always cheaper.

---

# 43. Cold Starts

Serverless and dynamically provisioned environments may experience cold-start latency.

Potential strategies:

- Provisioned concurrency where supported
- Keep frequently used resources warm
- Reduce initialization work
- Use smaller deployment packages
- Select appropriate runtime architecture

---

# 44. Network Optimization

Runtime performance can also be affected by networking.

Optimize:

- Number of network hops
- Cross-Region traffic
- Cross-AZ traffic where relevant
- NAT dependencies
- DNS resolution
- Payload size

Keep latency-sensitive components geographically and architecturally close when appropriate.

---

# 45. Payload Optimization

Large payloads increase:

- Network latency
- Serialization/deserialization cost
- Processing time
- Memory usage

For GenAI:

```text
Huge prompt
 ↓
Higher input tokens
 ↓
Higher latency/cost
```

Minimize unnecessary payload/context.

---

# 46. Parallel Processing

Independent operations can run concurrently.

Sequential:

```text
A
 ↓
B
 ↓
C
 ↓
D
```

Parallel:

```text
     ┌→ B ─┐
A ───┼→ C ─┼→ D
     └→ E ─┘
```

If B, C, and E are independent, parallelism can reduce total latency.

---

# 47. GenAI Tool Calls

Agentic applications may invoke multiple tools.

Bad:

```text
Tool A
 ↓
Tool B
 ↓
Tool C
 ↓
Tool D
```

If independent:

```text
     ┌→ Tool A ─┐
Request → Tool B ─┼→ Combine
     └→ Tool C ─┘
```

Parallel tool calls can reduce latency.

---

# 48. Streaming

Streaming allows users to receive output incrementally.

```text
Request
 ↓
Model
 ↓
Token 1
Token 2
Token 3
...
```

It can improve **perceived latency**, even when total generation time remains similar.

### Important

Streaming does not necessarily reduce total computation.

It improves user experience and time-to-first-token.

---

# 49. Cost Optimization

GenAI runtime cost is influenced by:

- Model choice
- Input tokens
- Output tokens
- Requests
- Inference capacity
- Accelerator type
- Idle resources
- Retrieval operations

Optimization:

```text
Right Model
+
Right Context
+
Right Capacity
+
Right Scaling
+
Caching
```

---

# 50. Cost vs Quality

A larger model may improve quality but increase:

- Cost
- Latency
- Memory requirements

Example:

```text
Large Model
Quality ↑
Cost ↑
Latency ↑

Smaller Model
Cost ↓
Latency ↓
Quality may ↓
```

Choose according to business requirements.

---

# 51. Cost vs Latency

A common trade-off:

```text
More provisioned capacity
       ↓
Lower latency
       ↓
Higher cost
```

Versus:

```text
Less capacity
       ↓
Lower cost
       ↓
Potentially higher latency
```

The exam often expects the solution that meets the SLA at the lowest reasonable cost.

---

# 52. Performance Testing

Do not optimize based only on assumptions.

Measure:

- p50 latency
- p90 latency
- p95 latency
- p99 latency
- Throughput
- Error rate
- Token usage
- Accelerator utilization
- Queue depth
- Cache hit rate

---

# 53. Tail Latency

Average latency can hide bad user experiences.

Example:

```text
Average = 200 ms
p99 = 5 seconds
```

Most users are fast, but a small percentage experience very high latency.

For enterprise applications, tail latency can be critical.

---

# 54. Observability for Runtime Optimization

Monitor:

```text
Metrics
Logs
Traces
```

GenAI-specific signals:

- TTFT
- Tokens/sec
- Input tokens
- Output tokens
- Model latency
- Retrieval latency
- Vector search latency
- Cache hit ratio
- Throttling
- Model errors
- Queue depth

---

# 55. Bottleneck Identification

Example:

```text
Total latency = 5 sec

API = 100 ms
Network = 200 ms
RAG = 3 sec
Model = 1.7 sec
```

Optimizing API from:

```text
100 ms → 50 ms
```

only saves 50 ms.

Optimizing RAG:

```text
3 sec → 1 sec
```

saves 2 seconds.

### Golden rule

> **Optimize the largest contributor to end-to-end latency first.**

---

# 56. RAG Performance Architecture

A high-performance RAG architecture may look like:

```text
User
 ↓
API
 ↓
Query Processing
 ↓
Embedding
 ↓
Vector Search
 ↓
Reranking
 ↓
Prompt Construction
 ↓
LLM
 ↓
Streaming Response
```

Optimization opportunities:

```text
Embedding → Cache
Vector Search → Index tuning
Reranking → Limit candidates
Prompt → Reduce tokens
LLM → Right-size model
Response → Streaming
```

---

# 57. Vector Search Optimization

Consider:

- Index configuration
- Top-K
- Metadata filtering
- Query complexity
- Embedding dimensions
- Database sizing
- Network placement
- Caching

Avoid retrieving excessive candidates.

---

# 58. Metadata Filtering

Filtering before vector search can reduce the search space.

Example:

```text
Search entire database
        ↓
Millions of records
```

Better:

```text
Filter:
tenant = A
document_type = policy
region = US
        ↓
Smaller search space
        ↓
Vector search
```

Benefits:

- Lower latency
- Better relevance
- Lower compute

---

# 59. Multi-Tenant Runtime Optimization

For SaaS GenAI:

```text
Tenant A
Tenant B
Tenant C
```

Consider:

- Tenant isolation
- Fair resource allocation
- Rate limits
- Per-tenant quotas
- Caching
- Noisy-neighbor prevention

One tenant should not consume all inference capacity.

---

# 60. Noisy Neighbor

A noisy neighbor is one workload/tenant consuming disproportionate shared resources.

Example:

```text
Tenant A → 95% capacity
Tenant B → Starved
Tenant C → Starved
```

Mitigation:

- Rate limiting
- Quotas
- Separate capacity
- Priority queues
- Fair scheduling

---

# 61. Priority Queues

Not all requests have equal importance.

Example:

```text
High Priority
    ↓
Critical Requests

Medium Priority
    ↓
Normal Requests

Low Priority
    ↓
Batch Requests
```

During capacity constraints, critical requests can receive priority.

---

# 62. Load Testing

Test realistic workloads:

- Normal traffic
- Peak traffic
- Burst traffic
- Concurrent users
- Large prompts
- Long outputs
- RAG-heavy queries
- Model throttling

Measure:

```text
Latency
Throughput
Cost
Error Rate
Capacity
```

---

# 63. Optimization Workflow

Use this process:

```text
1. Define SLA
       ↓
2. Measure baseline
       ↓
3. Identify bottleneck
       ↓
4. Optimize application/model
       ↓
5. Test
       ↓
6. Measure again
       ↓
7. Validate cost
       ↓
8. Deploy
       ↓
9. Monitor
```

---

# 64. SAP-C02 Scenario Mapping

| Scenario | Think |
|---|---|
| Managed foundation model access | Amazon Bedrock |
| Custom model/inference infrastructure | SageMaker |
| Lower inference memory | Quantization |
| Smaller model with acceptable quality | Distillation |
| Remove unnecessary model parameters | Pruning |
| Reduce input/output cost | Token optimization |
| Reduce repeated model calls | Caching |
| High-volume model inference | Batching / continuous batching |
| Predictable sustained inference | Provisioned capacity |
| Variable traffic | Autoscaling / on-demand |
| Low-latency interactive inference | Real-time inference |
| Long-running asynchronous requests | Async inference |
| Model inference accelerator | Inferentia2 |
| ML training accelerator | Trainium |
| Reduce RAG latency | Retrieval/index/context optimization |
| Reuse embeddings | Embedding cache |
| Prevent tenant resource abuse | Quotas / rate limits |
| Improve perceived response latency | Streaming |
| Reduce end-to-end latency | Optimize largest bottleneck |
| Global low-latency users | Regional placement / global routing |

---

# 65. Common SAP-C02 Exam Traps

## Trap 1 — Always choose the largest model

Wrong.

Choose the smallest model that meets quality requirements.

---

## Trap 2 — SageMaker is always better than Bedrock

Wrong.

Bedrock is often preferable when the requirement is managed foundation-model access with minimal infrastructure management.

---

## Trap 3 — Bigger instance automatically solves latency

Not necessarily.

First identify the actual bottleneck.

---

## Trap 4 — More context always improves quality

Not necessarily.

Excessive context can increase:

- Cost
- Latency
- Noise

Use relevant context.

---

## Trap 5 — Batching always improves latency

Not necessarily.

Batching usually improves throughput and utilization but can increase individual request latency.

---

## Trap 6 — Caching is always safe

Wrong.

Caching can return stale or inappropriate results.

---

## Trap 7 — Streaming reduces total inference time

Not necessarily.

Streaming mainly improves perceived latency and time-to-first-token.

---

## Trap 8 — Serverless is always cheaper

Wrong.

Cost depends on traffic, duration, concurrency, initialization, and capacity model.

---

## Trap 9 — GPU is always the best accelerator

Wrong.

Inferentia2 can be attractive for supported inference workloads.

---

## Trap 10 — Trainium is an inference-only accelerator

Wrong.

Think:

```text
Trainium → Training
Inferentia2 → Inference
```

---

# 66. Optimization Decision Tree

```text
Performance problem
        ↓
What is the bottleneck?
        │
 ┌──────┼────────┬──────────┐
 ↓      ↓        ↓          ↓
Model  RAG     Network    Capacity
 ↓      ↓        ↓          ↓
Model  Index   Payload    Scaling
size   tuning  reduction  / batching
 ↓      ↓
Quant. Cache
Distill Top-K
 ↓
Measure again
```

---

# 67. GenAI Model Selection Decision Tree

```text
Need foundation model?
        ↓
Want managed model access?
        │
       YES
        ↓
     Bedrock
        │
       NO
        ↓
Custom model / infrastructure control
        ↓
     SageMaker
        │
        ↓
Need lower inference resource usage?
        ↓
Quantization / Distillation / Pruning
        │
        ↓
Need high throughput?
        ↓
Batching / Continuous Batching / Scaling
        │
        ↓
Need low latency?
        ↓
Smaller model + caching + optimized context
```

---

# 68. Accelerator Decision Tree

```text
ML workload
    ↓
Training?
 │       │
YES     NO
 │       │
 ↓       ↓
Trainium  Inference
             ↓
       Supported accelerator?
          │        │
         YES       NO
          ↓         ↓
     Inferentia2    GPU
```

Always validate framework/model compatibility and operational requirements.

---

# 69. Latency Optimization Checklist

```text
□ Reduce model size where acceptable
□ Reduce input tokens
□ Reduce output tokens
□ Optimize RAG retrieval
□ Reduce top-K
□ Cache embeddings
□ Cache repeated responses where safe
□ Parallelize independent operations
□ Reduce network hops
□ Use streaming
□ Tune concurrency
□ Use appropriate inference capacity
□ Avoid unnecessary tool calls
□ Measure p95/p99 latency
```

---

# 70. Cost Optimization Checklist

```text
□ Right-size model
□ Reduce unnecessary tokens
□ Cache repeated requests
□ Avoid redundant embeddings
□ Optimize retrieval
□ Use batching where appropriate
□ Scale dynamically
□ Avoid idle provisioned capacity
□ Evaluate Inferentia2
□ Evaluate model routing
□ Monitor token usage
□ Monitor utilization
```

---

# 71. Performance vs Cost Matrix

| Optimization | Latency | Throughput | Cost | Risk |
|---|---|---|---|---|
| Smaller model | ↓ | ↑ | ↓ | Quality may decrease |
| Quantization | ↓ | ↑ | ↓ | Quality may decrease |
| Distillation | ↓ | ↑ | ↓ | Quality may decrease |
| Caching | ↓ | ↑ | ↓ | Stale result |
| Batching | May ↑ | ↑ | ↓ | Higher per-request latency |
| More capacity | ↓ | ↑ | ↑ | Underutilization |
| Streaming | Perceived ↓ | — | — | More complex UX |
| RAG context reduction | ↓ | ↑ | ↓ | May reduce context |
| Model routing | ↓ | ↑ | ↓ | Routing complexity |
| Parallel calls | ↓ | ↑ | ↑/— | More concurrent load |

---

# 72. Must-Memorize Concepts

```text
Latency
Throughput
Concurrency
TTFT
Tokens/sec
Model selection
Model routing
Amazon Bedrock
Amazon SageMaker
Quantization
Distillation
Pruning
Prompt optimization
Token optimization
Context optimization
RAG
Vector search
Reranking
Embedding cache
Semantic cache
Batching
Continuous batching
Autoscaling
Provisioned capacity
Real-time inference
Asynchronous inference
Inferentia2
Trainium
GPU
Streaming
Rate limiting
Priority queues
Noisy neighbor
Observability
p95/p99
```

---

# 73. 30-Second Revision

```text
First → Measure
        ↓
Find bottleneck
        ↓
Choose right model
        ↓
Reduce unnecessary tokens/context
        ↓
Optimize RAG
        ↓
Cache repeated work
        ↓
Batch for throughput
        ↓
Scale for demand
        ↓
Use appropriate accelerator
        ↓
Monitor latency + cost
```

### Key memory shortcuts

```text
Bedrock      → Managed foundation models
SageMaker    → Custom ML/model control

Quantization → Lower precision / lower memory
Distillation → Smaller student model
Pruning      → Remove less-important parameters

Inferentia2  → Inference
Trainium     → Training

Batching     → Throughput
Streaming    → Perceived latency / TTFT
Caching      → Avoid repeated work
Autoscaling  → Variable demand
```

---

# 74. SAP-C02 Golden Rules

1. **Measure before optimizing.**
2. **Optimize the actual bottleneck, not the most expensive component.**
3. **Choose the smallest model that meets the business quality requirement.**
4. **Use Amazon Bedrock when managed foundation-model access is the priority.**
5. **Use SageMaker when custom model development/inference infrastructure control is required.**
6. **Use quantization to reduce model memory and potentially improve inference efficiency.**
7. **Use distillation to create smaller models that retain acceptable behavior.**
8. **Reduce unnecessary input/output tokens to lower latency and cost.**
9. **Use RAG to retrieve relevant context instead of sending entire datasets to the model.**
10. **Cache repeated computations when correctness and freshness requirements permit.**
11. **Use batching primarily to improve throughput and hardware utilization.**
12. **Do not assume batching reduces individual request latency.**
13. **Use streaming to improve perceived latency and time-to-first-token.**
14. **Use autoscaling for variable workloads.**
15. **Evaluate provisioned capacity for predictable sustained workloads with consistent performance requirements.**
16. **Inferentia2 is designed for inference workloads.**
17. **Trainium is designed primarily for model training.**
18. **Optimize RAG retrieval, indexing, top-K, reranking, and context size when retrieval is the bottleneck.**
19. **Use rate limiting and quotas to protect shared inference capacity.**
20. **Prevent noisy neighbors in multi-tenant workloads.**
21. **Use p95/p99 latency, not only average latency, for performance analysis.**
22. **A larger instance or model is not automatically the best solution.**
23. **Every performance optimization has trade-offs; validate quality, latency, throughput, and cost together.**
24. **The best SAP-C02 answer normally meets the stated SLA with the least unnecessary operational complexity and cost.**

---

# 75. Final SAP-C02 Mental Model

When you see a GenAI optimization question, ask:

```text
What does the business require?
        ↓
Latency or throughput?
        ↓
What is the current bottleneck?
        ↓
Model?
RAG?
Network?
Tokens?
Memory?
Compute?
Concurrency?
        ↓
Can I optimize the workload first?
        ↓
Can I use a smaller model?
        ↓
Can I reduce context/tokens?
        ↓
Can I cache?
        ↓
Can I batch?
        ↓
Can I scale?
        ↓
Do I need specialized hardware?
        ↓
Do I need Bedrock or SageMaker?
        ↓
Does the solution meet:
Quality + Latency + Throughput + Cost?
```

## Ultimate optimization formula

```text
Runtime Optimization
=
Right Model
+
Right Context
+
Efficient Retrieval
+
Caching
+
Right Capacity
+
Appropriate Scaling
+
Efficient Hardware
+
Continuous Measurement
```

> **Key takeaway:** For SAP-C02, do not solve every GenAI performance problem by adding larger compute. First identify the bottleneck, optimize model selection and token/context usage, improve RAG and caching, choose the correct inference strategy, and then scale or select specialized hardware such as Inferentia2 when justified. The best architecture balances **quality, latency, throughput, operational simplicity, and cost**.
