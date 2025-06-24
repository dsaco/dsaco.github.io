---
title: terminal
date: 2025-06-05 17:26:15
tags: ['命令行']
---

### 端口相关

- 查看端口占用情况

```bash
lsof -i tcp:3000
```

- 查看启动服务的端口

```bash
netstat -tunlp
```

- 查看服务相关信息(PID、CPU 占用率、内存占用率)

```bash
ps -aux | grep redis
```

- 查看服务是否启动(返回 PID)

```bash
pgrep mongo -l
pgrep node -l
```

- 删除进程

```bash
kill -9 PID
```

### 文件

- 移动文件(file2 如果是文件可重命名)

```bash
mv file1 file2
```

- 远程拷贝文件

```bash
scp file1 user@ip:/path/to/file2
# 使用.pem文件验证身份
scp -i ~/.ssh/xxx.pem  root@123.456.789.0:/etc/nginx/nginx.conf .
```

### 获取服务器处理器信息
```bash
uname -m
```