# Architecture Patterns – Scalability and Performance
## SAP-C02 Exam Revision README

> **Target exam:** AWS Certified Solutions Architect – Professional (SAP-C02)
>
> **Course:** Architecture Patterns – Scalability and Performance
>
> **Duration:** 15 minutes
>
> **Level:** Advanced
>
> **Focus:** Generative AI scalability, inference performance, model size, memory, latency, throughput, Amazon Bedrock, Amazon SageMaker AI, Inferentia2, Trainium, quantization, model optimization, autoscaling, and cost/performance trade-offs.

---

# 1. SAP-C02 Exam Focus

Generative AI workloads can be much more demanding than traditional application workloads because foundation models may have billions of parameters and require substantial compute and memory.

Think about three primary bottlenecks:

```text
                GenAI Performance
                      |
       ┌──────────────┼──────────────┐
       ↓              ↓              ↓
   Model Size     Inference       Memory
                  Compute
```

The architecture must balance:

```text
Quality
+
Latency
+
Throughput
+
Cost
+
Scalability
```

AWS provides managed services such as **Amazon Bedrock** and **Amazon SageMaker AI**, plus purpose-built accelerators such as **AWS Inferentia** and **AWS Trainium**. citeturn0search4turn0search1

---

# 2. Core Mental Model

When an SAP-C02 question gives you a GenAI performance problem, follow this sequence:

```text
Identify workload
      ↓
Identify model
      ↓
Measure traffic
      ↓
Identify bottleneck
      ↓
Choose model size
      ↓
Choose inference platform
      ↓
Optimize model
      ↓
Choose hardware
      ↓
Configure scaling
      ↓
Measure latency + throughput
      ↓
Optimize cost
```

---

# 3. The Three Major GenAI Constraints

## 3.1 Model Size

A model with more parameters generally requires more:

- Memory
- Compute
- Storage
- Bandwidth
- Cost

Example:

```text
8B model
    ↓
Less resource demand

70B model
    ↓
Much higher resource demand
```

### SAP-C02 principle

Do not automatically select the largest model.

Choose the **smallest model that meets the required quality and business objective**.

---

# 4. Inference Compute

Inference generates responses from a model.

GenAI inference can be computationally intensive.

A simplified flow:

```text
User Prompt
     ↓
Tokenization
     ↓
Model Processing
     ↓
Token Generation
     ↓
Response
```

Larger models generally require more compute.

---

# 5. Memory Requirements

Large models require substantial accelerator memory.

Think:

```text
Model Weights
+
KV Cache
+
Activations
+
Runtime Overhead
=
Memory Requirement
```

If the model does not fit efficiently in available memory, performance and deployment options can suffer.

AWS guidance recommends selecting infrastructure whose available high-bandwidth memory can satisfy model requirements, including using multiple GPUs/accelerators when needed. citeturn0search5

---

# 6. Model Size vs Performance

A common mistake is:

> Bigger model = better architecture.

Not necessarily.

A smaller model may provide:

- Lower latency
- Lower cost
- Higher throughput
- Easier scaling
- Lower memory requirements

If it meets the required response quality, it may be the better production choice.

---

# 7. Latency

Latency is how long the user waits.

For interactive GenAI:

```text
Request
   ↓
Inference
   ↓
Response
```

Important latency measurements include:

- Time to First Token (TTFT)
- Inter-token latency
- End-to-end request latency
- P50
- P90
- P95
- P99

Current SageMaker inference recommendations can benchmark these metrics when evaluating deployment configurations. citeturn0search3

---

# 8. TTFT – Time to First Token

TTFT measures the time before the first generated token is returned.

```text
Request
  |
  |---- TTFT ----|
                Token 1
```

For conversational applications, TTFT can strongly influence perceived responsiveness.

---

# 9. Inter-Token Latency

After the first token, users care about how quickly subsequent tokens arrive.

```text
Token 1
   ↓
Token 2
   ↓
Token 3
   ↓
Token 4
```

