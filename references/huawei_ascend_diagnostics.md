# Huawei Ascend (NPU) Diagnostic Guide

## 1. Interconnect Vocabulary (NPU vs GPU)
| Ascend Term | NVIDIA Equivalent | Description |
| :--- | :--- | :--- |
| **HCCL** | NCCL | Huawei Collective Communication Library |
| **HCCS** | NVLink | High-speed Cache Coherent System (Intra-node) |
| **RoCE v2** | InfiniBand/RoCE | Primary Inter-node network protocol |
| **npu-smi** | nvidia-smi | NPU management and monitoring tool |
| **CANN** | CUDA/NCCL Stack | Huawei's AI computing framework |

## 2. Topology Analysis (`npu-smi info -t topo`)
- **HCCS**: Confirms high-speed interconnect between NPUs. 
- **Diagnostic**: If HCCS is not detected between cards in the same tray, check for hardware training link failures.
- **RoCE Affinity**: On 910B clusters, each NPU typically has a dedicated 100G/200G RoCE port. **Critical**: Ensure the RoCE IP is bound to the correct NPU rank.

## 3. HCCL Performance Tuning (Environment Variables)
- `HCCL_DEBUG=INFO`: Enables detailed communication logs (similar to `NCCL_DEBUG`).
- `HCCL_BUFFSIZE`: Default is often 200MB. For large models (MoE), increasing this can reduce fragmentation but consumes NPU memory.
- `HCCL_CONNECT_TIMEOUT`: Increase this (e.g., to 600s) for large-scale cluster initialization to avoid "Heartbeat Timeout".

## 4. Common Ascend-Specific Issues
### **Issue: HCCL Heartbeat Timeout**
- **Symptom**: Training hangs during initialization.
- **Cause**: Slow CPU-NPU synchronization or network jitter.
- **Fix**: Check `HCCL_IF_IP` to ensure it points to the correct RoCE/network interface.

### **Issue: Low HCCS Bandwidth**
- **Symptom**: Intra-node communication is slower than 300GB/s (on 910B).
- **Cause**: NPU frequency throttling or PCIe contention.
- **Fix**: Check `npu-smi info -d health` for thermal or power issues.

### **Issue: NUMA Misalignment**
- **Diagnostic**: Ascend NPUs are highly sensitive to CPU NUMA binding.
- **Fix**: Use `taskset` or `numactl` to bind the training process to the NUMA node local to the NPU.

## 5. Citations & Official References
- [1] Huawei CANN Documentation: [HCCL User Guide](https://www.hiascend.com/document/detail/zh/CANNCommunityEdition/80RC1alpha2/softwaredev/hcclug/atlas_hccl_16_0001.html)
- [2] MindSpore Documentation: [Distributed Training Performance Optimization](https://www.mindspore.cn/tutorials/experts/zh-CN/master/parallel/overview.html)
- [3] Ascend Community: [Troubleshooting HCCL Communication](https://www.hiascend.com/forum/thread-0229126466395368149-1-1.html)
