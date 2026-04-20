# Topology & Affinity Anatomy

## 1. nvidia-smi topo -m Decoding
Decode the paths between GPU $i$ and GPU $j$:

| Code | Meaning | Path Description | Bottleneck Factor |
| :--- | :--- | :--- | :--- |
| **NV#** | NVLink | Directly bonded by # NVLinks | None (Peak) |
| **PIX** | PCIe Bridge | Single PCIe switch traversal | Minor (PCIe Gen limit) |
| **PXB** | PCIe Switch | Multiple switches, no host bridge | Moderate |
| **PHB** | Host Bridge | Traverses CPU Root Complex | High (CPU/Memory load) |
| **SYS** | SMP/NUMA | Crosses CPU sockets (QPI/UPI) | Critical (NUMA latency) |

## 2. Network & GPU Affinity
For RDMA (InfiniBand/RoCE) to reach peak speed, the NIC must be on the same NUMA node as the GPU.

- **Knowledge Point**: `NCCL_NET_GDR_LEVEL=PHB` allows GPUDirect RDMA across the host bridge.
- **Agent Tip**: If `BusBW` is low in multi-node, compare `ibstat` local NIC to `nvidia-smi topo -m`. If GPU is on Socket 0 and NIC is on Socket 1, NCCL will traverse `SYS` unless `NCCL_TOPO_FILE` or `NCCL_CROSS_NIC=1` is configured.

## Citations & References
- [1] NVIDIA Management Library (NVML): [Topology Queries](https://docs.nvidia.com/deploy/nvml-api/group__nvmlTopologyQueries.html)
- [2] NVIDIA Support: [Helpful nvidia-smi Queries](https://developer.nvidia.com/nvidia-system-management-interface)
- [3] Mellanox/NVIDIA Networking: [Best Practices for GPUDirect RDMA](https://docs.nvidia.com/networking/display/winof2v230/gpudirect+rdma)
