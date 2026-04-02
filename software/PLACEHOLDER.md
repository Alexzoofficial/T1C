# Software — Compiler, Runtime, Drivers

This folder contains T1C software stack.

## What's Needed (Contribute!)

### llama.cpp Backend (Priority #1)
- Folder: `llama_cpp_backend/`
- What: Makes llama.cpp support T1C hardware
- Language: C++
- Reference: Look at `ggml-metal.m` and `ggml-cuda.cu` in llama.cpp
- When done: `./llama-cli --model llama3.gguf --device t1c` works

### Linux Kernel Driver
- Folder: `kernel_driver/`
- What: PCIe driver for T1C blade
- Language: C (kernel module)
- Reference: Standard PCIe driver examples in Linux kernel

### ONNX Runtime Provider
- Folder: `onnx_provider/`
- What: Run any ONNX model on T1C
- Language: C++
- Models: Stable Diffusion, BERT, YOLO, Whisper

### PyTorch Backend
- Folder: `pytorch_backend/`
- What: torch.compile() T1C support
- Base: Fork Intel Gaudi PyTorch extension, adapt for T1C

### HuggingFace Integration
- Folder: `hf_integration/`
- What: HF accelerate T1C device support
- Language: Python

## Getting Started (No Chip Needed)

Use the Verilator simulation model in `/sim/` to develop and test software before real chips arrive.

```bash
# Run T1C simulator
cd ../sim
./t1c_sim --kernel your_kernel.bin --input data.bin

# Test llama.cpp backend on simulator
./llama-cli --model llama3-1b.gguf --device t1c-sim
```

## ISA Reference

See `/isa/t1c_isa_spec.md` for all 9 T1C instructions.