Lower inter-token latency generally means a smoother streaming experience.

---

# 10. Throughput

Throughput measures how much work the system can handle.

Examples:

```text
Requests / second
Tokens / second
Concurrent requests
```

A system can have:

```text
Low latency
but
Low throughput
```

or:

```text
High throughput
but
High latency
```

The architecture must optimize for the actual business requirement.

---

# 11. Latency vs Throughput

| Requirement | Optimization Focus |
|---|---|
| Interactive chatbot | Low latency / TTFT |
| Batch generation | High throughput |
| Many simultaneous users | Concurrency + throughput |
| Real-time application | Low P95/P99 latency |
| Cost-sensitive batch workload | Throughput per dollar |

### SAP-C02 clue

Always identify what the question prioritizes before choosing hardware or scaling.

---

# 12. Amazon Bedrock

Amazon Bedrock is a fully managed service for building generative AI applications using foundation models.

AWS describes Bedrock as the choice when you primarily want to use pre-trained foundation models for inference without managing the underlying infrastructure. citeturn0search4

Architecture:

```text
Application
     ↓
Amazon Bedrock
     ↓
Foundation Model
     ↓
Response
```

---

# 13. Why Choose Bedrock?

Choose Bedrock when the requirement emphasizes:

- Managed infrastructure
- Foundation model choice
- Minimal infrastructure management
- Automatic scaling
- Rapid GenAI application development
- AWS-managed inference infrastructure

AWS's inference guidance describes Bedrock as handling infrastructure provisioning and scaling for its managed inference layer. citeturn0search8turn0search9

---

# 14. Amazon SageMaker AI

SageMaker AI is appropriate when you need greater control over:

- Model deployment
- Model optimization
- Instance selection
- Inference containers
- Custom models
- Training/fine-tuning
- Scaling configuration

Architecture:

```text
Application
     ↓
SageMaker Endpoint
     ↓
Model
     ↓
GPU / Inferentia / Trainium
```

---

# 15. Bedrock vs SageMaker

| Requirement | Bedrock | SageMaker AI |
|---|---|---|
| Fully managed FM inference | Excellent | Possible |
| Minimal infrastructure management | Excellent | Less abstracted |
| Choose from supported FMs | Excellent | Also possible |
| Custom inference control | More limited | Strong |
| Instance-level control | Abstracted | Strong |
| Model optimization control | More abstracted | Strong |
| Training/fine-tuning control | Limited relative to SageMaker | Strong |
| Custom inference infrastructure | Limited | Strong |

AWS's current decision guide emphasizes that the choice depends on infrastructure management, model architecture, pricing, and the level of inference-engine/configuration control required. citeturn0search4turn0search9

---

# 16. Inferentia2

AWS Inferentia2 is a purpose-built accelerator optimized for inference.

Think:

```text
Inference workload
      ↓
AWS Inferentia2
      ↓
High performance + improved cost efficiency
```

AWS supports Inferentia2 through SageMaker for generative AI inference workloads. citeturn0search1

### SAP-C02 clue

If the question says:

> Optimize inference cost/performance using AWS purpose-built inference hardware.

Think:

**Inferentia2.**

---

# 17. Trainium

AWS Trainium is purpose-built primarily for machine-learning training workloads, while also supporting inference use cases.

Think:

```text
Training
   ↓
Trainium

Inference
   ↓
Inferentia2
```

But do not treat this as an absolute rule: Trainium can also be used for inference, including large models, depending on the architecture. AWS documents Trainium support for both training and inference. citeturn0search1turn0search2

---

# 18. Inferentia2 vs Trainium

| Requirement | Prefer evaluating |
|---|---|
| Inference-focused workload | Inferentia2 |
| Large-scale model training | Trainium |
| Fine-tuning/training | Trainium |
| Large-model inference | Inferentia2 or Trainium depending on model/workload |
| Need AWS Neuron ecosystem | Both |

