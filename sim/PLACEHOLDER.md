# Simulation — Verilator MAAU Model

Software simulation of T1C MAAU. Run and test without real hardware.

## What's Needed

- [ ] `maau_verilator/` — Full MAAU Verilator model
- [ ] `test_matmul.cpp` — Matrix multiply test
- [ ] `test_kvcache.cpp` — KV-Cache test  
- [ ] `test_mim.cpp` — MIM partition test
- [ ] `run_llama.cpp` — Run LLaMA 1B on simulator

## How to Use (when ready)

```bash
cd sim/maau_verilator
make
./t1c_sim --kernel ../../isa/examples/matmul.bin
```
