# Oikonomia DCE: Bare-Metal SEC Rule 15c3-5 Risk Gate
Sub-microsecond, hardware-enforced pre-trade compliance and TOCTOU preemption deployed on AWS EC2 f2.6xlarge (Xilinx UltraScale+).

## Validated Hardware Telemetry (PCIe BAR0 MMIO)
* **Kernel Bypass P50:** 170 ns wire-to-wire latency
* **Consequence Governance:** 84.0 ns physical drop shunt (21 cycles @ 250 MHz)
* **Monotonic Anti-Replay:** Hardware epoch enforcement against stale execution tokens (401 Reject)
* **TOCTOU Mutation Preemption:** 403 Forbidden shunt on divergent payload SHA-256 digest
* **Operating Profile:** 0% OS scheduling jitter, 9 Watts steady-state Vccint

## Ingestion Example (C++ Shared Object)
```cpp
#include "oikonomia_dce.h"

// Direct user-space PCIe BAR0 MMIO registration
bool is_valid = OikonomiaDCE::validate_order_commit(fix_payload_hash, active_epoch);
if (!is_valid) {
    // Hardware has already asserted physical TX_DISABLE in 84.0ns
    abort_trade();
}
```

## Zero-Touch Enclave Deployment
Use `deploy_enclave.tf` to provision an air-gapped, zero-egress evaluation sandbox in your AWS tenant.
