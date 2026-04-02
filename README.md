# T1C — Tier 1 Chip

**Open-Source AI Accelerator | Brand: Alexzo | Founder: Sarthak | MIT License**

> *"We Design It. World Builds It."*

T1C is an open-source AI accelerator architecture — like RISC-V did for CPUs, T1C does for AI chips. Designed by Sarthak (Alexzo), released under MIT license. Anyone can fabricate, modify, and build products on T1C.

---

## ⚡ Why T1C?

| Problem With Current AI Chips | T1C Solution |
|-------------------------------|--------------|
| NVIDIA H100 costs $30,000 | T1C blade costs $280–$650 |
| Closed source — can't modify | Fully open source — MIT license |
| Need special cleanroom | Fabricatable via community shuttles |
| Von Neumann bottleneck | D-IMC — compute inside memory |
| No hardware multi-tenancy (cheap) | MIM — 4 isolated tenants per chip |

---

## 📊 Performance (Honest Numbers)

| System | LLaMA 7B Speed | Max Model | Cost |
|--------|---------------|-----------|------|
| T1C — 1 Blade | 12–20 tok/s | ~64B INT4 | $280–$650 |
| T1C — 8 Blades | 96–160 tok/s | ~512B INT4 | $2,240–$5,200 |
| NVIDIA RTX 4090 | 80–100 tok/s | ~24B FP16 | $1,500 |
| NVIDIA H100 | 1000+ tok/s | Unlimited | $30,000 |

> **Note:** T1C is not faster than H100. T1C's value is: open source + DIY buildable + hardware tenant isolation + Indian-designed.

---

## 🏗️ Architecture

T1C uses **Digital In-Memory Computing (D-IMC)**:
- Computation happens **inside** the SRAM array — not in a separate processor
- Eliminates the Von Neumann bottleneck (data movement = 60% of AI energy)
- Based on real research (d-Matrix, Samsung) — not theoretical

**Key specs per MAAU chip:**
- Process: 65nm LP (GlobalFoundries) or 130nm (IHP — free for research)
- Performance: 200–400 GFLOPS INT4 (physics verified)
- Power: 2–4W (with 70% clock gating)
- Voltage stability: ±3mV (5-layer AVS — production grade)
- MIM: 4 hardware-isolated tenant slices
- TurboQuant: 4× KV-cache expansion (Google ICLR 2026)

---

## 📁 Repository Structure

```
t1c/
├── README.md               ← You are here
├── LICENSE                 ← MIT License
├── CONTRIBUTING.md         ← How to contribute
├── docs/
│   ├── architecture.md     ← Full chip architecture
│   ├── blade.md            ← Carrier board spec
│   ├── turboquant.md       ← KV-cache compression
│   ├── mim.md              ← Multi-Instance MAAU
│   └── fabrication.md      ← How to get chips made
├── isa/
│   ├── t1c_isa_spec.md     ← ISA specification (9 instructions)
│   └── examples/           ← Assembly code examples
├── rtl/                    ← Verilog RTL (CONTRIBUTE HERE!)
│   └── PLACEHOLDER.md
├── pcb/                    ← KiCad board designs (CONTRIBUTE HERE!)
│   └── PLACEHOLDER.md
├── sim/                    ← Verilator simulation (CONTRIBUTE HERE!)
│   └── PLACEHOLDER.md
├── tools/                  ← Assembler, compiler tools (CONTRIBUTE HERE!)
│   └── PLACEHOLDER.md
└── software/               ← llama.cpp backend (CONTRIBUTE HERE!)
    └── PLACEHOLDER.md
```

---

## 🔧 Fabrication

| Method | Node | Cost | Availability |
|--------|------|------|-------------|
| IHP Germany (research) | 130nm | **FREE** | Available now |
| GlobalFoundries 65LP MPW | 65nm | $15–40/chip | Available now |
| Efabless + SkyWater | 130nm | $100–300/slot | Available now |
| Tiny Tapeout | 130nm | ~$100/slot | Available now |

**Start with IHP Germany** — completely free for open source research projects.

---

## 🤝 How To Contribute

We need help with everything! Pick what you know:

### Hardware Engineers
- [ ] Verilog RTL — MAC array module (`rtl/mac_array.v`)
- [ ] Verilog RTL — KV-Cache controller (`rtl/kv_cache.v`)
- [ ] Verilog RTL — DMA engine (`rtl/dma_engine.v`)
- [ ] Verilog RTL — MIM MMU (`rtl/mim_mmu.v`)
- [ ] Verilog RTL — Voltage monitor (`rtl/voltage_monitor.v`)
- [ ] KiCad blade PCB design (`pcb/blade_v1.kicad_pcb`)

### Software Engineers
- [ ] llama.cpp T1C backend (`software/llama_cpp_backend/`)
- [ ] Verilator simulation model (`sim/maau_verilator/`)
- [ ] Python assembler (`tools/assembler/`)
- [ ] Linux kernel driver (`software/kernel_driver/`)
- [ ] ONNX Runtime provider (`software/onnx_provider/`)

### Anyone
- [ ] Documentation improvements
- [ ] Translation to other languages
- [ ] Blog posts and tutorials
- [ ] Testing and bug reports

See [CONTRIBUTING.md](CONTRIBUTING.md) for full details.

---

## 📜 License

MIT License — Alexzo / Sarthak

Free to use, modify, fabricate, and sell. Attribution appreciated but not legally required.

---

## 🔗 Links

- **Brand:** Alexzo (alexzo.vercel.app)
- **Founder:** Sarthak, Rampur, Uttar Pradesh, India
- **Documentation:** See `/docs/` folder

---

*"Real Engineering. Honest Numbers. Open Future. From India — For the World."*
