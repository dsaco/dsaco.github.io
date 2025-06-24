---
title: Initial Conda
date: 2025-06-24 14:42:36
tags:
---

## Ubuntu安装miniconda
```bash
mkdir -p ~/miniconda3
wget https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-x86_64.sh -O ~/miniconda3/miniconda.sh
bash ~/miniconda3/miniconda.sh -b -u -p ~/miniconda3
rm ~/miniconda3/miniconda.sh
```

## 常用命令
```bash
# 查看当前的conda 环境
conda env list
conda info --envs

# 创建环境
conda create --name <env_name> python=3.9
conda create -n <env_name> python=3.8

# 删除环境
conda env remove -n <env_name>

# 进入环境
conda activate <env_name>

# 退出当前环境
conda deactivate
```