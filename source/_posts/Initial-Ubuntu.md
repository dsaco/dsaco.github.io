---
title: Initial - Ubuntu
date: 2026-03-12 13:55:36
tags:
---

## 下载nodejs

```bash
# 下载并安装 nvm：
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.40.3/install.sh | bash

# 代替重启 shell
\. "$HOME/.nvm/nvm.sh"

# 下载并安装 Node.js：
nvm install 24

# 验证 Node.js 版本：
node -v # Should print "v24.14.0".

# 验证 npm 版本：
npm -v # Should print "11.9.0".

```

## 安装opencode

```bash
npm install -g opencode-ai
```

## 安装docker、docker-compose

```bash
apt update
apt install docker.io

apt install -y ca-certificates curl gnupg
install -m 0755 -d /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | gpg --dearmor -o /etc/apt/keyrings/docker.gpg
chmod a+r /etc/apt/keyrings/docker.gpg

apt update
apt install -y docker-compose-plugin
```

## new-api

[new-api仓库](https://github.com/QuantumNous/new-api)
[new-api中文文档](https://docs.newapi.pro/zh/docs/installation)

```bash
# 第一步
# 将new-api/docker-compose.yaml拷贝至本地new-api目录

# 第二步
# 进入new-api目录
# 启动new-api
docker docker-compose up -d

# 但三步
# 创建密码
```

## api-proxy-cli

[api-proxy-cli仓库](https://github.com/router-for-me/CLIProxyAPI)

```bash
mkdir api-proxy-cli
cd api-proxy-cli
curl -L -o config.yaml https://raw.githubusercontent.com/router-for-me/CLIProxyAPI/main/config.example.yaml
# 修改config.yaml
# 启动
docker run -d --rm -p 8317:8317 -v ~/api-proxy-cli/config.yaml:/CLIProxyAPI/config.yaml eceasy/cli-proxy-api:latest
# 访问
# ip:port/management.html

# 在本机建立 SSH 本地端口转发，把本机的 1455 端口转到远端服务器的 127.0.0.1:1455
ssh -L 1455:127.0.0.1:1455 root@xx.xx.xx.xx -p 22
# oauth 登录codex
docker exec -it <容器ID或名称> /CLIProxyAPI/CLIProxyAPI --codex-login --no-browser

# 启动nginx
docker run -d --name my-nginx --restart unless-stopped --add-host=host.docker.internal:host-gateway -p 80:80 -v /etc/nginx/nginx.conf:/etc/nginx/nginx.conf nginx:latest
```
