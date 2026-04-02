# Tools — Assembler and Compiler

## Python Assembler (Priority!)

Converts T1C assembly code to binary.
~300 lines Python — beginner friendly!

### What It Should Do
```bash
python t1c_assembler.py attention.t1c -o attention.bin
```

### ISA Reference
See `/isa/t1c_isa_spec.md` — all 9 instructions defined.

### Example Input
```asm
DMALOAD  M0, 0x10000000, 16384
MATMUL   A0, M0, M1, 128
SOFTMAX  V0, A0, 128
DMASTORE 0x20000000, V0, 512
```

### Example Output
```
01 00 00 10 00 00 40 00  ; DMALOAD
01 A0 00 01 80 00 00 00  ; MATMUL
07 V0 A0 00 80 00 00 00  ; SOFTMAX
06 00 20 M2 00 02 00 00  ; DMASTORE
```

## MLIR Compiler (Future)

Higher-level compiler from MLIR IR to T1C binary.
