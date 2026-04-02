# T1C ISA Specification
## Instruction Set Architecture — Official Reference

**Version:** 1.0 | **Author:** Sarthak (Alexzo) | **License:** MIT

---

## Overview

The T1C ISA defines 9 core instructions that the MAAU (Modular AI Accelerator Unit) hardware understands. Any developer worldwide can write a compiler, assembler, or runtime using this specification — without needing a physical chip.

This is the official language of T1C.

---

## Register File

| Register | Name | Size | Purpose |
|----------|------|------|---------|
| R0–R7 | General Purpose | 32-bit | Integer ops, addresses, loop counters |
| A0–A7 | Accumulator | 128-bit | MAC results, partial sums |
| M0–M7 | Memory Pointer | 32-bit | SRAM address pointers for D-IMC |
| C0–C3 | Control | 32-bit | Flags, status, loop control |
| V0–V3 | Vector | 512-bit | Parallel vector operations |

---

## Instruction Encoding (32-bit)

```
[31:26] OPCODE  — 6-bit instruction opcode
[25:22] RD      — 4-bit destination register
[21:18] RS1     — 4-bit source register 1
[17:14] RS2     — 4-bit source register 2
[13:12] PREC    — Precision: 00=INT4, 01=INT8, 10=FP16, 11=BF16
[11:8]  FLAGS   — Status: overflow, zero, carry, parity
[7:0]   IMM     — 8-bit immediate value
```

---

## The 9 Core Instructions

---

### 1. MATMUL — Matrix Multiply (D-IMC)
**Opcode:** `0x01`

```
MATMUL Rd, Rs1, Rs2, size
```

**Operation:** `Rd = Rs1 × Rs2` (matrix multiply, computed in-memory)

| Field | Description |
|-------|-------------|
| Rd | Destination: output activation pointer |
| Rs1 | Weight matrix SRAM pointer |
| Rs2 | Input activation SRAM pointer |
| size | Matrix dimensions (square: size × size) |
| Precision | INT4 / INT8 / FP16 (set by PREC bits) |
| Latency | Variable — size dependent |

**Notes:**
- Core D-IMC instruction — computation happens INSIDE the SRAM array
- Weights stay stationary (weight-stationary dataflow)
- Activations stream through
- Supports: INT4, INT8, FP16, BF16

**Example:**
```asm
; Multiply 128×128 weight matrix with input
MATMUL A0, M0, M1, 128    ; A0 = weights[M0] × input[M1]
```

---

### 2. ACCUM — Accumulate
**Opcode:** `0x02`

```
ACCUM Ad, Rs, imm
```

**Operation:** `Ad = Ad + Rs + imm`

| Field | Description |
|-------|-------------|
| Ad | Accumulator register (destination + source) |
| Rs | Source register to add |
| imm | 8-bit immediate (bias value) |
| Latency | 1 cycle |

**Notes:**
- Used after split MATMUL operations
- Adds bias term in same instruction
- Accumulates partial sums across multiple MATMUL calls

**Example:**
```asm
ACCUM A0, A1, 0       ; A0 = A0 + A1 (no bias)
ACCUM A0, A1, 5       ; A0 = A0 + A1 + 5 (with bias)
```

---

### 3. KVWRITE — Write to KV-Cache
**Opcode:** `0x03`

```
KVWRITE slot, Rs_key, Rs_val
```

**Operation:** `KV_Cache[slot] = {Rs_key, Rs_val}` (TurboQuant compressed)

| Field | Description |
|-------|-------------|
| slot | Token position (0 to 512K) |
| Rs_key | Key vector register |
| Rs_val | Value vector register |
| Precision | FP16 input, stored as 4-bit PolarQuant |
| Latency | 2 cycles |

**Notes:**
- TurboQuant (PolarQuant-only) compression applied automatically in hardware
- 4× compression: 96MB SRAM → 384MB effective capacity
- MIM-aware: routes to correct slice KV partition

**Example:**
```asm
KVWRITE R0, A1, A2    ; Cache token at position R0
```

---

### 4. KVREAD — Read from KV-Cache
**Opcode:** `0x04`

```
KVREAD Rd_key, Rd_val, slot
```

**Operation:** `{Rd_key, Rd_val} = KV_Cache[slot]` (decompressed)

| Field | Description |
|-------|-------------|
| Rd_key | Destination: key vector |
| Rd_val | Destination: value vector |
| slot | Token position to read |
| Latency | 3 cycles (decompression overhead) |

**Notes:**
- TurboQuant decompression automatic — output is FP16
- Used during attention computation
- Batch reads possible for efficiency

**Example:**
```asm
KVREAD A3, A4, R1     ; Read KV pair at position R1
```

---

### 5. DMALOAD — DMA Load (LPDDR5X → SRAM)
**Opcode:** `0x05`

```
DMALOAD dst_sram, src_addr, len
```

**Operation:** `SRAM[dst_sram] = LPDDR5X[src_addr .. src_addr+len]`

| Field | Description |
|-------|-------------|
| dst_sram | SRAM destination address |
| src_addr | LPDDR5X source address |
| len | Transfer length in bytes (max 32MB) |
| Latency | Non-blocking — DMA handles asynchronously |

**Notes:**
- Non-blocking: RISC-V core continues executing while DMA transfers
- 4 independent DMA channels available simultaneously
- Use for loading model weights before compute

**Example:**
```asm
DMALOAD M0, 0x10000000, 16384   ; Load 16KB weights to SRAM
; RISC-V continues here while DMA works
```

---

### 6. DMASTORE — DMA Store (SRAM → LPDDR5X)
**Opcode:** `0x06`

```
DMASTORE dst_addr, src_sram, len
```

**Operation:** `LPDDR5X[dst_addr] = SRAM[src_sram .. src_sram+len]`