### Golden rule

Do not select hardware from the chip name alone.

Benchmark the actual model and workload.

---

# 19. AWS Neuron

AWS Neuron is the software stack for AWS AI accelerators such as Inferentia and Trainium.

Concept:

```text
Model
  ↓
Neuron SDK / Compiler
  ↓
Inferentia / Trainium
```

SageMaker uses the Neuron SDK for compilation on Trainium and Inferentia. citeturn0search0

---

# 20. Quantization

Quantization reduces numerical precision used for model weights and/or activations.

Example:

```text
FP16
  ↓
INT8
  ↓
INT4
```

Lower precision can reduce:

- Memory footprint
- Hardware requirements
- Data movement
- Inference cost

AWS SageMaker supports techniques including INT4-AWQ, FP8, and INT8-SmoothQuant depending on the model. citeturn0search0

---

# 21. Quantization Trade-off

Quantization is not free.

Potential benefit:

```text
Smaller model representation
↓
Lower memory
↓
Lower cost
↓
Potentially faster inference
```

Potential downside:

```text
Lower precision
↓
Possible quality/accuracy degradation
```

Therefore:

```text
Quantize
   ↓
Benchmark
   ↓
Validate quality
   ↓
Deploy
```

---

# 22. Why Quantization Helps

Smaller weights can mean:

- Less memory consumption
- More room for KV cache
- Larger batch sizes
- Less data movement
- Better hardware utilization

AWS notes that reduced weight sizes can improve memory efficiency and decoding behavior. citeturn0search6

---

# 23. Compilation

Model compilation optimizes the model for the selected hardware.

Concept:

```text
Model
  ↓
Compiler
  ↓
Optimized Graph
  ↓
Target Hardware
```

SageMaker supports compilation for GPU, Trainium, and Inferentia hardware. citeturn0search0

---

# 24. Why Compilation Helps

Compilation can:

- Optimize the computational graph
- Improve inference performance
- Reduce deployment overhead
- Reduce scaling latency
- Produce hardware-specific optimized artifacts

SageMaker documentation notes that ahead-of-time compilation can reduce deployment and autoscaling latency because weights do not need just-in-time compilation on every new instance. citeturn0search0

---

# 25. Speculative Decoding

Speculative decoding uses:

```text
Small Draft Model
        ↓
Generate candidate tokens
        ↓
Large Target Model
        ↓
Validate candidates
```

This can improve generation efficiency and reduce latency while maintaining the target model's output quality when implemented appropriately. citeturn0search0turn0search11

---

# 26. Model Optimization Stack

A strong GenAI optimization sequence is:

```text
Choose appropriate model
        ↓
Quantize if appropriate
        ↓
Compile for target hardware
        ↓
Consider speculative decoding
        ↓
Choose instance/accelerator
        ↓
Benchmark
        ↓
Autoscale
```

Do not optimize hardware before understanding the model and workload.

---

# 27. Horizontal Scaling

Horizontal scaling means:

```text
1 Instance
   ↓
2 Instances
   ↓
4 Instances
   ↓
8 Instances
```

This is often the preferred approach for GenAI inference systems when additional replicas can serve more concurrent requests.

AWS guidance notes that GenAI inference commonly uses horizontal scaling to adjust capacity based on demand. citeturn0search5

---

# 28. Vertical Scaling

Vertical scaling means:

```text
Smaller GPU instance
       ↓
Larger GPU instance
```

Useful when:

- Model does not fit in current memory
- Single-instance performance is insufficient
- Larger accelerator memory is required

But vertical scaling has limits and can be expensive.

---

# 29. Horizontal vs Vertical

| Problem | Better Direction |
|---|---|
| More concurrent requests | Horizontal scaling |
| Model doesn't fit in memory | Vertical / larger accelerator / model parallelism |
| Need more throughput | Horizontal or optimized larger instance |
| Single instance saturated | Benchmark larger instance or add replicas |
| Variable traffic | Autoscaling |

