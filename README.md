<div align="center">

# T1C — Tier 1 Chip

**Open-Source AI Accelerator Architecture. Like RISC-V did for CPUs, T1C does for AI chips.**

![License](https://img.shields.io/badge/License-MIT-yellow.svg)
![Brand](https://img.shields.io/badge/Brand-Alexzo-blue.svg)
![Founder](https://img.shields.io/badge/Founder-Sarthak-green.svg)
![Status](https://img.shields.io/badge/Status-Open_Source-informational.svg)
![Website](https://img.shields.io/badge/Website-alexzo.vercel.app-orange.svg)

[🌐 Visit Site](https://alexzo.vercel.app/t1c) · [📄 Documentation](docs/) · [🤝 Contribute](CONTRIBUTING.md)

</div>

> *"We Design It. World Builds It."*

---

## 🧠 Mind Map — T1C Project Overview

```mermaid
mindmap
  root((T1C))
    Architecture
      D-IMC
        Compute inside SRAM
        No Von Neumann bottleneck
      MAAU Chip
        200-400 GFLOPS INT4
        2-4W Power
        65nm / 130nm Process
      Key Features
        MIM - 4 Tenant Slices
        TurboQuant 4x KV-Cache
        5-Layer AVS
    Hardware
      RTL - Verilog
        MAC Array
        KV-Cache Controller
        DMA Engine
        MIM MMU
        Voltage Monitor
      PCB - KiCad
        Blade v1 Design
    Software
      llama.cpp Backend
      Verilator Simulation
      Python Assembler
      Linux Kernel Driver
      ONNX Runtime Provider
    ISA
      9 Instructions
      Assembly Examples
    Fabrication
      IHP Germany - FREE
      GlobalFoundries 65LP
      Efabless + SkyWater
      Tiny Tapeout
    Community
      MIT License
      Open Source
      Open Contributions
```

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

## 🏗️ Architecture Flowchart

```mermaid
flowchart TD
    A[AI Model Input] --> B[Host CPU / GPU]
    B --> C[DMA Engine]
    C --> D[T1C MAAU Chip]

    subgraph D[T1C MAAU Chip - D-IMC Architecture]
        D1[Instruction Fetch] --> D2[ISA Decoder]
        D2 --> D3{Operation Type?}

        D3 -->|MAC / Matrix Multiply| D4[SRAM Array - D-IMC]
        D3 -->|KV-Cache| D5[TurboQuant Engine]
        D3 -->|Memory Access| D6[MIM MMU]

        D4 --> D7[MAC Result]
        D5 --> D8[Compressed KV-Cache<br/>4x Expansion]
        D6 --> D9[Tenant Isolation<br/>4 Slices]

        D7 --> D10[Output Buffer]
        D8 --> D10
        D9 --> D10
    end

    D --> E[Output: AI Inference Result]

    subgraph F[Power & Stability]
        F1[2-4W Power Draw]
        F2[70% Clock Gating]
        F3[5-Layer AVS<br/>±3mV Stability]
    end

    F --> D

    style D fill:#1a1a2e,stroke:#e94560,stroke-width:2px,color:#fff
    style F fill:#16213e,stroke:#0f3460,stroke-width:2px,color:#fff
```

---

## 📊 Performance Comparison

```mermaid
xychart-beta
    title "LLaMA 7B Inference Speed (tokens/second)"
    x-axis ["T1C 1 Blade", "T1C 8 Blades", "RTX 4090", "H100"]
    y-axis "tokens/sec" 0 --> 1100
    bar [16, 128, 90, 1000]
```

```mermaid
xychart-beta
    title "Cost Comparison (USD)"
    x-axis ["T1C 1 Blade", "T1C 8 Blades", "RTX 4090", "H100"]
    y-axis "Cost ($)" 0 --> 35000
    bar [465, 3720, 1500, 30000]
```

```mermaid
xychart-beta
    title "Max Model Size Supported"
    x-axis ["T1C 1 Blade", "T1C 8 Blades", "RTX 4090", "H100"]
    y-axis "Billion Parameters" 0 --> 550
    bar [64, 512, 24, 500]
```

### Performance Table (Honest Numbers)

| System | LLaMA 7B Speed | Max Model | Cost | Open Source |
|--------|---------------|-----------|------|-------------|
| T1C — 1 Blade | 12–20 tok/s | ~64B INT4 | $280–$650 | ✅ MIT License |
| T1C — 8 Blades | 96–160 tok/s | ~512B INT4 | $2,240–$5,200 | ✅ MIT License |
| NVIDIA RTX 4090 | 80–100 tok/s | ~24B FP16 | $1,500 | ❌ Closed |
| NVIDIA H100 | 1000+ tok/s | Unlimited | $30,000 | ❌ Closed |

> **Note:** T1C is not faster than H100. T1C's value is: open source + DIY buildable + hardware tenant isolation + Indian-designed.

---

## 🔧 Data Processing Pipeline

```mermaid
flowchart LR
    A[AI Request] --> B[Host Software<br/>llama.cpp]
    B --> C[T1C Compiler<br/>ONNX / Python]
    C --> D[ISA Instructions<br/>9 Opcodes]
    D --> E[DMA Transfer<br/>to SRAM]
    E --> F[D-IMC Compute<br/>Inside Memory]
    F --> G[TurboQuant<br/>KV-Cache Compress]
    G --> H[MIM Output<br/>Tenant Isolated]
    H --> I[Result Back<br/>to Host]
    I --> J[AI Response]

    style A fill:#0f3460,color:#fff
    style J fill:#e94560,color:#fff
    style F fill:#1a1a2e,stroke:#e94560,stroke-width:2px,color:#fff
```

---

## 📁 Repository Structure

```mermaid
flowchart TD
    A[T1C Repository] --> B[README.md]
    A --> C[LICENSE - MIT]
    A --> D[CONTRIBUTING.md]
    A --> E[docs/]
    A --> F[isa/]
    A --> G[rtl/]
    A --> H[pcb/]
    A --> I[sim/]
    A --> J[tools/]
    A --> K[software/]

    E --> E1[architecture.md]
    E --> E2[blade.md]
    E --> E3[turboquant.md]
    E --> E4[mim.md]
    E --> E5[fabrication.md]

    F --> F1[t1c_isa_spec.md]
    F --> F2[examples/]

    G --> G1[Verilog RTL<br/>CONTRIBUTE HERE]
    H --> H1[KiCad PCB<br/>CONTRIBUTE HERE]
    I --> I1[Verilator Sim<br/>CONTRIBUTE HERE]
    J --> J1[Assembler Tools<br/>CONTRIBUTE HERE]
    K --> K1[llama.cpp Backend<br/>CONTRIBUTE HERE]

    style A fill:#1a1a2e,stroke:#e94560,stroke-width:3px,color:#fff
```

---

## 🏭 Fabrication Options

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

- **Website:** [alexzo.vercel.app/t1c](https://alexzo.vercel.app/t1c)
- **Brand:** Alexzo (alexzo.vercel.app)
- **Founder:** Sarthak
- **Documentation:** See `/docs/` folder

---

*"Real Engineering. Honest Numbers. Open Future. From India — For the World."*
