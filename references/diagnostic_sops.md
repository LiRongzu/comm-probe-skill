# Distributed Training Diagnostic SOPs

Derived from Meta (Llama 3), OpenAI, and AWS best practices.

## SOP 1: The "Straggler" Hunt (Performance Jitter)
1. **Symptom**: One node is slightly slower, causing the whole cluster to wait.
2. **Probe**: Run `nccl-tests` on all nodes simultaneously.
3. **Identifier**: Look for the node with the highest `max_time`.
4. **Root Cause**: Thermal throttling, background OS processes, or faulty NIC cable.

## SOP 2: The "GDR" Validation
1. **Symptom**: Multi-node training is 5x slower than expected.
2. **Probe**: Set `NCCL_NET_GDR_LEVEL=0` and compare.
3. **Diagnostic**: If performance *improves* or stays same, GPUDirect RDMA is not working.
4. **Fix**: Verify `nv_peer_mem` or `nvidia-peermem` kernel modules.

## SOP 3: P2P Deadlock Check
1. **Symptom**: Training hangs at step 0 during `dist.init_process_group`.
2. **Probe**: `NCCL_P2P_DISABLE=1`.
3. **Diagnostic**: If it runs (slowly) after disabling P2P, the issue is IOMMU/ACS blocking Peer-to-Peer access over PCIe.

## Citations & References
- [1] Meta Engineering Blog: [Introducing Llama 3: Training at Scale](https://ai.meta.com/blog/meta-llama-3/)
- [2] AWS Documentation: [Optimizing Distributed Training on AWS ParallelCluster](https://docs.aws.amazon.com/parallelcluster/latest/ug/deep-learning-optimization.html)
- [3] Microsoft Azure: [InfiniBand and NCCL Best Practices](https://learn.microsoft.com/en-us/azure/virtual-machines/sizes-gpu-high-performance-computing)