---

# 30. Autoscaling

Autoscaling dynamically adjusts capacity.

```text
Low traffic
   ↓
Few instances

High traffic
   ↓
More instances
```

For SageMaker inference, scaling can use invocation-related or custom CloudWatch metrics. AWS guidance also highlights RPS, KV-cache usage, and queue length as useful GenAI load indicators. citeturn0search5

---

# 31. Useful GenAI Scaling Metrics

Important metrics include:

### Requests per second

```text
RPS ↑
→ Capacity requirement ↑
```

### Queue length

```text
Queue ↑
→ Demand exceeds current capacity
```

### KV Cache usage

```text
KV Cache ↑
→ Memory pressure / concurrency ↑
```

### Concurrent requests

```text
Concurrency ↑
→ Scale out
```

---

# 32. Queue Length

A growing queue means requests are waiting.

```text
Incoming Requests
       ↓
   [Queue]
       ↓
Inference
```

If the queue continually grows:

```text
Demand > Capacity
```

Scale out or optimize the inference system.

---

# 33. Cold Starts

Autoscaling can introduce startup delay.

Scale-out may require:

```text
Provision instance
      ↓
Pull container
      ↓
Load model
      ↓
Initialize runtime
      ↓
Ready for traffic
```

For very large models, model loading can be significant.

AWS guidance identifies cold-start latency as an important challenge for GenAI inference autoscaling. citeturn0search5

---

# 34. Scheduled Scaling

If traffic is predictable:

```text
09:00 → High traffic
18:00 → Low traffic
```

Use scheduled scaling where appropriate.

This can reduce reactive scale-out delay.

---

# 35. Reactive Scaling

For unpredictable traffic:

```text
Load increases
   ↓
Metric threshold
   ↓
Scale out
```

Use metrics that accurately represent actual GenAI workload pressure.

---

# 36. Combining Scaling Strategies

Best architecture:

```text
Scheduled Scaling
       +
Reactive Autoscaling
       +
Right-Sized Capacity
```

This can reduce both:

- Cold-start risk
- Unnecessary idle capacity

---

# 37. Batching

Batching combines multiple requests.

Concept:

```text
Request 1 ─┐
Request 2 ─┼→ Batch → Model
Request 3 ─┘
```

Benefits can include:

- Better accelerator utilization
- Higher throughput

Trade-off:

- Potentially increased latency

Therefore:

```text
Batching
→ Throughput optimization
→ May hurt latency
```

---

# 38. Continuous vs Batch Workloads

### Interactive

```text
Low latency
Low TTFT
Streaming
```

### Batch

```text
High throughput
Cost efficiency
Latency less critical
```

Architecture should reflect the workload.

---

# 39. Streaming Responses

Streaming sends generated tokens progressively.

```text
Token 1 → User
Token 2 → User
Token 3 → User
...
```

Benefits:

- Better perceived responsiveness
- Lower time to visible output

It does not necessarily reduce total generation time.

---

# 40. Model Parallelism

Very large models may not fit into one accelerator.

Use multiple accelerators:

```text
Model
 ├── Part 1 → Accelerator 1
 ├── Part 2 → Accelerator 2
 ├── Part 3 → Accelerator 3
 └── Part 4 → Accelerator 4
```

This is model parallelism.

AWS documents large-model inference patterns using multiple accelerators and Neuron-based tooling. citeturn0search1

---

# 41. Tensor Parallelism

Tensor parallelism splits model computation across accelerators.

```text
Large Model
     ↓
Tensor Parallelism
 ┌───┼───┐
 ↓   ↓   ↓
A1  A2  A3
```

Useful when the model cannot efficiently fit or run on a single accelerator.

---

# 42. KV Cache

During autoregressive generation, the KV cache stores attention-related state for previously processed tokens.

Larger context/concurrency can increase KV-cache memory requirements.

Concept:

