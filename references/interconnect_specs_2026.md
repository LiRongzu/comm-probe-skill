# Interconnect Specifications (2024-2026)

Theoretical peak bandwidths for diagnostic comparison.

| Platform | Interconnect | Peak Bandwidth (Bi-dir) | Expected NCCL BusBW | Typical Latency |
| :--- | :--- | :--- | :--- | :--- |
| **NVIDIA Rubin (R200)** | NVLink 6 | 3.6 TB/s | ~3.2 TB/s | < 1us |
| **NVIDIA Blackwell (B200)** | NVLink 5 | 1.8 TB/s | ~1.6 TB/s | < 1us |
| **NVIDIA H100** | NVLink 4 | 900 GB/s | ~400 GB/s | 1-2us |
| **NVIDIA A100** | NVLink 3 | 600 GB/s | ~260 GB/s | 2us |
| **Networking** | 800G OSFP | 100 GB/s | ~90 GB/s | 10-50us (RDMA) |
| **Networking** | 400G NDR | 50 GB/s | ~45 GB/s | 10-50us (RDMA) |

## Efficiency Calculation (Golden Rule)
- **BusBW = (Total Data Moved) / (Time * CorrectionFactor)**
- For All-Reduce: `BusBW = DataSize * 2 * (n-1/n) / Time`
- **Expert Target**: 
  - NVLink: > 80% Efficiency
  - IB/RoCE: > 85% Efficiency
  - Ethernet (TCP): ~40-60% Efficiency

## Citations & References
- [1] NVIDIA Blackwell Architecture Whitepaper: [B200 NVLink 5.0 Specifications](https://www.nvidia.com/en-us/data-center/blackwell-architecture/)
- [2] NVIDIA H100 Tensor Core GPU Architecture: [NVLink 4.0 Detailed Specs](https://resources.nvidia.com/en-us-tensor-core)
- [3] NVIDIA Hopper Architecture In-Depth: [NVLink and NVSwitch](https://developer.nvidia.com/blog/nvidia-hopper-architecture-in-depth/)
