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
Execution Command:

export GGML_METAL_N_CB=4
export GGML_METAL_DEVICE_INDEX=0 (the gpu index)

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
