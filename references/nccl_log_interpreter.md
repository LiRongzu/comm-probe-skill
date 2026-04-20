# NCCL INFO Log Interpreter (Expert Level)

Use this guide to decode `NCCL_DEBUG=INFO` output during the initialization and execution phases.

## 1. Initialization Phase (Topology Discovery)
- **`NCCL INFO NET/IB : Using [1]devicetype/ext ...`**:
  - **Meaning**: Confirms if InfiniBand/RoCE is detected.
  - **Diagnostic**: If it shows `Using [0]`, NCCL has fallen back to TCP. Check `ibstat`.
- **`NCCL INFO GPU/Proxy : [0] GPU0 ...`**:
  - **Meaning**: Lists the proximity of GPUs.
  - **Diagnostic**: Look for `P2P : Yes` vs `P2P : No`. If `No` on an NVLink system, check `NCCL_P2P_DISABLE`.

## 2. Graph & Algorithm Selection
- **`NCCL INFO Trees [0] -1->0->1`**:
  - **Meaning**: NCCL has built a Tree topology.
  - **Diagnostic**: For $N > 8$ GPUs on Ethernet, Tree is mandatory for latency.
- **`NCCL INFO Ring 00 -> 01 -> 02`**:
  - **Meaning**: Ring topology selected.
  - **Diagnostic**: Best for large message sizes. If small messages use Ring, check `NCCL_ALGO`.

## 3. Runtime Errors & Warnings
- **`NCCL WARN : [0] Cuda failure 'unspecified launch failure'`**:
  - **Meaning**: Often a result of an Xid error or a kernel hang on a different GPU.
- **`NCCL INFO Channel 00 : ... [send] via NET/IB/0`**:
  - **Meaning**: Verifies the specific network rail being used.
  - **Diagnostic**: If only one rail is active on a multi-rail system (e.g., A100-80GB), check `NCCL_IB_HCA`.
