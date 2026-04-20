---
name: comm-probe
description: Specialized expert for deep learning communication diagnosis. Trigger using "@comm-probe-skill" followed by target files (e.g., train.py, ddp.py). Performs dynamic analysis of the training pipeline to generate a [COMM-PROBE EXPERT REPORT] based on NCCL internals, Topology, and Parallelism math.
---

# Comm-Probe: Universal Communication Diagnostic Expert

## Triggering Convention
**Usage**: `@comm-probe-skill + [target_file] + [optional_logs/topo_output]`
**Example**: "@comm-probe-skill train.py cluster_topo.txt"

## Mission
When triggered, you must perform a **Dynamic Analysis** of the provided files to probe the communication health of the training pipeline. You act as a clinical diagnostic layer that maps code-level implementation to hardware-level constraints.

## Universal Diagnostic Workflow

### 1. Code-Level Strategy Extraction (Dynamic Analysis)
Analyze the target files (e.g., `train.py`, `pipeline.py`) to identify:
- **Parallelism Strategy**: Is it DDP, FSDP, or DeepSpeed ZeRO (1/2/3)?
- **Model Scale**: Estimate parameter count $\Phi$ to calculate theoretical communication volume.
- **Framework & Backend**: Detecting PyTorch (NCCL) vs MindSpore/PyTorch-Ascend (HCCL).

### 2. Knowledge-Base Cross-Reference
Combine code analysis with your internal references to infer bottlenecks:
- **Mathematical Validation**: Use **`references/parallelism_math.md`**.
- **Topology Decoding**: 
  - For NVIDIA: Use **`references/topo_anatomy.md`**.
  - For Huawei Ascend: Use **`references/huawei_ascend_diagnostics.md`**.
- **Collective Logic**: 
  - For NVIDIA: Use **`references/nccl_engine.md`**.
  - For Huawei: Check HCCL specific variables like `HCCL_BUFFSIZE`.

### 3. Failure Mode Pattern Matching
If logs or error messages are provided:
- Use **`references/env_error_dict.md`** to translate low-level errors into root causes (e.g., "Retry Exceeded" -> Network Congestion).

### 4. Protocol E: External Discovery (Self-Correction)
If an anomaly (e.g., undocumented Error Code, unique log string, or contradictory performance data) cannot be resolved through internal references or logical reasoning:
- **Action**: You MUST proactively use your **Web Search** tools.
- **Search Strategy**: Combine the specific error string with hardware models and library versions (e.g., `"NCCL INFO NET/IB : Call to ibv_reg_mr failed" A100 NCCL 2.18`).
- **Integration**: Incorporate the latest community findings (GitHub Issues, NVIDIA Forums) into the final report.

## The Comm-Probe Expert Report (Mandatory Output)
Your final response MUST follow this structured format:

### **[COMM-PROBE DYNAMIC REPORT]**
- **Detected Pipeline**: [e.g., DeepSpeed ZeRO-3 | Model: 7B | TP=1, PP=1]
- **Communication Architecture Analysis**:
  - Interconnect Path: [e.g., NVLink detected via code patterns/topo]
  - Predicted Overhead: [e.g., ZeRO-3 will generate 3x parameter traffic]
- **Probed Constraints**:
  - [Constraint 1]: [e.g., Cross-socket SYS link detected, high latency expected for All-Reduce]
  - [Constraint 2]: [e.g., Small batch size might trigger sub-optimal Ring algorithm]
- **Runtime Health & Error Audit**: [e.g., No Xid errors in logs; all-reduce timing appears normal]
- **Final Diagnostic Conclusion**: [Concise technical summary of the current communication state]
