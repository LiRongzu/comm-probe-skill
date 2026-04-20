# Environment & Error Dictionary

## 1. Critical Environment Variables
| Variable | Effect | Diagnostic Purpose |
| :--- | :--- | :--- |
| `NCCL_DEBUG=INFO` | Logs topo & algorithm | Primary discovery tool |
| `NCCL_IB_DISABLE=1` | Disables InfiniBand | Force TCP fallback to test NIC hardware |
| `NCCL_P2P_DISABLE=1` | Disables NVLink/P2P | Force PCIe to test P2P/IOMMU stability |
| `NCCL_IB_GID_INDEX` | Sets RoCE GID | Fixes "Retry Exceeded" in RoCE networks |

## 2. Error Code Dictionary (C++ / OS level)
| Error String | Interpretation | Root Cause |
| :--- | :--- | :--- |
| `ibv_poll_cq failed` (Err 12) | Retry Exceeded | Network congestion or remote rank crash |
| `ibv_poll_cq failed` (Err 4) | Protection Error | Memory registration failure (often `ulimit` related) |
| `Watchdog timeout` | Silence failure | GPU "fell off the bus" (Xid error) or hard deadlock |
| `unhandled cuda error` | General crash | Often memory OOM or driver mismatch |

- **Knowledge Point**: `Xid 61` or `62` in `dmesg` confirms a hardware-level GPU hang.