```text
Longer Context
      +
More Concurrent Requests
      ↓
Higher KV Cache Usage
      ↓
More Memory Pressure
```

This is why KV-cache utilization can be a useful autoscaling signal. citeturn0search5

---

# 43. Context Length

Longer prompts increase processing work and memory requirements.

Example:

```text
1K tokens
   ↓
Lower resource demand

32K tokens
   ↓
Much higher resource demand
```

Do not configure extremely large context windows unless required.

---

# 44. Model Selection

Model choice is often the highest-impact optimization.

Evaluate:

- Accuracy
- Reasoning quality
- Context length
- Latency
- Throughput
- Memory
- Cost
- Supported modalities
- Fine-tuning requirements

A smaller model that meets requirements may outperform a larger model economically.

---

# 45. Cost Optimization Hierarchy

A useful sequence:

```text
1. Choose the right model
        ↓
2. Remove unnecessary tokens
        ↓
3. Quantize where appropriate
        ↓
4. Optimize compilation
        ↓
5. Choose right hardware
        ↓
6. Right-size instances
        ↓
7. Autoscale
        ↓
8. Benchmark price/performance
```

---

# 46. Benchmark Before Production

Do not choose infrastructure based only on theoretical specifications.

Test:

```text
Representative prompts
+
Expected concurrency
+
Expected input tokens
+
Expected output tokens
+
Real model
+
Candidate hardware
```

Then measure:

- TTFT
- P50/P90/P99 latency
- Throughput
- Cost
- Memory
- Queueing

SageMaker's current inference recommendations automate benchmarking across candidate configurations and can optimize for cost, latency, or throughput. citeturn0search3turn0search10

---

# 47. Price/Performance

The cheapest instance is not necessarily the cheapest solution.

Example:

```text
Instance A
$1/hour
100 req/s

Instance B
$2/hour
300 req/s
```

Instance B may have better:

```text
Cost per request
```

Always evaluate:

```text
Cost
÷
Useful throughput
```

---

# 48. Bedrock Scaling

Bedrock abstracts much of the infrastructure management.

Use it when the requirement emphasizes:

```text
Managed foundation-model inference
+
Automatic infrastructure management
+
Minimal operational burden
```

AWS describes Bedrock as managing provisioning and scaling for its inference layer. citeturn0search8turn0search9

---

# 49. SageMaker Scaling

SageMaker provides more explicit control over the inference environment.

You can control:

- Instance types
- Endpoint configuration
- Autoscaling
- Model deployment
- Optimization
- Containers
- Custom models

This makes SageMaker appropriate when the question emphasizes infrastructure and model-serving control.

---

# 50. Self-Managed Inference

For maximum control, organizations can use:

- Amazon EC2
- Amazon EKS
- Amazon ECS

AWS's inference architecture guidance identifies these as self-managed options when teams need greater infrastructure control. citeturn0search8turn0search9

Trade-off:

```text
More control
+
More operational responsibility
```

---

# 51. GenAI Inference Stack

Think of the stack as:

```text
Application
      ↓
Amazon Bedrock
      OR
SageMaker AI
      OR
EKS / ECS / EC2
      ↓
Inference Runtime
      ↓
GPU / Inferentia2 / Trainium
      ↓
Memory + Network
```

Choose the highest abstraction level that still satisfies requirements.

---

# 52. SAP-C02 Service Selection

| Requirement | Likely Direction |
|---|---|
| Use managed foundation models | Bedrock |
| Minimal infrastructure management | Bedrock |
| Custom inference control | SageMaker |
| Train/fine-tune models | SageMaker |
| Optimize model on specific hardware | SageMaker |
| Inference accelerator | Inferentia2 |
| Training accelerator | Trainium |
| Large model inference | Inferentia2 / Trainium based on benchmark |
| Maximum infrastructure control | EC2/EKS/ECS |
| Variable inference demand | Autoscaling / Bedrock managed scaling |
| Very large model | Model/tensor parallelism |
| Reduce memory | Quantization |
| Improve graph execution | Compilation |
| Reduce decoding latency | Speculative decoding |
| High throughput | Batching / scaling / optimization |
| Low user-perceived latency | Streaming + low TTFT |
| Growing request queue | Scale out |

