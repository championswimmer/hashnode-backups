---
title: "Running llama.cpp (compiled from source) on AMD Strix Halo 395"
datePublished: Sat Jan 31 2026 02:14:01 GMT+0000 (Coordinated Universal Time)
cuid: cml1ohosx000d02jug94ue3g3
slug: running-llamacpp-compiled-from-source-on-amd-strix-halo-395
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1769825572256/0080b981-1b21-4dda-891f-60b5acb65135.jpeg
tags: vulkan, graphics, drivers, inference, llama

---


Just a quick doc/note/tutorial for referencing myself later. 

Here's how to get llama.cpp running with Vulkan support on AMD AI Max 395 (Strix Halo) based devices. I tried it on a Beelink GTR 9 Pro, but should work for Framework Ddesktop too. 

### Installing a few Prerequisites 

```shell
sudo apt install amd-smi rocminfo glslc  libvulkan-dev vulkan-tools   mesa-vulkan-drivers   clinfo
``` 

### Download and build llama.cpp

Get it from github 

```shell
git clone https://github.com/ggerganov/llama.cpp
cd llama.cpp
```

Build it for Vulkan support 

```shell
cmake -B build \
  -DGGML_VULKAN=ON \
  -DCMAKE_BUILD_TYPE=Release

cmake --build build -j$(nproc)
``` 

### Run a local LLM model 

Once it is built, we can run a model 

```shell 
./build/bin/llama-cli \
  -m ~/.lmstudio/models/lmstudio-community/GLM-4.7-Flash-GGUF/GLM-4.7-Flash-Q4_K_M.gguf \
  -ngl 999 \
  -c 4096 \
  -t $(nproc) \
  --color on \
  -p "Explain how Vulkan helps LLM inference on AMD GPUs."
```



