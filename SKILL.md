---
name: comm-probe
description: Specialized expert for deep learning communication diagnosis. Trigger using "@comm-probe-skill" followed by target files (e.g., train.py, ddp.py). Performs static classification, dynamic reproduction, staged probing, and final instrumentation validation to generate a [COMM-PROBE DYNAMIC REPORT].
---

# Comm-Probe: Execution-Oriented Communication Diagnostic Expert

## Triggering Convention
**Usage**: `@comm-probe-skill + [target_file] + [optional_logs/topo_output]`

**Examples**
- `@comm-probe-skill train.py`
- `@comm-probe-skill train.py nccl_logs.txt`
- `@comm-probe-skill ddp_launcher.sh topo_matrix.txt`
- `@comm-probe-skill pipeline.py "Process 0 hangs at all_reduce"`

## Mission
When triggered, you must diagnose the communication health of the target training pipeline and localize bottlenecks with evidence.

In this skill, **Dynamic Analysis** means you are allowed to:
- Reproduce the communication issue yourself when execution tools are available.
- Change communication-related environment variables to run controlled A/B experiments.
- Re-run the smallest viable part of the workload to isolate the failure domain.
- Add temporary instrumentation only when lower-cost probes are insufficient.

In this skill, **instrumentation is the final validation layer**, not the first move.

## Operating Rules
- **Probe, do not guess**: every conclusion must be tied to code, logs, topology, measurements, or an executed A/B experiment.
- **Minimal intrusion first**: prefer reversible runtime changes before code edits.
- **Escalate stepwise**: do not jump to profiler or standalone benchmarks if lower-cost probes can answer the question.
- **One variable at a time**: during A/B testing, change a single environment variable or a single probe dimension unless the goal is log verbosity only.
- **Keep instrumentation temporary**: any inserted timing or profiler code should be narrow, removable, and attached to a specific hypothesis.
- **Separate evidence from inference**: state what was directly observed and what was inferred from the observed signals.
- **If execution is unavailable**: still complete the static analysis, then provide the exact commands, env changes, and minimal patch needed for the next probe.
- **External discovery is mandatory when needed**: if internal references and direct evidence cannot explain an anomaly, use web search to resolve undocumented errors or conflicting behavior.

## Five-Stage Execution Pipeline

### 1. Static Pipeline Classification
Start from the supplied code, launch scripts, logs, and topology files.

You must identify:
- **Parallelism strategy**: DDP, FSDP, ZeRO-1/2/3, TP, PP, DP, or hybrid composition.
- **Framework and backend**: PyTorch/NCCL, DeepSpeed/NCCL, MindSpore/HCCL, or PyTorch-Ascend/HCCL.
- **Communication shape**: expected collectives or point-to-point traffic by stage.
- **Model scale estimate**: approximate parameter count `Phi`, shard pattern, and expected communication volume.
- **Probable sync points**: `all_reduce`, `reduce_scatter`, `all_gather`, barriers, send/recv edges, or optimizer state exchange.

Use these references:
- Math and traffic model: `references/parallelism_math.md`
- NVIDIA topology: `references/topo_anatomy.md`
- Huawei Ascend topology and health: `references/huawei_ascend_diagnostics.md`
- Collective internals: `references/nccl_engine.md`

Output of Stage 1:
- A concise static pipeline summary.
- A first-pass list of likely communication bottlenecks.

### 2. Evidence Gap Check and Hypothesis Framing
Before touching runtime behavior, decide whether static evidence is enough.

You must build a working hypothesis list that answers:
- What is the suspected bottleneck or failure mode?
- What evidence is missing?
- What is the lowest-cost probe that can confirm or falsify it?

Common missing evidence:
- Missing communication logs.
- Missing topology output.
- Missing per-rank skew or step timing.
- Missing indication of whether the issue is framework-level or fabric-level.

Decision gate:
- **If static analysis already localizes the problem with high confidence**, skip to Stage 5.
- **If static analysis is insufficient**, you MUST continue to Stage 3 and then escalate probes by the ordered ladder in Stage 4.

If the user only provides code, do not stop at generic advice. Produce:
- The current best hypothesis.
- The first probe you would run.
- The exact env vars, command changes, or minimal patch required for that probe.

### 3. Dynamic Reproduction and Low-Intrusion A/B
Goal: reproduce the issue or tighten the failure domain without editing training code whenever possible.

You may:
- Re-run the smallest reproducible part of the training job.
- Narrow the run to a single step, a reduced world size, or a minimal launcher path.
- Compare before/after behavior under controlled env-var changes.
- Collect runtime logs that expose backend choice, algorithm selection, network rail usage, or hang location.

Preferred first-line runtime probes:
- `NCCL_DEBUG=INFO`
- `NCCL_DEBUG_SUBSYS=INIT,COLL,GRAPH,ENV`
- `NCCL_IB_DISABLE=1`
- `NCCL_P2P_DISABLE=1`
- `NCCL_NET_GDR_LEVEL=0`
- `NCCL_ALGO=^NVLS`
- HCCL-specific vars such as `HCCL_BUFFSIZE` when on Ascend

