# Contributing to T1C (Tier 1 Chip)

Thank you for wanting to contribute! T1C follows the RISC-V model — we release the architecture, community builds everything else. Every contribution matters.

## Getting Started

1. Fork this repository
2. Pick an issue or task from the list below
3. Create a branch: `git checkout -b feature/your-feature`
4. Make your changes
5. Submit a pull request

## What We Need Most (Priority Order)

### 🔴 Critical — Needed First
1. **Verilator simulation model** (`sim/`)
   - Software model of T1C MAAU
   - Anyone can run it without real chip
   - Start here if you know C++ or SystemVerilog

2. **Python assembler** (`tools/assembler/`)
   - Converts T1C assembly to binary
   - See ISA spec in `isa/t1c_isa_spec.md`
   - ~300 lines Python — beginner friendly!

3. **llama.cpp T1C backend** (`software/llama_cpp_backend/`)
   - Makes llama.cpp support T1C
   - Needs C++ knowledge
   - Look at existing backends (Metal, CUDA) as reference

### 🟠 High Priority
4. **Verilog RTL modules** (`rtl/`)
   - Start with simplest: `mac_unit.v` (single multiply-accumulate)
   - Then: `kv_cache.v`, `dma_engine.v`
   - Use SkyWater 130nm standard cells

5. **Linux kernel driver** (`software/kernel_driver/`)
   - PCIe driver for T1C blade
   - Needs Linux kernel development experience

### 🟡 Medium Priority
6. **KiCad PCB design** (`pcb/`)
   - 8-layer blade carrier board
   - See docs/blade.md for specs

7. **ONNX Runtime provider** (`software/onnx_provider/`)
   - Makes T1C work with ONNX models
   - SD, BERT, YOLO support

## Code Standards

- **C/C++**: Follow llama.cpp style
- **Verilog**: Use SkyWater 130nm PDK standard cells
- **Python**: PEP 8, type hints preferred
- **Documentation**: Clear comments, explain the why not just the what

## ISA Reference

All hardware and software must follow the T1C ISA specification in `isa/t1c_isa_spec.md`. The 9 core instructions are:

1. MATMUL — Matrix multiply (D-IMC)
2. ACCUM — Accumulate partial sums
3. KVWRITE — Write to KV-Cache (TurboQuant compressed)
4. KVREAD — Read from KV-Cache
5. DMALOAD — DMA load from LPDDR5X to SRAM
6. DMASTORE — DMA store from SRAM to LPDDR5X
7. SOFTMAX — Hardware softmax
8. RMSNORM — RMS normalization
9. MIMSET — Configure MIM tenant partition

## Questions?

Open a GitHub Issue. We respond to everything.

## License

All contributions are MIT licensed. By contributing, you agree your code will be MIT licensed.

---
*T1C — Tier 1 Chip | Alexzo | Sarthak*