---

# 53. Common SAP-C02 Exam Traps

## Trap 1 — Choose the biggest model

Wrong.

Choose the smallest model that satisfies quality requirements.

---

## Trap 2 — More GPUs always solve latency

Wrong.

The bottleneck could be:

- Model size
- Memory
- KV cache
- Data movement
- Runtime
- Network
- Queueing

---

## Trap 3 — Quantization has no downside

Wrong.

Lower precision can affect output quality/accuracy.

Always validate the quantized model.

---

## Trap 4 — Trainium is only for inference

Wrong.

Trainium is primarily associated with training but can also support inference workloads.

---

## Trap 5 — Inferentia2 is for training

Wrong.

Inferentia2 is purpose-built for inference.

---

## Trap 6 — More readers always improve write throughput

Not applicable to GenAI inference architecture.

Scaling replicas only helps if the architecture can distribute the workload.

---

## Trap 7 — Autoscaling has zero delay

Wrong.

Large-model loading can introduce cold-start latency.

---

## Trap 8 — Batch processing always improves the user experience

Wrong.

Batching improves utilization/throughput but may increase latency.

---

## Trap 9 — Bedrock requires managing GPU instances

Wrong.

Bedrock abstracts infrastructure management.

---

## Trap 10 — SageMaker is always cheaper than Bedrock

Wrong.

Compare the actual workload, utilization, operational overhead, and pricing model.

---

## Trap 11 — Vertical scaling is always best

Wrong.

For high concurrency, horizontal scaling may be better.

---

## Trap 12 — Horizontal scaling solves a model-memory problem

Not necessarily.

If one model cannot fit on an accelerator, you may need:

- Larger memory
- Quantization
- Model parallelism
- Different model
- Different accelerator

---

# 54. Scenario: Interactive Chatbot

Requirement:

> Thousands of users need fast responses and visible output quickly.

Think:

```text
Bedrock or SageMaker
      ↓
Streaming
      ↓
Low TTFT
      ↓
Autoscaling
      ↓
Appropriate model
      ↓
Latency benchmarking
```

---

# 55. Scenario: Large Model Does Not Fit

Requirement:

> Model cannot fit into one accelerator.

Consider:

```text
Quantization
      OR
Larger-memory accelerator
      OR
Model parallelism
      OR
Smaller model
```

Do not immediately add more independent inference instances; each instance still needs to fit the model unless using a distributed/model-parallel architecture.

---

# 56. Scenario: Cost Is Too High

Start with:

```text
Is the model too large?
      ↓
Can a smaller model meet quality?
      ↓
Can quantization reduce memory?
      ↓
Can hardware be optimized?
      ↓
Can autoscaling reduce idle capacity?
      ↓
Can batching improve utilization?
      ↓
Benchmark price/throughput
```

---

# 57. Scenario: Queue Is Growing

```text
Queue length ↑
      ↓
Demand > capacity
      ↓
Check:
- RPS
- Concurrency
- KV cache
- Instance utilization
      ↓
Scale out
OR
Optimize inference
```

AWS guidance specifically identifies queue length/pending requests as a useful signal for capacity pressure. citeturn0search5

---

# 58. Scenario: Latency Is High but CPU Is Low

Do not automatically scale CPU.

Investigate:

```text
Model size
KV cache
GPU/accelerator utilization
Memory bandwidth
Token generation
Queueing
Network
Inference runtime
```

GenAI inference can be memory-bandwidth-bound during token generation. citeturn0search11

---

# 59. Scenario: Predictable Daily Traffic

Example:

```text
08:00 → Low
09:00 → High
18:00 → Low
```

Consider:

