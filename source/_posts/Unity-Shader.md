---
title: Unity - Shader
date: 2026-01-07 11:43:28
tags: ['Unity', 'Shader']
---

## UV 超过 1 会发生什么

答案是：取决于纹理的 Wrap Mode 设置！
📊 三种 Wrap Mode

1. Repeat（重复）- 默认模式

```
uv.x += _Time.y * 0.2;
// 时间    uv.x值    实际采样位置
// 0秒     0.0       0.0
// 5秒     1.0       0.0（归零重复）
// 7.5秒   1.5       0.5（1.5 - 1 = 0.5）
// 10秒    2.0       0.0（又归零）
```

原理：GPU 会自动执行 frac(uv.x)

```
// 内部实际执行
float actualUV = frac(uv.x);
// frac 取小数部分：
// frac(1.3) = 0.3
// frac(2.7) = 0.7
// frac(5.0) = 0
```

效果：纹理会无限循环平铺，像传送带一样。

2. Clamp（钳制）

```
// 如果纹理设置为Clamp模式
uv.x += _Time.y * 0.2;
// 时间    uv.x值    实际采样位置
// 0秒     0.0       0.0
// 5秒     1.0       1.0（到达边缘）
// 10秒    2.0       1.0（卡在边缘，不再增加）
```

原理：GPU 会执行 clamp(uv.x, 0, 1)

```
// 内部实际执行
float actualUV = clamp(uv.x, 0, 1);
// clamp 限制在范围内：
// clamp(1.5, 0, 1) = 1.0
// clamp(-0.3, 0, 1) = 0.0
```

效果：纹理边缘会被拉伸，像"定格"在最后一个像素。

3. Mirror（镜像）

```
// 如果纹理设置为Mirror模式
uv.x += _Time.y * 0.2;
// 时间    uv.x值    实际采样位置
// 0秒     0.0       0.0
// 5秒     1.0       1.0
// 10秒    2.0       0.0（镜像翻转）
// 15秒    3.0       1.0（再次翻转）
```

效果：纹理会正向 → 反向 → 正向循环播放，像乒乓球一样来回。

## 旋转矩阵数学知识

数学公式

```
[ rotated.x ] = [ c  -s ] [ centered.x ]
[ rotated.y ] = [ s   c ] [ centered.y ]
```

为什么公式是这样的？
想象一个点 (x, y) 在极坐标中：

原始点：
r = √(x² + y²) ← 距离原点的距离
θ = atan2(y, x) ← 角度

旋转 α 度后：
新角度 = θ + α
新坐标 = (r·cos(θ+α), r·sin(θ+α))

用三角恒等式展开：
cos(θ+α) = cos(θ)cos(α) - sin(θ)sin(α)
sin(θ+α) = sin(θ)cos(α) + cos(θ)sin(α)

代入 x=r·cos(θ), y=r·sin(θ)：
新 x = x·cos(α) - y·sin(α)
新 y = x·sin(α) + y·cos(α)

这就是旋转矩阵的公式！

```cpp
// 将UV中心移到(0.5, 0.5)
float2 centered = i.uv - 0.5;
// 每秒旋转1弧度
float angle = _Time.y;
// 旋转矩阵的参数
float s = sin(angle);
float c = cos(angle);

float2 rotated;
rotated.x = centered.x * c - centered.y * s;
rotated.y = centered.x * s + centered.y * c;
// 将旋转后的坐标再移回(0, 0)
float2 uv = rotated + 0.5;
```
