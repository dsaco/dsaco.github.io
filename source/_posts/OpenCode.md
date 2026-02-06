---
title: OpenCode
date: 2026-01-20 09:34:35
tags:
---

[官网](https://opencode.ai/)

[中文教程](https://learnopencode.com/1-start/)

[opencode-antigravity-auth](https://github.com/NoeFabris/opencode-antigravity-auth)
启用 Opencode 通过 OAuth 认证 Antigravity（谷歌 IDE），这样你就可以使用 Antigravity 的速率限制，并用 Google 凭证访问 gemini-3-pro 和 claude-opus-4-5-thinking 等模型。

## XDG_CONFIG_HOME

使用此环境变量可以替换opencode默认的路径`~/.config`

## 配置文件

配置文件是合并的，而不是被替换。以下配置位置的设置会合并。后期配置只针对键冲突时覆盖了早期配置。所有配置中不冲突的设置都会被保留

配置源按以下顺序加载（后续源覆盖早期源）：

- Remote config (from .well-known/opencode) - organizational defaults
- Global config (~/.config/opencode/opencode.json) - user preferences
- Custom config (OPENCODE_CONFIG env var) - custom overrides
- Project config (opencode.json in project) - project-specific settings
- .opencode directories - agents, commands, plugins
- Inline config (OPENCODE_CONFIG_CONTENT env var) - runtime overrides

## 全局提示词

| 作用域   | 位置                         | 适用场景           | 描述                                          |
| -------- | ---------------------------- | ------------------ | --------------------------------------------- |
| 全局     | ~/.config/opencode/AGENTS.md | 所有项目通用的偏好 | 配置时`${XDG_CONFIG_HOME}/opencode/AGENTS.md` |
| 项目     | 项目目录向上查找 AGENTS.md   | 项目特定的规范     | client的directory下的AGENTS.md                |
| 配置文件 | instructions 指定的文件      | 引用多个规则文件   |                                               |

> OpenCode 同时支持 AGENTS.md 和 CLAUDE.md（兼容 Claude Code）。推荐用 AGENTS.md，这是 OpenCode 的标准名称。

### 规则加载顺序

规则按以下顺序加载，后加载的会补充（不是覆盖）前面的:

1. 全局 ~/.config/opencode/AGENTS.md
2. 全局 ~/.claude/CLAUDE.md（兼容模式）
3. 项目目录向上查找 AGENTS.md / CLAUDE.md
4. 配置文件 instructions 指定的文件

```bash
# ~/.config/opencode/AGENTS.md
始终使用简体中文回复
```

### 通用开发规则

```md
## 工作态度

- 每次工作都要用严谨的工作态度，保证完美的质量标准

## 沟通风格

- 直接输出代码或方案，禁止客套话（"抱歉"、"我明白了"等）
- 除非明确要求，否则不提供代码摘要

## 求真原则（禁止瞎猜）

- 不确定/信息不足时先查证或提问澄清
- 对环境/配置/源码/行为的结论必须有证据
- 回答里把"事实"和"推测/假设"分开写
```

### 代码质量规则

```md
## 代码质量原则

- 优先代码可读性，做最简单的修改
- 禁止使用 `eslint-disable` 或 `@ts-ignore` 绕过问题
- 禁止使用 `any` 类型，必须定义明确的类型
- 不要为了向后兼容而保留废弃代码
- 删除未使用的代码，不要注释掉

## 复用优先

- 编写新代码前，先确认项目中是否已有类似实现
- 优先复用现有组件和工具函数，而非新建
```

### 工作流程规则

```md
## 执行规范

- 任何非平凡任务，先制定计划再动手
- 修改代码前必须先阅读相关文件
- 修改完成后自行运行测试验证

## 子代理调度策略

- 尽可能调用子代理完成任务
- 能派给专家的就派，不要什么都自己干
```
