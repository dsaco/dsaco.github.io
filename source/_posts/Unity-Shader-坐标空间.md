---
title: Unity - Shader - 坐标空间
date: 2026-01-07 09:33:24
tags: ['Unity', 'Shader']
---

## 🎮 四种主要坐标空间

1. 模型空间（Local Space / Object Space）
   比喻：想象你站在一个房间里，以你自己为中心来描述位置。

- 含义：以模型自己的中心点为原点(0,0,0)的坐标系
- 例子：一个角色模型，他的头顶可能是(0, 2, 0)，脚底是(0, -1, 0)
- 特点：无论你把这个模型移动到世界的哪里，他的头相对于身体的位置永远不变
- Unity 中：transform.localPosition 就是模型空间的位置

```cpp
// 例子：获取子物体相对于父物体的位置
Vector3 localPos = childObject.transform.localPosition;
```

2. 世界空间（World Space）
   比喻：就像地球上的经纬度，所有东西都用同一个全局坐标系。

- 含义：整个场景的统一坐标系，原点通常是场景中心
- 例子：玩家在(10, 0, 5)，敌人在(-3, 0, 8)，这些都是世界坐标
- 特点：不管物体怎么嵌套，世界坐标就是它在场景中的"真实位置"
- Unity 中：transform.position 就是世界空间的位置

```cpp
// 例子：两个物体之间的距离（用世界坐标）
float distance = Vector3.Distance(player.position, enemy.position);
```

3. 视图空间（View Space / Camera Space）
   比喻：以摄像机的眼睛为中心看世界，"前后左右"都是相对于摄像机的。

- 含义：以摄像机为原点，摄像机看向的方向是前方(Z 轴)
- 例子：物体在摄像机前方 2 米，右边 1 米，上方 0.5 米
- 特点：摄像机旋转或移动，所有物体的视图空间坐标都会变化
- 用途：判断物体是否在摄像机视野内

```cpp
// 例子：将世界坐标转换为摄像机视图坐标
Vector3 viewPos = Camera.main.WorldToViewportPoint(targetObject.position);
// viewPos 的范围：x 和 y 在 0-1 之间表示在屏幕内，z 表示深度
```

4. UV 坐标（纹理坐标）
   比喻：就像给礼物包装纸，UV 坐标告诉你纸上的哪个图案贴到盒子的哪个面上。

- 含义：2D 坐标(U, V)，用来把 2D 图片(纹理)贴到 3D 模型表面
- 范围：通常是 0 到 1，(0,0)是纹理左下角，(1,1)是右上角
- 例子：模型的某个顶点 UV=(0.5, 0.5)，表示这个点会显示纹理图片的正中心
- 特点：和模型的 3D 坐标无关，纯粹描述"图片的哪部分贴在哪"

```cpp
// 例子：在Shader中使用UV坐标采样纹理
fixed4 color = tex2D(_MainTex, i.uv); // i.uv就是UV坐标
```

5. 裁剪空间（Clip Space）
   为什么需要裁剪空间？
   想象你用手机拍照：

   - 镜头前太近的东西拍不到（太糊了）
   - 太远的东西也拍不到（超出范围）
   - 镜头外的东西也拍不到（不在视野内）

   裁剪空间就是用来判断哪些东西应该被渲染，哪些应该被丢弃。
   裁剪空间的坐标范围
   在 Unity 中（使用 OpenGL/Vulkan 风格）：

   - X 轴：-1 到 +1（左到右）
   - Y 轴：-1 到 +1（下到上）
   - Z 轴：-1 到 +1（近到远，在某些平台是 0 到 1）

6. 🖥️ 屏幕空间（Screen Space）
   屏幕空间是什么？
   - 比喻：就像你电脑显示器上的像素坐标。
   - 左下角：(0, 0)
   - 右上角：(屏幕宽度, 屏幕高度)，比如(1920, 1080)

屏幕空间的特点

分辨率：1920x1080 为例

```
(0, 1080) ←----------→ (1920, 1080)  屏幕顶部
    ↑                        ↑
    |                        |
    |     屏幕中心           |
    |   (960, 540)          |
    |                        |
    ↓                        ↓
  (0, 0) ←----------→ (1920, 0)  屏幕底部
```

Unity 中的屏幕空间 API

```cpp

// 1. 获取屏幕尺寸
int width = Screen.width; // 比如 1920
int height = Screen.height; // 比如 1080

// 2. 鼠标位置（屏幕空间）
Vector3 mousePos = Input.mousePosition;
// 输出可能是：(800, 450, 0) - 表示从左下角数，右 800 像素，上 450 像素

// 3. 世界坐标转屏幕坐标
Vector3 screenPos = Camera.main.WorldToScreenPoint(enemy.position);
// 输出可能是：(1200, 600, 15)
// X=1200 像素, Y=600 像素, Z=15 米（距离摄像机的深度）

// 4. 屏幕坐标转世界坐标
Vector3 worldPos = Camera.main.ScreenToWorldPoint(new Vector3(mousePos.x, mousePos.y, 10));
// 第三个参数 10 表示：在摄像机前方 10 米处的世界坐标
```

### 坐标转换关系图

```
模型空间 → 世界空间 → 视图空间 → 裁剪空间 → 屏幕空间
   ↑           ↑
localPosition  position

UV坐标（独立系统，用于纹理映射）
```