```text
Scheduled Scaling
+
Reactive Autoscaling
```

This can pre-provision capacity before known peaks while still reacting to unexpected demand.

---

# 60. Scenario: Need Maximum Control

Requirement:

> The organization needs control over inference runtime, instance type, model server, networking, and scaling.

Likely direction:

```text
SageMaker
```

or, when even more infrastructure control is required:

```text
EC2 / EKS / ECS
```

The more control you require, the more operational responsibility you accept. citeturn0search9

---

# 61. Decision Tree

```text
GenAI workload
      ↓
Need foundation-model inference with minimal infrastructure?
      |
     YES
      ↓
Amazon Bedrock
      |
     NO
      ↓
Need model/inference infrastructure control?
      |
     YES
      ↓
SageMaker AI
      |
     NO
      ↓
Evaluate managed options
```

---

# 62. Performance Decision Tree

```text
Performance issue
       ↓
What is the bottleneck?
       |
 ┌─────┼───────────┬───────────┐
 ↓     ↓           ↓           ↓
Model Memory     Compute      Queue
 ↓     ↓           ↓           ↓
Smaller Quantize  Optimize    Scale
model   / bigger  hardware    out
        memory
```

---

# 63. Optimization Decision Tree

```text
Model too expensive?
      ↓
Smaller model possible?
      ↓
YES → Test smaller model

NO
 ↓
Quantization possible?
 ↓
YES → Quantize + validate

NO / insufficient
 ↓
Compile model
 ↓
Benchmark hardware
 ↓
Choose accelerator
 ↓
Autoscale
```

---

# 64. Cost vs Performance Matrix

| Technique | Performance | Cost | Risk/Trade-off |
|---|---|---|---|
| Smaller model | Often improves | Lower | Quality may decline |
| Quantization | Often improves | Lower | Accuracy/quality risk |
| Compilation | Improves | Can lower | Hardware/model-specific |
| Inferentia2 | High inference efficiency | Potentially lower | Compatibility/optimization required |
| Trainium | High compute efficiency | Potentially lower | Best fit depends on workload |
| More instances | Higher throughput | Higher | Idle capacity |
| Larger instance | More capacity | Higher | May be underutilized |
| Batching | Higher throughput | Better utilization | Higher latency |
| Streaming | Better perceived latency | Similar compute | Does not necessarily reduce total time |
| Autoscaling | Better utilization | Lower idle cost | Cold starts |

---

# 65. GenAI Production Checklist

```text
□ Business quality target defined
□ Model selected
□ Model size validated
□ Input token distribution known
□ Output token distribution known
□ Expected concurrency known
□ RPS known
□ Latency target defined
□ TTFT target defined
□ Throughput target defined
□ Memory requirement measured
□ KV-cache behavior measured
□ Candidate hardware benchmarked
□ Quantization evaluated
□ Compilation evaluated
□ Speculative decoding evaluated
□ Autoscaling configured
□ Cold-start impact measured
□ Cost per request measured
□ P50/P90/P99 latency measured
□ Failure/scaling behavior tested
```

---

# 66. 30-Second Revision

```text
GenAI
 ↓
Model Size + Compute + Memory
 ↓
Choose model
 ↓
Bedrock OR SageMaker
 ↓
Choose hardware
 ↓
Inferentia2 → inference
Trainium → training / supported inference
 ↓
Optimize
 ↓
Quantization
Compilation
Speculative Decoding
 ↓
Scale
 ↓
Horizontal Autoscaling
 ↓
Monitor
RPS / Queue / KV Cache / Concurrency
 ↓
Measure
TTFT / Latency / Throughput / Cost
```

### Memorize

```text
Bedrock
→ Managed GenAI inference

SageMaker
→ More model/infrastructure control

Inferentia2
→ Inference accelerator

Trainium
→ Training-focused accelerator; also supports inference

Quantization
→ Reduce model memory / precision

Compilation
→ Optimize model for hardware

Speculative decoding
→ Improve generation efficiency

Horizontal scaling
→ More inference capacity

KV cache
→ Important memory/scaling signal

Queue length
→ Capacity pressure

TTFT
→ Time until first token

Throughput
→ Amount of work handled
```

