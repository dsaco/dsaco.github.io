---
title: Flux - prompt
date: 2025-08-22 11:05:04
tags: ['Flux', 'prompt']
---

[flux 官方 prompting 总结](https://docs.bfl.ai/guides/prompting_summary)

# 提示词快速参考

> 高效 FLUX 文生图提示词的核心技巧

## 核心框架

采用此结构确保效果稳定：**主体 + 动作 + 风格 + 背景环境**

**示例**："红狐坐在高草丛中，野生动物纪录片摄影风格，薄雾清晨"

- **主体**：核心焦点（人物、物体、角色）
- **动作**：主体的行为或姿态
- **风格**：艺术手法、媒介或美学风格
- **背景环境**：场景、光线、时间、情绪或氛围条件

## 使用结构化描述

对关系和描述使用自然语言，但对技术和氛围元素采用直接说明。

1. "身穿未来装备的人类探险者行走于赛博朋克森林，戏剧性氛围光照，科幻奇幻艺术风格，电影式构图"

2. "穿银色宇航服的宇航员在国际空间站外漂浮，电影级摄影配合戏剧性光线，宁静而令人敬畏"

3. "复古游戏风格侦探穿着老派西装，上半身特写，色彩鲜艳，未来感设计带有 vibrant glow"

## 词语顺序至关重要

将最重要的元素前置。FLUX 会优先关注提示词开头的部分。

**优先级顺序**：主要主体 → 关键动作 → 核心风格 → 必要背景 → 次要细节

## 增强层级

在基础框架上添加这些可选层级：

**基础层**：主体 + 动作 + 风格 + 背景环境

**+ 视觉层**：具体光线、色彩搭配、构图细节

**+ 技术层**：相机设置、镜头规格、质量标识

**+ 氛围层**：情绪基调、情感色彩、叙事元素

**构建示例**：

- 基础："宇航员在空间站外漂浮，电影级摄影"
- 增强："穿银色宇航服的宇航员优雅漂浮在国际空间站外，电影级摄影配合戏剧性光线，沐浴金色阳光，深蓝地球色调，浅景深效果，85mm 镜头，传递惊奇与成就"

## 最佳提示词长度

**短（10-30 词）**：快速概念与风格探索

**中（30-80 词）**：适合多数项目的理想长度

**长（80+词）**：需要详细说明的复杂场景

## 避免否定表述

用"宁静独处"替代"无人拥挤"
用"清澈无遮挡的眼睛"替代"不戴眼镜"

自问："如果这个东西不存在，我实际会看到什么？"

## 快速模板

**人像**：`[主体描述], [姿态/表情], [风格], [光线], [背景]`

**产品**：`[产品细节], [摆放位置], [布光设置], [风格], [情绪]`

**景观**：`[地点/场景], [时间/天气], [拍摄角度], [风格], [氛围]`

**建筑**：`[建筑/空间], [透视], [光线], [风格], [情绪]`

## 文字融合

清晰描述时 FLUX 可在图像中生成可读文字：

- **使用引号**："红色霓虹字母'OPEN'出现在门上方"

- **指定位置**：文字与其他元素的相对位置

- **描述风格**："优雅衬线字体"或"粗犷工业字体"

## 常用场景模式

不同类型图像适用不同提示结构。根据重点调整元素顺序：

### 角色导向

人像/角色设计：以详细角色描述开头

> 详细角色 → 动作 → 风格 → 背景环境

**构建流程**：

1. **基础**：_"长白须锐利蓝眸的年老巫师"_
2. **+ 动作**：_"长白须锐利蓝眸的年老巫师正在施法"_
3. **+ 风格**：_"长白须锐利蓝眸的年老巫师施法，奇幻艺术风格"_
4. **+ 背景**：_"长白须锐利蓝眸的年老巫师在魔法森林空地施法，奇幻艺术风格"_

### 环境导向

景观/建筑摄影：以场景设定开头

> 场景 → 氛围条件 → 风格 → 技术参数

**构建流程**：

1. **基础**：_"古希腊神庙遗迹"_
2. **+ 氛围**：_"日落时分的古希腊神庙遗迹，金色时刻光线"_
3. **+ 风格**：_"日落时分的古希腊神庙遗迹，金色时刻光线，电影摄影风格"_
4. **+ 细节**：_"日落时分的古希腊神庙遗迹，金色时刻光线，电影摄影风格，散落大理石柱"_

### 风格导向

艺术创作：以艺术风格或参考开头

> 艺术参考 → 主体 → 背景环境 → 技术实现

**构建流程**：

1. **基础**：_"梵高绘画风格配合漩涡状笔触"_
2. **+ 主体**：_"梵高漩涡笔触风格描绘现代城市街道"_
3. **+ 背景**：_"梵高漩涡笔触风格描绘现代城市街道，鲜亮蓝黄色调"_
4. **+ 技术**：_"梵高漩涡笔触风格描绘现代城市街道，鲜亮蓝黄色调，印象派技法"_

### 技术导向

专业摄影：以主体开头，最后加入相机设置

> 主体 → 背景 → 光线 → 镜头/设置

**构建流程**：

1. **基础**：_"商业高管专业肖像"_
2. **+ 背景**：_"商业高管专业肖像，纯白背景"_
3. **+ 光线**：_"商业高管专业肖像，纯白背景，影棚灯光"_
4. **+ 技术**：_"商业高管专业肖像，纯白背景，影棚灯光，85mm 镜头，f/1.4 光圈，浅景深"_

## 专业摄影控制（高级）

**背景虚化与清晰度（f 值）**：通常称为"光圈"，f 值控制背景模糊程度。小数值（`f/1.4`）虚化背景；大数值（`f/8`）保持全景清晰。

**场景宽度与变焦（毫米数）**：通常称为"焦距"，`mm`数值控制场景范围和 zoom 效果。小数值（`24mm`）呈现广阔场景；大数值（`85mm`）拉近视角。

**光线控制**：可调控图像中的光影风格。例如`"伦勃朗光"`制造戏剧肖像，`"黄金时刻"`营造温暖氛围

**示例**："专业肖像，85mm 镜头，f/2.8 光圈，伦勃朗布光，企业环境"

## 质量检查清单

- 提示词是否包含核心框架要素？
- 最重要元素是否前置？
- 是否用具体描述替代模糊术语？
- 是否描述期望呈现的内容而非避免的内容？
- 所有元素是否协同构建统一视觉？

# Prompting Quick Reference

> Essential techniques for effective FLUX text-to-image prompting

## Core Framework

Use this structure for consistent results: **Subject + Action + Style + Context**

**Example**: "Red fox sitting in tall grass, wildlife documentary photography, misty dawn"

- **Subject**: The main focus (person, object, character)
- **Action**: What the subject is doing or their pose
- **Style**: Artistic approach, medium, or aesthetic
- **Context**: Setting, lighting, time, mood, or atmospheric conditions

## Use Structured Descriptions

Use natural language for relationships and descriptions, but direct specifications for technical and atmospheric elements.

1. "Human explorer in futuristic gear walking through cyberpunk forest, dramatic atmospheric lighting, sci-fi fantasy art style, cinematic composition"

2. "An astronaut with a silver spacesuit floating outside the International Space Station, cinematic photography with dramatic lighting, peaceful and awe-inspiring"

3. "Retro game style detective in old school suit, upper body shot, colorful, futuristic design with vibrant glow"

## Word Order Matters

Front-load your most important elements. FLUX pays more attention to what comes first.

**Priority order**: Main subject → Key action → Critical style → Essential context → Secondary details

## Enhancement Layers

Build beyond the basic framework with these optional layers:

**Foundation**: Subject + Action + Style + Context

**+ Visual Layer**: Specific lighting, color palette, composition details

**+ Technical Layer**: Camera settings, lens specs, quality markers

**+ Atmospheric Layer**: Mood, emotional tone, narrative elements

**Example progression**:

- Foundation: "An astronaut floating outside the space station, cinematic photography"
- Enhanced: "An astronaut with silver spacesuit floating gracefully outside the International Space Station, cinematic photography with dramatic lighting, bathed in golden sunlight, deep blue Earth tones, shallow depth of field, 85mm lens, conveying wonder and achievement"

## Optimal Prompt Length

**Short (10-30 words)**: Quick concepts and style exploration

**Medium (30-80 words)**: Usually ideal for most projects

**Long (80+ words)**: Complex scenes requiring detailed specifications

## Work Without Negatives

Instead of "no crowds," write "peaceful solitude"
Instead of "without glasses," write "clear, unobstructed eyes"

Ask: "If this thing wasn't there, what would I see instead?"

## Quick Templates

**Portrait**: `[Subject description], [pose/expression], [style], [lighting], [background]`

**Product**: `[Product details], [placement], [lighting setup], [style], [mood]`

**Landscape**: `[Location/setting], [time/weather], [camera angle], [style], [atmosphere]`

**Architecture**: `[Building/space], [perspective], [lighting], [style], [mood]`

## Text Integration

FLUX can generate readable text in images when you describe it clearly. Here's how to get good text results:

- **Use quotation marks**: "The text 'OPEN' appears in red neon letters above the door"

- **Specify placement**: Where the text appears in relation to other elements

- **Describe style**: "elegant serif typography" or "bold industrial lettering"

## Common Use Case Patterns

Different types of images work better with different prompt structures. Put the most important elements first based on what you want to emphasize:

### Character-focused

For portraits, character art: Start with detailed character description and then add the rest of the prompt.

> Detailed character → Action → Style → Context

**Building progression**:

1. **Foundation**: _"Elderly wizard with long white beard and piercing blue eyes"_
2. **+ Action**: _"Elderly wizard with long white beard and piercing blue eyes, casting a spell"_
3. **+ Style**: _"Elderly wizard with long white beard and piercing blue eyes, casting a spell, fantasy art style"_
4. **+ Context**: _"Elderly wizard with long white beard and piercing blue eyes, casting a spell, fantasy art style, in a magical forest clearing"_

### Context-focused

For landscapes, architectural shots: Lead with the setting and then add the rest of the prompt.

> Setting → Atmospheric conditions → Style → Technical specs

**Building progression**:

1. **Foundation**: _"Ancient Greek temple ruins"_
2. **+ Atmosphere**: _"Ancient Greek temple ruins at sunset, golden hour lighting"_
3. **+ Style**: _"Ancient Greek temple ruins at sunset, golden hour lighting, cinematic photography style"_
4. **+ Details**: _"Ancient Greek temple ruins at sunset, golden hour lighting, cinematic photography style, with scattered marble columns"_

### Style-focused

For artistic interpretations: Begin with the art style or reference and then add the rest of the prompt.

> Artistic reference → Subject → Context → Technical execution

**Building progression**:

1. **Foundation**: _"Van Gogh painting style with swirling brushstrokes"_
2. **+ Subject**: _"Van Gogh painting style with swirling brushstrokes, depicting a modern city street"_
3. **+ Context**: _"Van Gogh painting style with swirling brushstrokes, depicting a modern city street, vibrant blues and yellows"_
4. **+ Technical**: _"Van Gogh painting style with swirling brushstrokes, depicting a modern city street, vibrant blues and yellows, impressionist technique"_

### Technical-focused

For professional photography: Start with the subject and then add the rest of the prompt, finishing with camera settings.

> Subject → Background → Lighting → Lens/settings

**Building progression**:

1. **Foundation**: _"Professional headshot of a business executive"_
2. **+ Background**: _"Professional headshot of a business executive, clean white background"_
3. **+ Lighting**: _"Professional headshot of a business executive, clean white background, studio lighting"_
4. **+ Technical**: _"Professional headshot of a business executive, clean white background, studio lighting, 85mm lens, f/1.4, shallow depth of field"_

## Professional Photography Control (Advanced)

**Background blur vs. sharpness (f-numbers)**: Usually called "aperture", the f-number controls how blurry vs. sharp your background is. Small numbers (`f/1.4`) blur the background; big numbers (`f/8`) keep everything sharp.

**Scene width & zoom (mm numbers)**: Usually called "focal length", the `mm` number controls how much of the scene you see and how "zoomed in" it looks. Small numbers (`24mm`) show wide scenes; big numbers (`85mm`) zoom in closer.

**Lighting**: Allow you to control the lighting style in the image. For instance, `"Rembrandt lighting"` for dramatic portraits, `"golden hour"` for warm atmosphere

**Example**: "Professional headshot, 85mm lens, f/2.8, Rembrandt lighting, corporate setting"

## Quality Control Checklist

- Does your prompt include the core framework elements?
- Are your most important elements mentioned first?
- Have you replaced vague terms with specific descriptors?
- Are you describing what you want to see, not what you want to avoid?
- Do all elements work together toward a unified vision?
