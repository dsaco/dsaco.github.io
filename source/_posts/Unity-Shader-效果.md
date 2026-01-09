---
title: Unity - Shader - 效果
date: 2026-01-07 10:28:09
tags: ['Unity', 'Shader']
---

### 计算灰度

```cpp
fixed4 frag (v2f i) : SV_Target
{
    fixed4 col = tex2D(_MainTex, i.uv);

    // 应用灰度公式：0.299r + 0.587g + 0.114b
    // 更容易理解的
    // fixed gray = 0.299 * col.r + 0.587 * col.g + 0.114 * col.b;
    // 更高效的dot版本
    fixed gray = dot(col.rgb, fixed3(0.299, 0.587, 0.114));
    // 将RGB都设为灰度值
    col.rgb = gray;

    UNITY_APPLY_FOG(i.fogCoord, col);
    return col;
}

```

### 抖音图标效果

```cpp
{
    Properties
    {
        _Offset ("偏移量", Range(0, 0.1)) = 0.005
    }
    SubShader
    {
        Pass
        {
            float _Offset;

            fixed4 frag (v2f i) : SV_Target
            {
                // 用sin函数让偏移量随时间变化 范围从0到2 * _Offset
                float offset = _Offset * (sin(_Time.y) + 1);
                // 红色通道 向右下偏移
                float r = tex2D(_MainTex, i.uv + float2(-offset, offset)).r;
                // 绿色通道 无偏移
                float g = tex2D(_MainTex, i.uv).g;
                // 蓝色通道 向左上偏移
                float b = tex2D(_MainTex, i.uv + float2(offset, -offset)).b;

                return fixed4(r, g, b, 1);
            }
            ENDCG
        }
    }
}
```

### 平移效果

```cpp
fixed4 frag (v2f i) : SV_Target
{
    float2 uv = i.uv;
    // 0.2为滚动速度
    uv.x += _Time.y * 0.2;
    fixed4 col = tex2D(_MainTex, uv);
    UNITY_APPLY_FOG(i.fogCoord, col);
    return col;
}
```

### 旋转效果

```cpp
fixed4 frag (v2f i) : SV_Target
{
    // 定义旋转中心为(0.5, 0.5), 0时旋转中心为(1.0, 1.0), 1时旋转中心为(0.0, 0.0)
    float rCenter = 0.5;
    // 将UV中心移到(0.5, 0.5)
    float2 centered = i.uv - rCenter;

    // 计算旋转角度（随时间变化）
    float angle = _Time.y; // 每秒旋转1弧度

    // 旋转矩阵
    float s = sin(angle);
    float c = cos(angle);
    float2 rotated;
    rotated.x = centered.x * c - centered.y * s;
    rotated.y = centered.x * s + centered.y * c;

    // 移回原来的位置
    float2 uv = rotated + rCenter;

    fixed4 col = tex2D(_MainTex, uv);
    return col;
}
```

### 径向差异旋转

```cpp
fixed4 frag (v2f i) : SV_Target
{
    // 定义旋转中心为(0.5, 0.5), 0时旋转中心为(1.0, 1.0), 1时旋转中心为(0.0, 0.0)
    float rCenter = 0.5;
    // 将UV中心移到(0.5, 0.5)
    float2 centered = i.uv - rCenter;

    // // 计算旋转角度（随时间变化）
    // float angle = _Time.y; // 每秒旋转1弧度

    // ========== 新增：计算距离中心的距离 ==========
    float dist = length(centered); // 距离：0（中心）到 0.707（角落）

    // ========== 修改：让角度随距离变化 ==========
    float angle = _Time.w * (1.0 - dist);
    // dist = 0（中心）→ angle = _Time.y * 1.0 （转得快）
    // dist = 0.5（边缘）→ angle = _Time.y * 0.5 （转得慢）

    // 旋转矩阵
    float s = sin(angle);
    float c = cos(angle);
    float2 rotated;
    rotated.x = centered.x * c - centered.y * s;
    rotated.y = centered.x * s + centered.y * c;

    // 移回原来的位置
    float2 uv = rotated + rCenter;

    fixed4 col = tex2D(_MainTex, uv);
    return col;
}
```

| 代码                                     | 效果                       |
| ---------------------------------------- | -------------------------- |
| angle = \_Time.y                         | 全部同速旋转               |
| angle = \_Time.y \* (1.0 - dist)         | 中心快，边缘慢（漩涡）     |
| angle = \_Time.y \* dist                 | 边缘快，中心慢（反向漩涡） |
| angle = \_Time.y \* (1.0 - dist \* dist) | 平滑漩涡                   |
| angle = \_Time.y \* (1.0 - pow(dist, 3)) | 中心区域更大的漩涡         |
