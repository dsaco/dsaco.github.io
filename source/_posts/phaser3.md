---
title: phaser3
date: 2025-11-14 10:58:56
tags: ['phaser3', 'typescript']
---

## Scenes 场景

ES6 创建一个示例场景：

```ts
class MyScene extends Phaser.Scene {
  constructor() {
    super('MyFirstScene');
  }

  preload() {
    this.load.image('logo', 'assets/sprites/logo.png');
  }

  create() {
    this.add.image(400, 300, 'logo');
  }
}
```

创建场景时，它会提取你在其配置中定义的任何设置。如果没有，它只使用默认值。设置在传递给场景构造函数的配置对象中定义。以下是从配置中设置场景名称和物理引擎的示例：

```ts
class Level1Scene extends Phaser.Scene {
  constructor() {
    super({
      key: 'Level1',
      physics: {
        arcade: {
          debug: true,
          gravity: { y: 200 },
        },
      },
    });
  }
}
```

Settings 对象也可用于加载文件。您实际上应该只将其用于加载小文件，因为不会向用户提供进度反馈。使用以下语法：

```ts
class BootScene extends Phaser.Scene {
  constructor() {
    super({
      key: 'boot',
      files: [
        { type: 'image', key: 'bar', url: 'loaderBar.png' },
        { type: 'image', key: 'bg', url: 'background.png' },
      ],
    });
  }
}
```

它非常适合加载极少量的图形，即背景和进度条图像，然后你的预加载器场景可以在加载所有其他游戏资产时显示这些图像。或者，您可以使用它来加载内部游戏配置，可以在加载其余资产之前对其进行解析。

场景系统类是场景的核心。它控制所有场景插件，发出事件，允许您修改场景（例如将其发送到睡眠状态、唤醒场景等），并允许所有插件相互通信。
它会自动将 7 个核心插件安装到您的场景中。
还有 7 个默认插件 。
请记住，核心插件无法删除。要删除所有默认插件，您只需在场景配置中传递一个空的插件数组：

```ts
class BootScene extends Phaser.Scene {
  constructor() {
    super({
      key: 'boot',
      plugins: [],
    });
  }
}
```

如果你现在尝试在场景中使用 this.load，它将失败，因为加载器插件已从该场景中排除。

如果您只想要加载器和补间插件，而不需要其他插件，则可以仅指定以下插件：

```ts
class BootScene extends Phaser.Scene {
  constructor() {
    super({
      key: 'boot',
      plugins: ['Loader', 'TweenManager'],
    });
  }
}
```

如果你不确定在创建场景时需要哪些插件，可以通过场景系统安装它们。这必须在 init 方法中完成：

```ts
class BootScene extends Phaser.Scene {
  constructor() {
    super({
      key: 'boot',
      plugins: [],
    });
  }

  init() {
    this.sys.install('TweenManager');
  }
}
```

调用 install 告诉它安装默认插件 TweenManager，允许您在此场景中添加补间。

### Scene Callbacks 场景回调

场景回调方法是 init（）、preload（）、create（） 和 update（）。 原型回调模式是

```ts
// Configure this scene cycle
function init(data) {}

// Queue assets for downloading
function preload() {}

// Create game objects with loaded assets
function create(data) {}

// Work on game objects at each game step
function update(time, delta) {}
```

init（） 不太常用。
您可以使用任何方法创建游戏对象，但如果您在 preload（） 中将资产排队，则您将无法在 create（） 之前使用这些资产。

### 创建多场景游戏

在多场景游戏中，每个场景都需要一个唯一的键。
同样，在游戏配置中添加场景是最简单的。第一个场景以及带有 { active： true } 的任何其他场景将自动启动。

```ts
const bootSceneConfig = { key: 'boot' /*…*/ };
const playSceneConfig = { key: 'play' /*…*/ };
const uiSceneConfig = { key: 'ui', active: true };

new Phaser.Game({
  // 'boot' and 'ui' will be started
  scene: [bootSceneConfig, playSceneConfig, uiSceneConfig],
});
```

游戏配置中的多个场景实例

```ts
// Scene classes:
class BootScene {
  /*…*/
}
class PlayScene {
  /*…*/
}
class UIScene {
  /*…*/
}

new Phaser.Game({
  // 'boot' and 'ui' will be started
  scene: [
    new BootScene('boot'),
    new PlayScene('play'),
    new UIScene({ key: 'ui', active: true }),
  ],
});
```

### Changing scenes 场景变换

start（'target'） 启动目标场景并停止调用场景。它相当于 stop（）.launch（'target'）。
如果您想在不停止任何内容的情况下开始第二个场景，请改用 launch（'target'） 。

```ts
// In scene A: stop A, start B
this.scene.start('B');

// In scene B: stop B, start C
this.scene.start('C');

// In scene C: stop C, start A again
this.scene.start('A');
```

switch（'target'） 启动或唤醒目标场景，并使调用场景休眠。

```ts
// In scene A: sleep A, start B
this.scene.switch('B');

// In scene B: sleep B, start C
this.scene.switch('C');

// In scene C: sleep C, *wake* A
this.scene.switch('A');
```

