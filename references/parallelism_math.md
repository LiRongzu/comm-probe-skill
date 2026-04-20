# Parallelism & Communication Models

## 1. ZeRO Series Overhead Model
Volume of data moved per training step (where $\Phi$ is Model Params):

| Strategy | Comm Volume | Collective Op | When it triggers |
| :--- | :--- | :--- | :--- |
| **DDP** | $2\Phi$ | All-Reduce | Backward (Grad Sync) |
| **ZeRO-1/2**| $2\Phi$ | Reduce-Scatter | Backward (Grad Sharding) |
| **ZeRO-3** | $3\Phi$ | All-Gather + R-S | Forward (Param), Backward (Param + Grad) |

- **Agent Tip**: ZeRO-3 communication is $1.5 \times$ DDP. If you see huge All-Gather traffic during **Forward**, it's ZeRO-3's sharding mechanism, not a network bug.

## 2. Hybrid Parallelism Interaction
- **Tensor Parallel (TP)**: Intra-node ONLY. High-freq All-Reduce. Needs NVLink.
- **Pipeline Parallel (PP)**: Inter-node. Point-to-point (Send/Recv). Sensitive to NIC latency.
- **Data Parallel (DP)**: All-Reduce or Reduce-Scatter. Sensitive to cluster-wide bandwidth.

- **Knowledge Point**: PP Deadlocks usually happen because of `Send`/`Recv` mismatch in the pipeline schedule, not hardware failure.
