---
layout: post
title: "The Future of LLM Inference Belongs to CPUs "
date: 2025-09-14 10:00:00 +0800
categories: ai llm
---

## 1. Introduction
Large language models are rapidly evolving along three major trends:
- **Context Length Scaling**: Expanding context windows from thousands of tokens to the million level.
- **Total Parameter Scaling**: Growing from hundreds of billions to trillions of parameters.
- **MoE Sparse Activation**: Only a small subset of experts are activated per inference, drastically reducing computation.

These trends impose new hardware requirements: ultra-large memory, sparse-computation friendliness, and flexible scheduling capabilities.


## 2. Limitations of GPUs
- **Limited VRAM Capacity**: Million-token contexts require terabytes of KV cache, while GPUs typically only provide tens to hundreds of GB of VRAM.
- **Inefficient Sparse Computation**: MoE activates only a few experts, leaving GPU’s large matrix compute units underutilized.
- **High Communication Overhead**: Distributed inference requires All-to-All communication, stressing high-bandwidth interconnects.
- **High Cost and Power Consumption**: Large-scale GPU inference clusters consume massive energy and entail high procurement costs.

> GPUs excel at dense computation, but face efficiency bottlenecks when handling ultra-long contexts, massive parameters, and sparse activations.


## 3. Advantages of CPUs (Intel Xeon)
- **Large Memory Capacity**: Supports DDR5 / MRDIMM / CXL expansions up to terabytes, accommodating ultra-long contexts and KV caches.
- **Improved Memory Bandwidth**: Each CPU generation nearly doubles DDR5 MRDIMM bandwidth, meeting large model demands.
- **Sparse-Friendly**: General-purpose CPU instructions are flexible, well-suited for small-batch and irregular MoE computations.
- **AMX Matrix Units**:
  - Performance is not constrained by the number of CPU cores.
  - **High matrix efficiency, with only a small gap compared to GPUs**, sufficient for large-model GEMM operations.
  - General-purpose cores handle control logic, sparse routing, and dynamic scheduling, enabling CPUs to combine high matrix throughput with flexibility.
- **Cost and Ubiquity**: Lower power, manageable cost, and flexible deployment.

> The combination of CPU + AMX + high-bandwidth DDR5 MRDIMM memory provides near-GPU matrix performance while supporting terabyte-scale contexts and sparse computations, making CPUs the ideal platform for future inference.


## 4. Case Study: Qwen-480B
Qwen-480B is a representative ultra-large model:
- **Total Parameters**: ~480B
- **MoE Sparse Activation**: Only 35B parameters (~7%) activated per inference, leading to sparse computation loads.
- **GPU Challenges**:
  - Insufficient VRAM to store all KV caches.
  - Sparse activation reduces compute utilization and increases communication overhead.
- **CPU + AMX Fit**:
  - Terabyte-scale memory supports ultra-long contexts.
  - **AMX matrix computation efficiency close to GPUs**, sufficient for MoE GEMM workloads.
  - General-purpose cores handle control logic and routing, ensuring efficient CPU resource usage.

| Metric       | Qwen-480B Total Params | Activated Params | GPU Challenges          | CPU + AMX Advantages                |
| ------------ | ---------------------- | ---------------- | ----------------------- | ----------------------------------- |
| Parameters   | 480B                   | 35B              | Insufficient VRAM       | Memory expandable to TB scale       |
| Context Len  | Million tokens         | -                | KV cache bottleneck     | Large memory support                |
| Computation  | Sparse MoE             | Small batch      | Low utilization, comms  | AMX matrix ops + general-core logic |
| Memory BW    | -                      | -                | Limited HBM             | DDR5 MRDIMM doubles each generation |
| Cost/Deploy  | High                   | -                | Expensive, power-hungry | Affordable, widespread, scalable    |

### Processor Comparison: CPU vs GPU

| Metric                    | CPU (Xeon 6900) | GPU (H20) | Ratio  |
| ------------------------- | --------------- | --------- | ------ |
| General Compute (Cores)   | 64              | 9,216     | 144x   |
| Matrix Perf (BF16 TFLOPS) | 64              | 149       | 2.33x  |
| Memory BW (TB/s)          | 0.845           | 4.0       | 4.73x  |
| Memory Capacity (TB)      | 3               | 0.141     | 0.047x |

## 5. Conclusion and Outlook
- Under the trends of **ultra-long context + massive parameters + MoE sparse activation**, GPUs are hitting bottlenecks.  
- Xeon CPUs with **AMX matrix units** are not constrained by general-core counts, narrowing the performance gap with GPUs, while retaining flexibility through general-purpose cores.  
- DDR5 MRDIMM bandwidth improvements ensure terabyte-scale context processing.  
- **The future of large-model inference belongs to CPUs**, as demonstrated by Qwen-480B, where CPU + AMX + high-bandwidth memory provide unmatched advantages in capacity, compute efficiency, and sparse workloads.

> In summary: The key metrics for future inference are no longer peak FLOPS, but memory capacity, sparse-computation adaptability, and scalability. With AMX matrix units, general-purpose cores, and high-speed memory, CPUs are well-positioned to dominate inference in the long term.