## 碰撞检测

开启物理引擎

```ts
const config = {
  // ...
  physics: {
    default: 'arcade',
    // arcade: {
    //   x: 0,
    //   y: 0,
    //   width: scene.sys.scale.width,
    //   height: scene.sys.scale.height,
    //   gravity: {
    //     x: 0,
    //     y: 0,
    //   },
    //   checkCollision: {
    //     up: true,
    //     down: true,
    //     left: true,
    //     right: true,
    //   },
    //   customUpdate: false,
    //   fixedStep: true,
    //   fps: 60,
    //   timeScale: 1, // 2.0 = half speed, 0.5 = double speed
    //   customUpdate: false,
    //   overlapBias: 4,
    //   tileBias: 16,
    //   forceX: false,
    //   isPaused: false,
    //   debug: false, // 开启调试模式
    //   debugShowBody: true,
    //   debugShowStaticBody: true,
    //   debugShowVelocity: true,
    //   debugBodyColor: 0xff00ff,
    //   debugStaticBodyColor: 0x0000ff,
    //   debugVelocityColor: 0x00ff00,
    //   maxEntries: 16,
    //   useTree: true, // set false if amount of dynamic bodies > 5000
    // },
  },
  // ...
};

const game = new Phaser.Game(config);
```

```ts
class PhysicsObject extends Phaser.GameObjects.Container {
  constructor(scene) {
    super(scene);

    this.scene.physics.add.existing(this);
    // 物理大小和偏移量
    this.body.setSize(w, h);
    this.body.setOffset(ox, oy);
  }

  test() {
    this.scene.physics.overlap(this, other);
  }
}
```

## 物体变形

