# Comm-Probe: Universal Communication Diagnostic Expert

`comm-probe` is a cross-platform AI Agent Skill designed to provide expert-level diagnostics for deep learning communication layers. It is compatible with **Claude Code**, **Codex**, **Gemini CLI**, **Kimi Code**, and other agents supporting the Open Agent Skills ecosystem.

## 🚀 Overview
Unlike static documentation, `comm-probe` empowers your AI agent to perform **Dynamic Analysis** of your training scripts. It maps code-level parallelism strategies to hardware-level physical constraints using deep knowledge of NCCL internals, GPU topology, and distributed math models.

## 🛠 Supported Platforms
- **Claude Code**: Direct triggering via `@comm-probe-skill`.
- **Gemini CLI**: Native integration via `npx skills`.
- **Codex / GitHub Copilot**: Context-aware analysis.
- **Kimi Code / Windsurf**: Workflow-based diagnostic probing.

## 📖 Usage Pattern
To trigger a diagnostic probe, use the following convention:

```text
@comm-probe-skill [target_file] [optional_logs/topo]
```

### Examples
- **Basic Analysis**: `@comm-probe-skill train.py`
- **Full Cluster Diagnosis**: `@comm-probe-skill ddp_launcher.sh nccl_logs.txt topo_matrix.txt`
- **Deadlock Investigation**: `@comm-probe-skill pipeline.py "Process 0 is hanging at all_reduce"`

## 📊 What the Skill Probes
When invoked, the agent executes a diagnostic protocol to generate a **[COMM-PROBE DYNAMIC REPORT]**:
1. **Pipeline Extraction**: Identifies ZeRO stage, TP/PP/DP degrees, and model scale.
2. **Topology Mapping**: Decodes `nvidia-smi topo` paths (NVLink, PIX, PXB, PHB, SYS) to detect latency bottlenecks.
3. **Algorithmic Audit**: Predicts optimal NCCL Algorithms (Tree vs Ring) and Protocols (LL128/Simple) for your message sizes.
4. **Volume Validation**: Calculates theoretical vs. observed communication traffic ($3\Phi$ vs $2\Phi$).
5. **Error Decoding**: Translates low-level C++/OS errors (e.g., `ibv_poll_cq failed`) into actionable root causes.

## 📥 Installation

### 1. Open Agent Skills (Gemini CLI, Claude Code, etc.)
The easiest way to install is via the standard `npx skills` package manager:
```bash
npx skills add github:LiRongzu/comm-probe-skill -g
```

### 2. MCP (Model Context Protocol) for Claude
For **Claude Code** or **Claude Desktop**, you can add this as an MCP server. Copy the configuration from `mcp-config.json.example` to your local MCP settings.

### 3. Codex & GitHub Copilot
As a pure Markdown-based skill, you can simply **link the repository** or **copy the SKILL.md** context into your workspace's `.github/copilot-instructions.md` for context-aware diagnostics.

## 📜 Principles
- **Probe, Don't Prescribe**: Focused on providing the most accurate "Status Report" of the communication layer.
- **Zero Overhead**: Prefers non-invasive analysis and temporary instrumentation guides.

## 📚 References & Citations
The diagnostic logic in this skill is grounded in official documentation and industry-leading research:

### NCCL & Communication Internals
- **NVIDIA NCCL Documentation**: [Collective Operations Cost Model](https://docs.nvidia.com/deeplearning/nccl/user-guide/docs/overview.html) & [Protocols (LL, LL128, Simple)](https://github.com/NVIDIA/nccl/wiki/Protocols)
- **NVIDIA Developer Blog**: [Optimizing Multi-GPU Communication with NCCL](https://developer.nvidia.com/blog/optimizing-multi-gpu-communication-with-nccl/)
- **AWS HPC Blog**: [Debugging NCCL Performance on EC2](https://aws.amazon.com/blogs/hpc/debugging-nccl-performance-on-aws/)

### Hardware & Topology
- **NVIDIA Blackwell & Hopper Architecture**: [B200 NVLink 5.0 Whitepaper](https://www.nvidia.com/en-us/data-center/blackwell-architecture/) & [H100 Tensor Core GPU Architecture](https://resources.nvidia.com/en-us-tensor-core)
- **NVML API**: [Topology Queries & Affinity Best Practices](https://docs.nvidia.com/deploy/nvml-api/group__nvmlTopologyQueries.html)
- **Networking**: [GPUDirect RDMA Best Practices (NVIDIA/Mellanox)](https://docs.nvidia.com/networking/display/winof2v230/gpudirect+rdma)

### Parallelism & Scalability
- **ZeRO Paper**: [Memory Optimizations Toward Training Trillion Parameter Models (Rajbhandari et al.)](https://arxiv.org/abs/1910.02054)
- **Meta Engineering**: [Llama 3: Training at Scale (Meta AI Blog)](https://ai.meta.com/blog/meta-llama-3/)
- **PyTorch & DeepSpeed**: [FSDP Tutorials](https://pytorch.org/tutorials/intermediate/FSDP_tutorial.html) & [ZeRO-Offload (Microsoft)](https://www.deepspeed.ai/tutorials/zero/)
