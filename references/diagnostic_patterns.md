# Operational Trace & Probing Patterns

### **Pattern: Minimal Torch Profiler Injection**
When diagnosing op-level delays, suggest adding this block around the training loop:
```python
with torch.profiler.profile(
    activities=[torch.profiler.ProfilerActivity.CPU, torch.profiler.ProfilerActivity.GPU],
    schedule=torch.profiler.schedule(wait=1, warmup=1, active=3),
    on_trace_ready=torch.profiler.tensorboard_trace_handler('./log'),
    record_shapes=True,
    with_stack=True
) as prof:
    # training code...
    prof.step()
```

### **Pattern: Timing Distributed Barrier**
To detect "Stragglers" (slow nodes):
```python
import time
start = time.perf_counter()
torch.distributed.barrier()
duration = time.perf_counter() - start
print(f"Rank {rank} wait time: {duration:.4f}s")
```

### **Pattern: NCCL Environment Scan**
Essential variables to probe:
- `NCCL_DEBUG=INFO`
- `NCCL_DEBUG_SUBSYS=INIT,COLL,GRAPH,ENV`
- `NCCL_ALGO=^NVLS` (To check if specific algorithms are being bypassed)
