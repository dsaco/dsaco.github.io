---
title: pnpm - 笔记
date: 2025-07-04 14:28:31
tags: ['pnpm', 'npm', 'yarn']
---

### 安装
```bash
npm i -g pnpm
brew install pnpm
curl -fsSL https://get.pnpm.io/install.sh | sh -
```

### CLI
| npm命令 | pnpm等效 |
| - | - |
| `npm install` | `pnpm install` |
| `npm i <pkg>` | `pnpm add <pkg>` |
| `npm run <cmd>` | `pnpm <cmd>` |

```bash
# 执行子目录admin中的scripts dev
pnpm -C ./packages/admin dev
# 筛选项目package.json中name并安装tsx
pnpm --filter "name" add tsx
# 筛选示例
pnpm --filter "@babel/core" test
pnpm --filter "@babel/*" test
pnpm --filter "*core" test
```

### pnpm-workspace.yaml

```bash
packages:
  # 指定根目录直接子目录中的包
  - 'my-app'
  # packages/ 直接子目录中的所有包
  - 'packages/*'
  # components/ 子目录中的所有包
  - 'components/**'
  # 排除测试目录中的包
  - '!**/test/**'
```