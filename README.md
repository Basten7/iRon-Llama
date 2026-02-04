Iron-llama

This repository hosts the latest iteration of a customized LLaMA inference setup optimized for macOS 26.2 on a machine equipped with an Intel Xeon W3235 CPU and two Radeon 6800X Duo GPUs. The implementation is designed to be MetalV3 compatible and avoids Apple Silicon optimizations (M1, M2, M3), ensuring compatibility with Intel-based Mac hardware.

🎯 Objective

The goal is to run large language models (LLMs) efficiently using GGUF quantization on Metal-compatible GPUs, focusing on:

Single GPU optimization
Quantized models: Q4_K_M, Q4_K, Q6_K
Support for F32 tensors: 241 tensors
Support for Q4_K tensors: 289 tensors
Support for Q6_K tensors: 49 tensors
⚙️ Configuration

Target Platform: macOS 26.2 (Intel-based)
CPU: Intel Xeon W3235
GPUs: Two Radeon 6800X Duo (MetalV3 compatible)
Model Format: GGUF quantized models (Q4_K_M, Q4_K, Q6_K)

📦 Installation

  git clone https://github.com/ggerganov/llama.cpp.git

  cd llama.cpp

  git fetch --tags

  git checkout b6123

➡️ Then replace the 2 modified files from this repo into:


llama.cpp/ggml/src/ggml-metal/      … and you’re done ✅

➡️ Prerequisites (Metal-only build)

brew install cmake git libomp glslang

➡️ Prerequisites (Metal + Vulkan build)

brew install cmake git libomp glslang molten-vk shaderc vulkan-loader vulkan-headers

➡️ Build

cmake -S . -B build -DCMAKE_BUILD_TYPE=Release -DGGML_METAL_MGPU=ON -DOpenMP_ROOT="$(brew --prefix)/opt/libomp" && cmake --build build -j


⚙️ Execution Command:

export GGML_METAL_N_CB=4
export GGML_METAL_DEVICE_INDEX=0 (Choose the gpu index)

export GGML_METAL_N_CB=4; ./build/bin/llama-bench -m /Volumes/NM790-4To/Qwen3-2507/Qwen3-30B-A3B/Qwen3-Coder-30B-A3B-Instruct-1M-Q4_K_M.gguf -fa 0 -ub 32 -b 32                           
| model                          |       size |     params | backend    | threads | n_batch | n_ubatch |            test |                  t/s |
| ------------------------------ | ---------: | ---------: | ---------- | ------: | ------: | -------: | --------------: | -------------------: |
| qwen3moe 30B.A3B Q4_K - Medium |  17.28 GiB |    30.53 B | Metal,BLAS |      12 |      32 |       32 |           pp512 |        271.87 ± 0.17 |
| qwen3moe 30B.A3B Q4_K - Medium |  17.28 GiB |    30.53 B | Metal,BLAS |      12 |      32 |       32 |           tg128 |         72.57 ± 0.29 |

./build/bin/llama-server --port 8010  -ngl 99 --temp 0.7 -c 16192 -m ~/models/Qwen3-Coder-30B-A3B-Instruct-1M-Q4_K_M.gguf --jinja --prio 2 -ub 32 -b 32
  
🧠 Optimizations

Currently, the code is optimized for a single GPU using Q4_K_M quantized tensors and MOE models, which balances performance and accuracy for inference tasks. Future steps will expand support to both GPUs and include further optimizations for different tensor types.

📦 Dependencies

To get started, ensure you have the following installed:

llama.cpp (with Metal support)
MetalV3 drivers for AMD GPUs
Compatible GGUF model files for F16, F32, Q4_K_M, Q4_K, and Q6_K quantization
🛠️ Strategy

Validate current setup using ./build/bin/llama-server with the specified model.
Iterate on single GPU optimization with Q4_K_M tensors.
Expand to dual GPU support as needed.
Tune for performance and memory usage on Intel-based Macs.
📝 Notes

No file or function names are invented; all code and structure are consistent with existing llama.cpp implementations.
The model path and execution parameters are based on the provided example and should be adapted to your system.


Performance
llama.cpp MetalV3 MacIntel (iRon-Llama)

