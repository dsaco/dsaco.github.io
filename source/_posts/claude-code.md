---
title: Claude-Code
date: 2025-12-24 09:24:25
tags:
---

[code.claude.com](https://code.claude.com/docs/zh-CN/quickstart)

## 安装

```bash
# native install
curl -fsSL https://claude.ai/install.sh | bash
# npm
npm install -g @anthropic-ai/claude-code
```

## 配置

```json
# ~/.claude/config.json
{
    "primaryApiKey": "self"
}
# ~/.claude/settings.json
{
  "env": {
    "ANTHROPIC_AUTH_TOKEN": "sk-你的API令牌",
    "ANTHROPIC_BASE_URL": "https://wolfai.top",
    "ANTHROPIC_MODEL": "claude-sonnet-4-5-20250929",
    "ANTHROPIC_SMALL_FAST_MODEL": "claude-haiku-4-5-20251001"
  }
}
# ~/.claude.json
{
  // 跳过登录
  "hasCompletedOnboarding": true,
}
```

## cc-switch
```bash
# macos 安装 cc-switch
brew install --cask cc-switch
# 直接去下载 https://github.com/farion1231/cc-switch/releases
```

## CLI - claude-code-router

[claude-code-router](https://github.com/musistudio/claude-code-router)

```bash
npm install -g @anthropic-ai/claude-code
npm install -g @musistudio/claude-code-router
```
