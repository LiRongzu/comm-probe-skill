# Expert Blog Insights & Heuristics

## 1. Stas Bekman (Hugging Face) - The "Straggler" Rule
- **Insight**: A single slow GPU (due to clock throttling) can degrade the entire cluster's BusBW by 50%+.
- **Heuristic**: If `nccl-tests` shows inconsistent results across runs, it's rarely the network; check `nvidia-smi -q -d CLOCK` for thermal throttling.
- **Reference**: [ml-engineering/network](https://github.com/stas00/ml-engineering/blob/master/network/)

## 2. Horace He - The Overlap Principle
- **Insight**: 100% communication/compute overlap is the goal, but "Bubble" analysis is more important than absolute bandwidth.
- **Heuristic**: In Profiler, if the `Stream` view shows large gaps between compute kernels where NCCL is inactive, your `bucket_size` might be too small.
- **Reference**: [horace.io](https://horace.io/)

## 3. AWS HPC Team - The GDR/P2P Matrix
- **Insight**: On AWS/Cloud, the instance topology is fixed, but the software layer (EFA/NCCL) often defaults to sub-optimal paths.
- **Heuristic**: Always verify `NCCL_NET_GDR_LEVEL=PHB` on P4d/P5 instances to ensure peer-memory access.
- **Reference**: [AWS HPC Blog](https://aws.amazon.com/blogs/hpc/debugging-nccl-performance-on-aws/)
