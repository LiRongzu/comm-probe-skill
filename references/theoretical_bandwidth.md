# Theoretical Bandwidth Reference

| Hardware | Interconnect | Theoretical (Uni/Bi) | Expected NCCL BusBW |
| :--- | :--- | :--- | :--- |
| **H100** | NVLink 4 | 450 GB/s / 900 GB/s | ~400 GB/s |
| **A100** | NVLink 3 | 300 GB/s / 600 GB/s | ~260 GB/s |
| **A100** | PCIe Gen4 x16 | 32 GB/s / 64 GB/s | ~24 GB/s |
| **V100** | NVLink 2 | 150 GB/s / 300 GB/s | ~120 GB/s |
| **Network** | 400G IB/Eth | 50 GB/s | ~40 GB/s |
| **Network** | 100G IB/Eth | 12.5 GB/s | ~10 GB/s |

## Common Efficiency Ratios
- **Excellent**: > 85% of theoretical
- **Good**: 70% - 85%
- **Poor**: < 50% (Likely config or topology issue)
