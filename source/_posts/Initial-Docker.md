---
title: Initial - Docker
date: 2026-03-03 09:35:40
tags:
---

## nginx

获取 nginx 配置文件

```bash
docker create --name tmp-nginx nginx:latest
docker cp tmp-nginx:/etc/nginx/nginx.conf ./nginx.conf
docker rm tmp-nginx
```

启动服务

```bash
docker run -d \
--name my-nginx \
--restart unless-stopped \
--add-host=host.docker.internal:host-gateway \
-p 80:80 \
-v /etc/nginx/nginx.conf:/etc/nginx/nginx.conf \
nginx:latest
```

查看日志

```bash
# 查看日志
docker logs my-nginx
# 实时追踪日志（类似 tail -f）
docker logs -f my-nginx
# 只看最后 100 行
docker logs --tail 100 my-nginx
# 看最近 10 分钟日志
docker logs --since 10m my-nginx
```