| Field | Description |
|-------|-------------|
| dst_addr | LPDDR5X destination address |
| src_sram | SRAM source address |
| len | Transfer length in bytes |
| Latency | Non-blocking |

**Example:**
```asm
DMASTORE 0x20000000, M2, 512    ; Store 512 bytes output
```

---

### 7. SOFTMAX — Hardware Softmax
**Opcode:** `0x07`

```
SOFTMAX Vd, Vs, n
```

**Operation:** `Vd[i] = exp(Vs[i]) / Σ(exp(Vs[0..n]))`

| Field | Description |
|-------|-------------|
| Vd | Destination vector register |
| Vs | Source vector (attention logits) |
| n | Sequence length (up to 512K) |
| Precision | FP16 |
| Latency | n/4 cycles (parallel exp units) |

**Notes:**
- Dedicated softmax hardware unit — not using MAC array
- Numerically stable (subtract max before exp)
- Used for attention score normalization

**Example:**
```asm
SOFTMAX V0, A3, R2    ; Softmax over R2 elements
```

---

### 8. RMSNORM — RMS Normalization
**Opcode:** `0x08`

```
RMSNORM Vd, Vs, weight, eps
```

**Operation:** `Vd = (Vs / RMS(Vs)) × weight`

| Field | Description |
|-------|-------------|
| Vd | Destination vector |
| Vs | Source vector (layer output) |
| weight | Learned scale parameter pointer |
| eps | Epsilon for numerical stability (typically 1e-6) |
| Precision | FP16 |
| Latency | 8 cycles |

**Notes:**
- Used in LLaMA, Mistral, Gemma (all modern LLMs)
- Replaces LayerNorm in most architectures
- RMS computed in hardware in parallel

**Example:**
```asm
RMSNORM V1, V0, M3, 6    ; RMSNorm with epsilon=1e-6
```

---

### 9. MIMSET — Configure MIM Partition
**Opcode:** `0x09`

```
MIMSET maau_id, topology, slice_id
```

**Operation:** Configure MIM (Multi-Instance MAAU) partition

| Field | Description |
|-------|-------------|
| maau_id | Target MAAU (0–7 on blade) |
| topology | 0=FULL, 1=MIM-2, 2=MIM-4 |
| slice_id | Active slice for subsequent operations |
| Latency | ~50ms (MAAU quiesce + MMU reconfigure) |

**Notes:**
- Only blade controller can issue this instruction
- MAAU drains in-flight ops before reconfiguring (no data loss)
- After MIMSET, all subsequent instructions route to selected slice
- Runtime reconfigurable — no system reboot needed

**Example:**
```asm
MIMSET 0, 2, 0    ; Set MAAU 0 to MIM-4, select slice 0
MIMSET 0, 2, 1    ; Switch to slice 1 on same MAAU
MIMSET 0, 0, 0    ; Restore MAAU 0 to full mode
```

---

## Memory Map

```
Address Range        Size    Description
─────────────────────────────────────────
0x0000_0000         32MB    Weight SRAM (D-IMC array)
0x0200_0000         24MB    Activation SRAM
0x0380_0000         24MB    KV-Cache SRAM (TurboQuant)
0x0500_0000         8MB     Instruction Cache
0x0580_0000         8MB     Scratchpad
0x0600_0000         ──      Reserved
0x8000_0000         64GB    LPDDR5X (wide-bus, via DMA)
0xC000_0000         512GB   CXL DDR5 pool (optional)
0xF000_0000         ──      MMIO — control registers
```

---

## Example: Full LLaMA Attention Layer

```asm
; ── LOAD WEIGHTS ──────────────────────────────
DMALOAD  M0, WEIGHT_Q_ADDR, 16384  ; Load Q projection
DMALOAD  M1, WEIGHT_K_ADDR, 16384  ; Load K projection
DMALOAD  M2, WEIGHT_V_ADDR, 16384  ; Load V projection
DMALOAD  M3, NORM_WEIGHTS,  512    ; Load RMSNorm weights

; ── LAYER NORM ────────────────────────────────
RMSNORM  V0, input_acts, M3, 6     ; Normalize input

; ── QKV PROJECTIONS ───────────────────────────
MATMUL   A0, M0, V0, 128           ; Q = W_q × input
MATMUL   A1, M1, V0, 128           ; K = W_k × input
MATMUL   A2, M2, V0, 128           ; V = W_v × input

; ── CACHE CURRENT TOKEN ───────────────────────
KVWRITE  token_pos, A1, A2         ; Cache K, V (compressed)

; ── READ ALL HISTORY ──────────────────────────
KVREAD   A3, A4, 0                 ; Read from position 0

; ── ATTENTION SCORES ──────────────────────────
MATMUL   A5, A0, A3, seq_len       ; Scores = Q × K^T

; ── SOFTMAX ───────────────────────────────────
SOFTMAX  V1, A5, seq_len           ; Normalize scores

; ── WEIGHTED VALUES ───────────────────────────
MATMUL   A6, V1, A4, 128           ; Context = scores × V

; ── OUTPUT PROJECTION ─────────────────────────
DMALOAD  M4, WEIGHT_O_ADDR, 16384  ; Load output weights
MATMUL   A7, M4, A6, 128           ; Output = W_o × context

; ── STORE RESULT ──────────────────────────────
DMASTORE OUTPUT_ADDR, A7, 512      ; Write to LPDDR5X
```

---

## Assembler Syntax

```
; Comment lines start with semicolon
INSTRUCTION  Rd, Rs1, Rs2, imm    ; operands comma-separated
.data        label: value          ; data definitions
.sram        addr: size            ; SRAM allocation
```

---

*T1C ISA Specification v1.0 | Alexzo | Sarthak | MIT License*
