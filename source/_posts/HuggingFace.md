---
title: HuggingFace
date: 2025-07-14 13:42:38
tags:
---

[官网 quick-start](https://huggingface.co/docs/huggingface_hub/quick-start)
[huggingface-cli 文档](https://huggingface.co/docs/huggingface_hub/en/guides/cli)

```bash
# 安装
pip install huggingface_hub

# 登录
huggingface-cli login

# 配置镜像
export HF_ENDPOINT=https://hf-mirror.com
export HF_ENDPOINT=https://alpha.hf-mirror.com


## 设置令牌
# Settings/Access Tokens/New Token
# 有些仓库下载需要在 https://huggingface.co/settings/tokens 中授权
export HF_TOKEN=hf_xxxxxxxxxxxxxxxxxxxxxxxxx
export HUGGINGFACE_TOKEN=hf_xxxxxxxxxxxxxxxxxxxxxxxxx

# 多线程传输
pip install huggingface_hub[hf_transfer]
export HF_HUB_ENABLE_HF_TRANSFER=1

#设置缓存盘
huggingface-cli download xxx/yyy --cache-dir ./path/to/cache

```

## 下载模型

huggingface-cli download google/siglip-so400m-patch14-384 --local-dir ./LLM/siglip-so400m-patch14-384
huggingface-cli download unsloth/Meta-Llama-3.1-8B-bnb-4bit --local-dir ./LLM/Meta-Llama-3.1-8B-bnb-4bit

## 下载文件

### 下载 Flux Kontext
huggingface-cli download black-forest-labs/FLUX.1-Kontext-dev flux1-kontext-dev.safetensors --local-dir ./diffusion_models

huggingface-cli download google/siglip-so400m-patch14-384 config.json --local-dir ./SOME_PATH

## 下载 spaces

huggingface-cli download fancyfeast/joy-caption-alpha-two --repo-type=space --local-dir ./Joy_caption_two

huggingface-cli download XLabs-AI/flux-controlnet-depth-v3 flux-depth-controlnet-v3.safetensors --local-dir ./xlabs/controlnet

huggingface-cli download XLabs-AI/flux-controlnet-canny-v3 flux-canny-controlnet-v3.safetensors --local-dir ./xlabs/controlnet

huggingface-cli download Shakker-Labs/FLUX.1-dev-ControlNet-Depth diffusion_pytorch_model.safetensors --local-dir ./controlnet

huggingface-cli download InstantX/FLUX.1-dev-Controlnet-Canny diffusion_pytorch_model.safetensors --local-dir ./controlnet

huggingface-cli download InstantX/FLUX.1-dev-Controlnet-Union diffusion_pytorch_model.safetensors --local-dir ./controlnet
