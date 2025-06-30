---
title: Initial-Git
date: 2025-06-30 13:35:26
tags: ['Git']
---

## 安装
```bash
# mac
brew install git
# ubuntu
sudo apt install git
```

## 生成公钥
```bash
ssh-keygen -t rsa -b 4096 -C "your.email@example.com"
ssh-keygen -m PEM -t ed25519 -C "your.email@example.com"
# 不支持Ed25519可使用以下
ssh-keygen -m PEM -t rsa -b 4096 -C "your.email@example.com"
# 验证conding
ssh -T git@e.coding.net
# 验证github
ssh -T git@github.com
```

#### 拉取代码异常时
> Unable to negotiate with 106.52.160.162 port 22: no matching host key type found. Their offer: ssh-rsa
> fatal: Could not read from remote repository.
> Please make sure you have the correct access rights
and the repository exists.

```bash
# ~/.ssh/config
Host *
    HostkeyAlgorithms +ssh-rsa
    PubkeyAcceptedKeyTypes +ssh-rsa
```