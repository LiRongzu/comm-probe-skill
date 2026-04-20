# NCCL/Communication Error Codes & Diagnostics

### **ID: NET-IB-NOT-FOUND**
- **Log String**: `NET/IB : No device found`
- **Cause**: InfiniBand drivers (OFED) not installed or net devices down.
- **Fix**: Check `ibstat` or use `NCCL_IB_DISABLE=1` to fallback to TCP.

### **ID: P2P-NOT-ALLOWED**
- **Log String**: `P2P is not allowed` / `P2P Check Failed`
- **Cause**: Peer-to-Peer access blocked by IOMMU or BIOS settings.
- **Fix**: Set `NCCL_P2P_DISABLE=1` or check ACS settings in BIOS.

### **ID: TIMEOUT**
- **Log String**: `Watchdog: ... timeout` / `Call to ... failed : Timeout`
- **Cause**: Network congestion or a "straggler" node.
- **Fix**: Check for CPU bottlenecks on the slowest node or increase `NCCL_GRAPH_MIX_THRESHOLD`.
