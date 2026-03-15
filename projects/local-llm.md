# 🧠 Local LLM Inference Setup

> Running large language models locally on consumer hardware using llama.cpp with CLBlast GPU acceleration on an AMD RX 580.

---

## Overview

A fully local LLM inference setup running on a personal Arch Linux machine, using llama.cpp compiled with CLBlast (OpenCL) support to leverage an AMD RX 580 GPU for accelerated inference. No cloud API required.

**Hardware:** AMD RX 580 8GB VRAM  
**OS:** Arch Linux  
**Backend:** llama.cpp + CLBlast (OpenCL)  
**Storage constraint:** 90GB total disk — model selection matters

---

## Why This Is Non-Trivial

Running LLMs locally on AMD GPUs is significantly harder than on NVIDIA:
- No CUDA — must use OpenCL via CLBlast or ROCm
- ROCm has limited RX 580 (GCN 4.0) support
- CLBlast requires manual compilation with correct build flags
- 8GB VRAM means careful model/quantization selection

---

## Build Process

### Dependencies
```bash
# Arch Linux
sudo pacman -S cmake clblast opencl-headers ocl-icd
```

### Compiling llama.cpp with CLBlast
```bash
git clone https://github.com/ggerganov/llama.cpp
cd llama.cpp

cmake -B build \
  -DGGML_CLBLAST=ON \
  -DCMAKE_BUILD_TYPE=Release

cmake --build build --config Release -j$(nproc)
```

### Verifying GPU is used
```bash
./build/bin/llama-cli -m model.gguf \
  --n-gpu-layers 32 \
  --ctx-size 2048 \
  -p "Hello"
# Watch GPU usage: watch -n1 radeontop
```

---

## Model Strategy

With 8GB VRAM and 90GB total disk, every gigabyte counts:

| Model | Quant | VRAM | Quality |
|---|---|---|---|
| Llama 3.1 8B | Q4_K_M | ~5GB | ✅ Good daily driver |
| Mistral 7B | Q4_K_M | ~4.5GB | ✅ Fast, capable |
| Llama 3.1 8B | Q8_0 | ~8GB | ✅ Near full quality |
| 13B models | Q4_K_M | ~8GB | ⚠️ Tight, some layers on CPU |

**Rule of thumb:** Q4_K_M gives the best quality-per-GB ratio for this hardware.

---

## Performance

Approximate tokens/second on RX 580 8GB with CLBlast:

| Model | Quant | GPU Layers | t/s |
|---|---|---|---|
| 7B | Q4_K_M | 32 | ~18-22 t/s |
| 8B | Q4_K_M | 32 | ~16-20 t/s |
| 8B | Q8_0 | 32 | ~10-12 t/s |

*Not blazing fast — but free, private, and offline.*

---

## Key Skills Demonstrated

- From-source compilation with custom CMake flags
- OpenCL / GPU compute (non-CUDA path)
- Understanding of LLM quantization formats (GGUF, Q4_K_M, Q8_0)
- Resource-constrained system optimisation
- AMD GPU tooling (radeontop, OpenCL debugging)
- ML infrastructure concepts (model serving, context windows, layer offloading)

---

## Lessons Learned

- CLBlast on RX 580 works well but needs `GGML_CLBLAST=ON` explicitly — the default OpenCL build path is different
- Quantization matters more than model size for VRAM budgeting
- Context size (`--ctx-size`) has a large impact on VRAM usage — keep it at 2048-4096 for this card
- `radeontop` is essential for verifying the GPU is actually being used

---

## What's Next

- [ ] Set up llama.cpp as a persistent systemd service with OpenAI-compatible API endpoint
- [ ] Explore k3s + local model serving for homelab AI workloads
- [ ] Test ROCm path if official RX 580 support improves

---

*Part of [spy1345a's portfolio](../README.md)*