---

# 67. SAP-C02 Golden Rules

1. **GenAI performance is a combined model, compute, memory, and architecture problem.**
2. **Do not automatically choose the largest foundation model.**
3. **Choose the smallest model that meets the required quality.**
4. **Bedrock is the preferred direction when the requirement is managed foundation-model inference with minimal infrastructure management.**
5. **SageMaker AI is preferred when more model, inference, hardware, or deployment control is required.**
6. **Inferentia2 is purpose-built for inference.**
7. **Trainium is primarily designed for training but can also support inference workloads.**
8. **Benchmark the actual model and workload instead of choosing hardware from theoretical specifications alone.**
9. **Quantization reduces precision and can reduce memory and cost, but quality must be validated.**
10. **Compilation optimizes the model for the target hardware.**
11. **Speculative decoding can improve generation efficiency by using a smaller draft model with a larger target model.**
12. **Horizontal scaling is commonly used for GenAI inference capacity.**
13. **Vertical scaling is useful when a model needs more memory or single-instance capacity.**
14. **Adding independent inference instances does not solve a model-that-does-not-fit problem unless the architecture uses model parallelism.**
15. **RPS, concurrency, queue length, and KV-cache usage can be valuable scaling signals.**
16. **Autoscaling has cold-start considerations, especially for large models.**
17. **Scheduled scaling is useful for predictable traffic patterns.**
18. **Batching can improve throughput but may increase latency.**
19. **Streaming improves perceived responsiveness but does not necessarily reduce total generation time.**
20. **TTFT measures time to the first generated token.**
21. **P95/P99 latency can be more important than average latency for user-facing systems.**
22. **Longer context and higher concurrency can increase KV-cache memory requirements.**
23. **A model that fits memory is not necessarily the model with the best price/performance.**
24. **Cost per request or cost per useful token can be more meaningful than hourly instance price.**
25. **For production GenAI, benchmark representative prompts, token lengths, concurrency, and traffic patterns.**
26. **Use model optimization before blindly adding hardware.**
27. **Use autoscaling to reduce idle capacity while maintaining required performance.**
28. **The correct SAP-C02 answer usually balances quality, latency, throughput, scalability, cost, and operational complexity.**

---

# 68. Final SAP-C02 Mental Model

When you see a GenAI scalability question:

```text
What is the business requirement?
        ↓
Quality?
Latency?
Throughput?
Cost?
Availability?
        ↓
What model?
        ↓
How large?
        ↓
Does it fit memory?
        ↓
Bedrock or SageMaker?
        ↓
Which accelerator?
        ↓
Can it be quantized?
        ↓
Can it be compiled?
        ↓
Can speculative decoding help?
        ↓
How many concurrent requests?
        ↓
Horizontal autoscaling?
        ↓
What scaling metric?
        ↓
What is TTFT / P95 / P99?
        ↓
What is cost per request?
        ↓
Benchmark
        ↓
Deploy
        ↓
Monitor
        ↓
Continuously optimize
```

## Ultimate GenAI Scalability Formula

```text
GenAI Performance
=
Right Model
+
Right Precision
+
Right Hardware
+
Optimized Runtime
+
Right Scaling
+
Representative Benchmarking
```

> **SAP-C02 takeaway:** For GenAI workloads, don't solve every performance problem by adding bigger GPUs. Start with the **right model**, then optimize **memory and computation**, select the appropriate **Bedrock/SageMaker architecture and accelerator**, use **quantization/compilation/speculative decoding where appropriate**, and scale horizontally based on real workload signals such as **concurrency, queue length, RPS, and KV-cache usage**. Finally, validate **quality, TTFT, P95/P99 latency, throughput, and cost** using representative workloads.
