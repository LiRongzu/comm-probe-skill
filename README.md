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

### Global (Recommended)
```bash
npx skills add github:LiRongzu/comm-probe-skill -g
```

### Local Workspace
```bash
gemini skills install github:LiRongzu/comm-probe-skill --scope workspace
```

## 📜 Principles
- **Probe, Don't Prescribe**: Focused on providing the most accurate "Status Report" of the communication layer.
- **Zero Overhead**: Prefers non-invasive analysis and temporary instrumentation guides.
