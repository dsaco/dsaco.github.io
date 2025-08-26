---
title: Flux Kontext
date: 2025-07-14 13:50:29
tags: ['Flux', 'kontext']
---

## 安装模型

```bash
huggingface-cli download black-forest-labs/FLUX.1-Kontext-dev flux1-kontext-dev.safetensors --local-dir ./diffusion_models
```

## 提示词

### 编辑元素

- change A to B

> change the cake to an apple

> change the color of the cup to red

> change the car color to bright red with metallic finish(将车的颜色改为带金属光泽的亮红色)

### 增删元素

- add A / remove A

> add a strawberry on the top of the cake

> remove the white plate under the cake and coffee cup

###

### 风格迁移

- transform to A style

> transform to 90's retro anime style (90 年代复古动漫风格)

> transform to realistic photograph (转换成真实摄影照片)

> transform to an Retro-style filmic photos (复古胶片摄影)

### 文字编辑

- change the text “A” to “B”

### 改换背景

- change the background to A

> change the background to a city street

> add a setting sun into the background

> it's snowing now, everything is covered whth snow

### 风格一致性

- using this (风格描述) style generate a + A(新内容)

> using this style generate a cute pig

### 人脸一致性

> this woman holding a bunch of white flower

### 物品一致性

- a(物品描述) + b（所处情景）(特征 环境 视角等)

> this rice cooker is placed in a kitchen setting

上传一件衣服

> a young blonde female model wearing this dress

提取衣服 反向提取物品

> extract only the green cloth of the female over a white background,product photography style

[flux 官方 kontext 总结](https://docs.bfl.ai/kontext/kontext_image_editing)

# Image Editing

## Examples of Editing

### Basic Object Modifications

FLUX.1 Kontext is really good at straightforward object modification, for example if we want to change the colour of an object, we can prompt it.

For example: `Change the car color to red`

### Iterative Editing

FLUX.1 Kontext excels at character consistency, even after multiple edits. Starting from a reference picture, we can see that the character is consistent throughout the sequence. The prompts used for each edit are shown in the captions below each image.

- Remove the object from her face
- She is now taking a selfie in the streets of Freiburg, it’s a lovely day out.
- It’s now snowing, everything is covered in snow.

### Text Editing

FLUX.1 Kontext can directly edit text that appears in images, making it easy to update signs, posters, labels, and more without recreating the entire image.

The most effective way to edit text is using quotation marks around the specific text you want to change:

**Prompt Structure**: `Replace '[original text]' with '[new text]'`

**Example -** We can see below where we have an input image with "Choose joy" written, and we replace "joy" with "BFL" - note the upper case format for BFL.
