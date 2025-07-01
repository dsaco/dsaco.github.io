---
title: MCP
date: 2025-07-01 10:19:35
tags: ["MCP", "modelcontextprotocol"]
---

# 什么是 MCP（Model Context Protocol）？
MCP 是一种开源协议，旨在以标准化的方式向大型语言模型（LLM）提供上下文信息。

- 类比理解： 可以把 MCP 想象成 AI 领域的“U盘”。我们知道，U盘可以存储各种文件，插入电脑后就能直接使用。类似地，MCP Server 上可以“插”上各种提供上下文的“插件”，LLM 可以根据需要向 MCP Server 请求这些插件，从而获取更丰富的上下文信息，增强自身能力。
- 与 Function Tool 的对比： 传统的 Function Tool（函数工具）也可以为 LLM 提供外部功能，但 MCP 更像是一种更高维度的抽象。Function Tool 更多的是针对具体任务的工具，而 MCP 则提供了一种更通用的、模块化的上下文获取机制。

MCP 的核心优势
1. 标准化： MCP 提供了统一的接口和数据格式，使得不同的 LLM 和上下文提供者可以无缝协作。
2. 模块化： MCP 允许开发者将上下文信息分解为独立的模块（插件），方便管理和复用。
3. 灵活性： LLM 可以根据自身需求动态选择所需的上下文插件，实现更智能、更个性化的交互。
4. 可扩展性： MCP 的设计支持未来添加更多类型的上下文插件，为 LLM 的能力拓展提供了无限可能。

[MCP官网](https://modelcontextprotocol.io/introduction)
[typescript-sdk](https://github.com/modelcontextprotocol/typescript-sdk)
[python-sdk](https://github.com/modelcontextprotocol/python-sdk)
[cherry studio](https://github.com/CherryHQ/cherry-studio)

### 创建MCP应用
```bash
npm install -g yo generator-mcp

yo mcp -n "Weather MCP Server"

```

### 测试
```bash
npx @modelcontextprotocol/inspector dist/index.js
```