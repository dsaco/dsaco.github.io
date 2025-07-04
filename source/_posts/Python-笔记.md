---
title: Python - 笔记
date: 2025-07-02 14:19:10
tags: ['Python']
---

### 虚拟环境

```bash
# 创建
python -m venv myenv
# 激活
source myenv/bin/activate
# 退出
deactivate
```

### 依赖

```bash
pip freeze > requirements.txt
pip install -r requirements.txt

# 使用pipreqs生成依赖
pip install pipreqs
pipreqs . --encoding=utf8 --force
```