[codepen](https://codepen.io/rexrainbow/pen/xxLeyez)
[github](https://github.com/rexrainbow/phaser3-rex-notes)

```ts
class Demo extends Phaser.Scene {
  constructor() {
    super({
      key: 'examples',
    });
  }

  preload() {
    this.load.plugin(
      'rexquadimageplugin',
      'https://raw.githubusercontent.com/rexrainbow/phaser3-rex-notes/master/dist/rexquadimageplugin.min.js',
      true
    );

    this.load.image(
      'card2',
      'https://raw.githubusercontent.com/rexrainbow/phaser3-rex-notes/master/assets/images/card2.png'
    );
  }

  create() {
    var image0 = CreateCard(this, 200, 300, false);
    var image1 = CreateCard(this, 500, 300, true);

    this.debug = this.add.graphics();
    image0.setDebug(this.debug);
    image1.setDebug(this.debug);
  }

  update() {
    this.debug.clear();
    this.debug.lineStyle(1, 0x00ff00);
  }
}

var CreateCard = function (scene, x, y, rtl) {
  scene.add.image(x, y, 'card2').setScale(0.5);
  var image = scene.add
    .rexQuadImage(x, y, 'card2', null, {
      hideCCW: false,
      rtl: rtl,
    })
    .setAlpha(0.8)
    .setScale(0.5);

  var controlPoints = image.controlPoints;
  for (var i = 0, cnt = controlPoints.length; i < cnt; i++) {
    CreateControlCircle(scene, controlPoints[i]);
  }

  return image;
};

var CreateControlCircle = function (scene, controlPoint) {
  var circle = scene.add
    .circle(controlPoint.x, controlPoint.y, 10, 0xff0000)
    .setInteractive({ draggable: true })
    .on('drag', function (pointer, dragX, dragY) {
      circle.x = dragX;
      circle.y = dragY;
      controlPoint.setPosition(dragX, dragY);
    });
  return circle;
};

var config = {
  type: Phaser.AUTO,
  parent: 'phaser-example',
  width: 800,
  height: 600,
  scale: {
    mode: Phaser.Scale.FIT,
    autoCenter: Phaser.Scale.CENTER_BOTH,
  },
  scene: Demo,
  backgroundColor: 0x33333,
};

var game = new Phaser.Game(config);
```

## 着色方式

- setTint: 采用插值着色。如果您设置了多个角的颜色，它会在这几个颜色之间生成平滑的渐变
- setTintFill‌: 采用填充着色。它使用单一颜色替换整个游戏对象的像素，使其变成纯色块
- clearTint: 清除着色。恢复游戏对象的原始颜色

```ts
const img = this.scene.add.image(0, 0, `image_key`);
img.setTint(0xff0000, 0x00ff00, 0x0000ff, 0xffff00);
img.setTintFill(0xffffff);
img.clearTint();
```

## 镂空点击事件

```ts
const hitArea = new Phaser.Geom.Rectangle(
  0,
  0,
  this.scene.scale.width,
  this.scene.scale.height
);
const hitAreaCallback = (area: Phaser.Geom.Rectangle, x: number, y: number) => {
  if (data.boundsArray) {
    for (const bound of data.boundsArray) {
      if (bound.contains(x, y)) {
        // 点击了高亮区域 穿透点击事件
        return false;
      }
    }
  }
  // 点击了遮罩区域 捕获点击事件
  return true;
};
this.guideMask.setInteractive(hitArea, hitAreaCallback);
```
## 镂空Demo
```ts
interface IHoleConfig {
  bounds: Phaser.Geom.Rectangle;
  padding?: number;
  radius?: number;
  shape?: 'rect' | 'circle';
}
interface IShowConfig {
  alpha?: number;
  touchClose?: boolean;
}

export class GuideMask extends Phaser.GameObjects.Container {
  private hitArea: Phaser.Geom.Rectangle;
  private guideMask: Phaser.GameObjects.Graphics;
  private maskGraphics: Phaser.GameObjects.Graphics;

  private handSprite: Phaser.GameObjects.Sprite;
  constructor(scene: Phaser.Scene, x: number = 0, y: number = 0) {
    super(scene, x, y);
    this.name = 'GuideMaskContainer';
    this.init();
  }
  init() {
    const { width, height } = this.scene.scale;

    this.hitArea = new Phaser.Geom.Rectangle(0, 0, width, height);
    this.guideMask = this.scene.add.graphics();
    this.guideMask.name = 'GuideMask';
    this.add(this.guideMask);

    this.maskGraphics = this.scene.make.graphics(
      { fillStyle: { color: 0xffffff } },
      false
    );

    // 创建帧动画
    if (!this.scene.anims.exists('hand_click_anim')) {
      this.scene.anims.create({
        key: 'hand_click_anim',
        frames: [{ key: 'finger_down' }, { key: 'finger_up' }],
        frameRate: 2.5, // 每秒2.5帧，相当于每帧400ms，总循环800ms
        repeat: -1, // 无限循环
        yoyo: false, // 不需要yoyo，因为我们直接在两帧之间切换
      });
    }

    // 使用sprite替代image以支持动画
    this.handSprite = this.scene.add.sprite(0, 0, 'finger_down');
    this.handSprite.setOrigin(0, 0);
    this.handSprite.setAlpha(0);
    this.add(this.handSprite);

    this.setVisible(false);
    this.scene.add.existing(this);
  }

  show(holes: IHoleConfig | IHoleConfig[], config?: IShowConfig) {
    const { alpha = 0.5, touchClose = true } = config;

    const hitAreaCallback = (
      _: Phaser.Geom.Rectangle,
      x: number,
      y: number
    ) => {
      return true;
      if (Array.isArray(holes)) {
        for (const hole of holes) {
          if (hole.bounds.contains(x, y)) {
            return true;
          }
        }

        return false;
      } else {
        return holes.bounds.contains(x, y);
      }
    };

    this.guideMask.clear();
    this.maskGraphics.clear();

    this.guideMask.fillStyle(0x000000, alpha);
    this.guideMask.fillRect(0, 0, this.hitArea.width, this.hitArea.height);

    this.guideMask.setInteractive(this.hitArea, hitAreaCallback);
    this.guideMask.on('pointerdown', (pointer: Phaser.Input.Pointer) => {
      if (Array.isArray(holes)) {
        if (holes.some((hole) => hole.bounds.contains(pointer.x, pointer.y))) {
          this.hide();
          const hitObjects = this.scene.input.hitTestPointer(pointer);
          for (const obj of hitObjects) {
            if (obj !== this.guideMask && obj.input?.enabled) {
              obj.emit(
                'pointerdown',
                pointer,
                obj.input.localX,
                obj.input.localY
              );
              break;
            }
          }
        }
      }
    });

    if (Array.isArray(holes)) {
      holes.forEach((hole) => this.makeHole(hole));
      this.handSprite.setPosition(
        holes[0].bounds.x + holes[0].bounds.width * 0.5,
        holes[0].bounds.y + holes[0].bounds.height * 0.5
      );
    } else {
      this.makeHole(holes);
      this.handSprite.setPosition(
        holes.bounds.x + holes.bounds.width * 0.5,
        holes.bounds.y + holes.bounds.height * 0.5
      );
    }
    this.handSprite.play('hand_click_anim');
    this.handSprite.setAlpha(1);

    const highlight = this.maskGraphics.createGeometryMask();
    highlight.setInvertAlpha(true);
    this.guideMask.setMask(highlight);

    this.setVisible(true);
  }

  makeHole(hole: IHoleConfig) {
    const { bounds, padding = 20, radius = 20, shape = 'rect' } = hole;

    if (shape === 'circle') {
      this.maskGraphics.fillCircle(
        bounds.x + bounds.width * 0.5,
        bounds.y + bounds.height * 0.5,
        Math.max(bounds.width, bounds.height) * 0.5 + padding
      );
    } else {
      this.maskGraphics.fillRoundedRect(
        bounds.x - padding,
        bounds.y - padding,
        bounds.width + padding * 2,
        bounds.height + padding * 2,
        radius
      );
    }
  }

  hide() {
    if (this.handSprite) {
      this.handSprite.stop();
      this.handSprite.setAlpha(0);
    }

    this.guideMask.removeInteractive();
    this.guideMask.removeAllListeners();

    this.setVisible(false);
  }
}
```