export GGML_METAL_N_CB=4; ./build/bin/llama-bench -m ~/models/Qwen3-30B-A3B/Qwen3-Coder-30B-A3B-Instruct-1M-Q4_K_M.gguf -fa 0 -ub 16,32,64 -b 16,32,64
| model                          |       size |     params | backend    | threads | n_batch | n_ubatch |            test |                  t/s |
| ------------------------------ | ---------: | ---------: | ---------- | ------: | ------: | -------: | --------------: | -------------------: |
| qwen3moe 30B.A3B Q4_K - Medium |  17.28 GiB |    30.53 B | Metal,BLAS |      12 |      16 |       16 |           pp512 |        250.96 ± 1.91 |
| qwen3moe 30B.A3B Q4_K - Medium |  17.28 GiB |    30.53 B | Metal,BLAS |      12 |      16 |       16 |           tg128 |         80.56 ± 0.27 |
| qwen3moe 30B.A3B Q4_K - Medium |  17.28 GiB |    30.53 B | Metal,BLAS |      12 |      16 |       32 |           pp512 |        245.31 ± 0.21 |
| qwen3moe 30B.A3B Q4_K - Medium |  17.28 GiB |    30.53 B | Metal,BLAS |      12 |      16 |       32 |           tg128 |         72.25 ± 0.50 |
| qwen3moe 30B.A3B Q4_K - Medium |  17.28 GiB |    30.53 B | Metal,BLAS |      12 |      16 |       64 |           pp512 |        235.57 ± 0.15 |
| qwen3moe 30B.A3B Q4_K - Medium |  17.28 GiB |    30.53 B | Metal,BLAS |      12 |      16 |       64 |           tg128 |         62.70 ± 0.61 |
| qwen3moe 30B.A3B Q4_K - Medium |  17.28 GiB |    30.53 B | Metal,BLAS |      12 |      32 |       16 |           pp512 |        251.67 ± 0.29 |
| qwen3moe 30B.A3B Q4_K - Medium |  17.28 GiB |    30.53 B | Metal,BLAS |      12 |      32 |       16 |           tg128 |         80.33 ± 0.17 |
| qwen3moe 30B.A3B Q4_K - Medium |  17.28 GiB |    30.53 B | Metal,BLAS |      12 |      32 |       32 |           pp512 |        269.62 ± 0.11 |
| qwen3moe 30B.A3B Q4_K - Medium |  17.28 GiB |    30.53 B | Metal,BLAS |      12 |      32 |       32 |           tg128 |         71.63 ± 0.27 |
| qwen3moe 30B.A3B Q4_K - Medium |  17.28 GiB |    30.53 B | Metal,BLAS |      12 |      32 |       64 |           pp512 |        262.39 ± 0.20 |
| qwen3moe 30B.A3B Q4_K - Medium |  17.28 GiB |    30.53 B | Metal,BLAS |      12 |      32 |       64 |           tg128 |         60.68 ± 0.49 |
| qwen3moe 30B.A3B Q4_K - Medium |  17.28 GiB |    30.53 B | Metal,BLAS |      12 |      64 |       16 |           pp512 |        254.45 ± 0.22 |
| qwen3moe 30B.A3B Q4_K - Medium |  17.28 GiB |    30.53 B | Metal,BLAS |      12 |      64 |       16 |           tg128 |         78.94 ± 0.47 |
| qwen3moe 30B.A3B Q4_K - Medium |  17.28 GiB |    30.53 B | Metal,BLAS |      12 |      64 |       32 |           pp512 |        271.10 ± 0.21 |
| qwen3moe 30B.A3B Q4_K - Medium |  17.28 GiB |    30.53 B | Metal,BLAS |      12 |      64 |       32 |           tg128 |         71.00 ± 0.31 |
| qwen3moe 30B.A3B Q4_K - Medium |  17.28 GiB |    30.53 B | Metal,BLAS |      12 |      64 |       64 |           pp512 |        275.74 ± 1.71 |
| qwen3moe 30B.A3B Q4_K - Medium |  17.28 GiB |    30.53 B | Metal,BLAS |      12 |      64 |       64 |           tg128 |         61.09 ± 0.17 |

build: 79c1160b0 (6123)


