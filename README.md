# Comm-Probe Skill for Gemini CLI

A specialized diagnostic expert for deep learning communication. 

## Features
- **Topology Probe**: NVLink vs PCIe detection.
- **Bandwidth Benchmarking**: Automated `nccl-tests` analysis.
- **Instrumentation Guide**: Temporary `torch.profiler` and `dist.barrier()` injection.
- **Standardized Reports**: Unified [COMM-PROBE REPORT] output.

## Installation
```bash
npx skills add github:<your-username>/comm-probe-skill
```

## Usage
Trigger this skill by asking:
- "Check the communication topology."
- "Probe the bandwidth of current training."
- "Why is my training hanging at the gradient sync step?"
