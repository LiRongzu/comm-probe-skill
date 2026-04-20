# NCCL Internals: Algorithms & Protocols

## 1. Algorithms (Collective Logic)
NCCL chooses between Ring and Tree based on a dynamic cost model.

| Algorithm | Topology | Message Size | Scaling | Cost Metric |
| :--- | :--- | :--- | :--- | :--- |
| **Ring** | Any (Best on NVLink) | Large (> 256KB) | $O(N)$ latency | Max BW utilization |
| **Tree** | Hierarchical / Multi-node | Small (< 256KB) | $O(\log N)$ latency | Min hop count |

- **Agent Tip**: If logs show `NCCL_ALGO=Ring` for small messages and high latency, suggest `NCCL_ALGO=Tree`.
- **Key Toggle**: `NCCL_ALGO=Tree` forces logarithmic scaling.

## Citations & References
- [1] NVIDIA NCCL Documentation: [Collective Operations - Cost Model](https://docs.nvidia.com/deeplearning/nccl/user-guide/docs/overview.html)
- [2] NCCL GitHub Wiki: [Protocols (LL, LL128, Simple)](https://github.com/NVIDIA/nccl/wiki/Protocols)
- [3] NVIDIA Developer Blog: [Optimizing Multi-GPU Communication with NCCL](https://developer.nvidia.com/blog/optimizing-multi-gpu-communication-with-nccl/)

## 2. Protocols (Data Movement)
Protocols handle the synchronization between GPU threads during data transfer.

| Protocol | Latency | Bandwidth | Use Case | Mechanism |
| :--- | :--- | :--- | :--- | :--- |
| **Simple** | High | 100% | Large data | Mem fences + System sync |
| **LL** | Ultra-Low | ~50% | Tiny messages | 8B packets (4B data + 4B flag) |
| **LL128** | Low | ~90% | Medium (NVLink) | 128B unrolled writes |

- **Agent Tip**: Check if `LL128` is active on NVLink systems. If it falls back to `Simple`, check for PCIe interference or old hardware.