llama.cpp on Vulkan
./build/bin/llama-bench -m /Volumes/NM790-4To/Qwen3-2507/Qwen3-30B-A3B/Qwen3-Coder-30B-A3B-Instruct-1M-Q4_K_M.gguf -fa 0 -ub 16,32,64 -b 16,32,64 -mg 3 -sm none
ggml_vulkan: Found 5 Vulkan devices:
ggml_vulkan: 0 = AMD Radeon RX 6800 XT (MoltenVK) | uma: 0 | fp16: 1 | bf16: 0 | warp size: 64 | shared memory: 65536 | int dot: 0 | matrix cores: none
ggml_vulkan: 1 = AMD Radeon PRO W6800X Duo (MoltenVK) | uma: 0 | fp16: 1 | bf16: 0 | warp size: 64 | shared memory: 65536 | int dot: 0 | matrix cores: none
ggml_vulkan: 2 = AMD Radeon PRO W6800X Duo (MoltenVK) | uma: 0 | fp16: 1 | bf16: 0 | warp size: 64 | shared memory: 65536 | int dot: 0 | matrix cores: none
ggml_vulkan: 3 = AMD Radeon PRO W6800X Duo (MoltenVK) | uma: 0 | fp16: 1 | bf16: 0 | warp size: 64 | shared memory: 65536 | int dot: 0 | matrix cores: none
ggml_vulkan: 4 = AMD Radeon PRO W6800X Duo (MoltenVK) | uma: 0 | fp16: 1 | bf16: 0 | warp size: 64 | shared memory: 65536 | int dot: 0 | matrix cores: none
| model                          |       size |     params | backend    | threads | n_batch | n_ubatch |   main_gpu |    sm |            test |                  t/s |
| ------------------------------ | ---------: | ---------: | ---------- | ------: | ------: | -------: | ---------: | ----: | --------------: | -------------------: |
| qwen3moe 30B.A3B Q4_K - Medium |  17.28 GiB |    30.53 B | Vulkan,BLAS |      12 |      16 |       16 |          3 |  none |           pp512 |         82.14 ± 1.22 |
| qwen3moe 30B.A3B Q4_K - Medium |  17.28 GiB |    30.53 B | Vulkan,BLAS |      12 |      16 |       16 |          3 |  none |           tg128 |         87.85 ± 0.22 |
| qwen3moe 30B.A3B Q4_K - Medium |  17.28 GiB |    30.53 B | Vulkan,BLAS |      12 |      16 |       32 |          3 |  none |           pp512 |         83.21 ± 0.70 |
| qwen3moe 30B.A3B Q4_K - Medium |  17.28 GiB |    30.53 B | Vulkan,BLAS |      12 |      16 |       32 |          3 |  none |           tg128 |         85.24 ± 2.63 |
| qwen3moe 30B.A3B Q4_K - Medium |  17.28 GiB |    30.53 B | Vulkan,BLAS |      12 |      16 |       64 |          3 |  none |           pp512 |         83.45 ± 0.80 |
| qwen3moe 30B.A3B Q4_K - Medium |  17.28 GiB |    30.53 B | Vulkan,BLAS |      12 |      16 |       64 |          3 |  none |           tg128 |         87.50 ± 0.35 |
| qwen3moe 30B.A3B Q4_K - Medium |  17.28 GiB |    30.53 B | Vulkan,BLAS |      12 |      32 |       16 |          3 |  none |           pp512 |         82.34 ± 0.94 |
| qwen3moe 30B.A3B Q4_K - Medium |  17.28 GiB |    30.53 B | Vulkan,BLAS |      12 |      32 |       16 |          3 |  none |           tg128 |         87.09 ± 0.36 |
| qwen3moe 30B.A3B Q4_K - Medium |  17.28 GiB |    30.53 B | Vulkan,BLAS |      12 |      32 |       32 |          3 |  none |           pp512 |        164.17 ± 2.78 |
| qwen3moe 30B.A3B Q4_K - Medium |  17.28 GiB |    30.53 B | Vulkan,BLAS |      12 |      32 |       32 |          3 |  none |           tg128 |         86.02 ± 2.65 |
| qwen3moe 30B.A3B Q4_K - Medium |  17.28 GiB |    30.53 B | Vulkan,BLAS |      12 |      32 |       64 |          3 |  none |           pp512 |        164.53 ± 0.76 |
| qwen3moe 30B.A3B Q4_K - Medium |  17.28 GiB |    30.53 B | Vulkan,BLAS |      12 |      32 |       64 |          3 |  none |           tg128 |         87.55 ± 0.37 |
| qwen3moe 30B.A3B Q4_K - Medium |  17.28 GiB |    30.53 B | Vulkan,BLAS |      12 |      64 |       16 |          3 |  none |           pp512 |         84.02 ± 0.22 |
| qwen3moe 30B.A3B Q4_K - Medium |  17.28 GiB |    30.53 B | Vulkan,BLAS |      12 |      64 |       16 |          3 |  none |           tg128 |         88.08 ± 0.05 |
| qwen3moe 30B.A3B Q4_K - Medium |  17.28 GiB |    30.53 B | Vulkan,BLAS |      12 |      64 |       32 |          3 |  none |           pp512 |        163.21 ± 0.74 |
| qwen3moe 30B.A3B Q4_K - Medium |  17.28 GiB |    30.53 B | Vulkan,BLAS |      12 |      64 |       32 |          3 |  none |           tg128 |         87.25 ± 0.44 |
| qwen3moe 30B.A3B Q4_K - Medium |  17.28 GiB |    30.53 B | Vulkan,BLAS |      12 |      64 |       64 |          3 |  none |           pp512 |         25.65 ± 0.35 |
| qwen3moe 30B.A3B Q4_K - Medium |  17.28 GiB |    30.53 B | Vulkan,BLAS |      12 |      64 |       64 |          3 |  none |           tg128 |         85.43 ± 0.54 |

build: bcb43163a (7833)