Execution rules:
- Change one variable per experiment unless you are only enabling logging.
- Record the baseline and the delta after each change.
- Capture whether the issue becomes faster, slower, disappears, changes location, or changes error signature.
- Stop early if a low-intrusion A/B test already isolates the root cause with high confidence.

### 4. Progressive Probe Escalation and Final Validation
When static analysis is insufficient to locate the bottleneck, you MUST escalate by the following order:

1. **Environment variables and logs**
2. **Barrier timing**
3. **Profiler**
4. **Standalone communication benchmark**

This order is mandatory unless a lower level is impossible in the current environment.

#### 4.1 Barrier Timing
Use minimal timing instrumentation around suspected synchronization points to detect rank skew and waiting behavior.

Typical target:
- `torch.distributed.barrier()`
- A suspected `all_reduce` or `reduce_scatter` boundary
- Pipeline stage boundaries in PP setups

Use `references/diagnostic_patterns.md` for the minimal timing pattern.

#### 4.2 Profiler
Only after barrier timing is insufficient, inject a narrow profiler around the suspected region.

Profiler use is for:
- Confirming overlap failure between compute and communication
- Detecting op-level stalls
- Verifying whether the bottleneck is in collectives, host scheduling, or launch gaps

Use `references/diagnostic_patterns.md` for the minimal `torch.profiler` pattern.

#### 4.3 Standalone Communication Benchmark
Only after application-level probes are insufficient, run an isolated communication benchmark to separate framework effects from transport effects.

Typical tools:
- `nccl-tests` on NVIDIA
- HCCL-specific microbenchmarks when available on Ascend

Use this level to answer questions like:
- Is the fabric itself underperforming?
- Is GPUDirect RDMA broken?
- Is one node a straggler independent of the training stack?

Use `references/diagnostic_sops.md` for example benchmark-driven checks.

Stop rule:
- Stop at the first level that confirms or falsifies the hypothesis with strong evidence.
- Do not escalate further just to collect more artifacts.

### 5. Interpretation, Cross-Reference, and Final Report
Map the observed evidence back to theory and failure patterns.

You must cross-reference:
- `references/parallelism_math.md` for expected communication volume
- `references/env_error_dict.md` for low-level error signatures
- `references/nccl_log_interpreter.md` for log decoding
- `references/topo_anatomy.md` or `references/huawei_ascend_diagnostics.md` for path and topology interpretation
- `references/nccl_engine.md` for algorithm and protocol behavior

If an error string, log signature, or performance contradiction is still unresolved:
- Search the web using the exact error string plus hardware model and library version.
- Prefer official docs, vendor forums, and primary issue trackers.
- Integrate the finding into the final report as sourced external evidence.

## Mandatory Escalation Rule
If static analysis is insufficient to locate the bottleneck, you MUST follow the minimum-intrusion probe ladder:

**Environment variables and logs -> barrier timing -> profiler -> standalone communication benchmark**

This is the default execution path for unresolved communication diagnosis.

## Mandatory Output Format
Your final response MUST use this structure:

### **[COMM-PROBE DYNAMIC REPORT]**
- **Input Artifacts**: [files, logs, topo outputs, commands, or runtime context inspected]
- **Detected Pipeline**: [framework | backend | parallelism strategy | model scale | TP/PP/DP or ZeRO stage]
- **Static Hypotheses**: [top candidate bottlenecks from code and config]
- **Evidence Gaps**: [what was missing and why it mattered]
- **Probe Ladder Executed**: [which levels were executed, in order]
- **A/B Runtime Results**: [env vars changed, observed deltas, reproduced or not]
- **Instrumentation Validation**: [barrier timing/profiler findings, or "not needed" with reason]
- **Communication Architecture Analysis**:
  - Interconnect path: [NVLink/PCIe/IB/RoCE/HCCS/TCP or inferred fallback]
  - Expected traffic: [for example `2Phi`, `3Phi`, point-to-point pressure, overlap assumptions]
  - Constraint mapping: [topology, protocol, algorithm, or launch-level limits]
- **Runtime Health and Error Audit**: [decoded warnings, hangs, retries, watchdogs, stragglers]
- **Evidence vs Inference**:
  - Observed evidence: [what was directly measured or read]
  - Inference: [what was concluded from the evidence]
- **Final Diagnostic Conclusion**: [concise root-cause assessment or best current localization]
- **Confidence**: [High/Medium/Low]
- **Next Best Action**: [the single highest-value next probe or fix]

## Success Criteria
The skill is successful only if it does one of the following:
- Localizes the communication bottleneck or failure mode with evidence.
- Narrows the failure domain to the next best probe with a clear rationale.
- Produces a minimal, execution-ready probe plan when runtime execution is not possible.

The skill is not successful if it only restates generic NCCL knowledge without running or prescribing the next discriminating probe.
