# RTL — Verilog Source Files

This folder will contain the T1C Verilog RTL (Register Transfer Level) design.

## Files Needed (Contribute!)

- [ ] `mac_unit.v` — Single INT4/INT8/FP16 multiply-accumulate unit
- [ ] `mac_array.v` — Array of MAC units (D-IMC compute core)
- [ ] `kv_cache.v` — KV-Cache controller with TurboQuant
- [ ] `dma_engine.v` — 4-channel scatter-gather DMA
- [ ] `mim_mmu.v` — MIM hardware MMU and page tables
- [ ] `flash_attention.v` — Flash-Attention V2 + GQA unit
- [ ] `softmax_unit.v` — Hardware softmax
- [ ] `rmsnorm_unit.v` — RMS normalization unit
- [ ] `ldo_ctrl.v` — On-chip LDO voltage regulator control
- [ ] `voltage_monitor.v` — Hardware voltage monitor (±3mV protection)
- [ ] `thermal_sensor.v` — 8 thermal sensors + auto-throttle
- [ ] `top_maau.v` — Top-level MAAU integration
- [ ] `jtag_tap.v` — JTAG debug interface

## Process Node

Primary: **65nm LP (GlobalFoundries GF65LP PDK)**
Secondary: **130nm (IHP SG13G2 — free for research)**

## Tools Required

```bash
# Install OpenLane 2.0
pip install openlane

# Install Verilator (simulation)
sudo apt install verilator

# Run simulation
verilator --cc mac_unit.v --exe sim_mac.cpp
```

## How to Contribute

See [CONTRIBUTING.md](../CONTRIBUTING.md) for full guide.
Start with `mac_unit.v` — it's the simplest and most important module.
