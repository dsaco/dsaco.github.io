---
title: VS Code
date: 2026-01-20 09:47:56
tags:
---

## 我的习惯

快捷键绑定

```json
// Code/User/keybindings.json
[
  {
    "key": "cmd+i",
    "command": "workbench.action.terminal.toggleTerminal",
    "when": "terminal.active"
  },
  {
    "key": "cmd+j",
    "command": "editor.action.copyLinesDownAction",
    "when": "editorTextFocus && !editorReadonly"
  },
  {
    "key": "cmd+down",
    "command": "editor.action.moveLinesDownAction",
    "when": "editorTextFocus && !editorReadonly"
  },
  {
    "key": "cmd+up",
    "command": "editor.action.moveLinesUpAction",
    "when": "editorTextFocus && !editorReadonly"
  },
  {
    "key": "cmd+k",
    "command": "editor.action.deleteLines",
    "when": "textInputFocus && !editorReadonly"
  },
  {
    "key": "cmd+y",
    "command": "stylelint.executeAutofix"
  },
  {
    "key": "cmd+u",
    "command": "editor.action.formatDocument",
    "when": "editorHasDocumentFormattingProvider && editorTextFocus && !editorReadonly && !inCompositeEditor"
  }
]
```

settings

```json
// Code/User/settings.json
{
  "[python]": {
    "editor.defaultFormatter": "ms-python.black-formatter"
  },
  "[csharp]": {
    "editor.defaultFormatter": "csharpier.csharpier-vscode"
  }
}
```

## 插件

- GitLens — Git supercharged 显示git历史提交记录

- Prettier - Code formatter 代码格式化
  可在 控制台 OUTPUT Prettier中查看生效的config
- Stylelint Css样式格式化
- Black Formatter 格式化python代码
- CSharpier - Code formatter 格式化c#代码

- TRAE AI: Coding Assistant 代码补全提示
- Tailwind CSS IntelliSense tailwind智能提示

- Claude Code for VS Code
- opencode

- Unity