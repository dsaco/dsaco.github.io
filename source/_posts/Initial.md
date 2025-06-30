---
title: 初始化
date: 2025-06-03 10:41:39
tags:
---
包含Mac及Ubuntu的初始化

## Mac初始化
### homebrew
```bash
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```
### git
```bash
brew install git
```
生成公钥
```bash
ssh-keygen -t rsa -b 4096 -C "your.email@example.com"
ssh-keygen -m PEM -t ed25519 -C "your.email@example.com"
```


### nvm
[git仓库](https://github.com/nvm-sh/nvm?tab=readme-ov-file#installing-and-updating)
```bash
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.40.3/install.sh | bash
```
> 添加下面代码至 \~/.bash_profile, \~/.zshrc, \~/.profile, \~/.bashrc
```bash
export NVM_DIR="$([ -z "${XDG_CONFIG_HOME-}" ] && printf %s "${HOME}/.nvm" || printf %s "${XDG_CONFIG_HOME}/nvm")"
[ -s "$NVM_DIR/nvm.sh" ] && \. "$NVM_DIR/nvm.sh" # This loads nvm
```
### oh my zsh
[安装教程](https://www.jianshu.com/p/9c3439cc3bdb)
```bash
sh -c "$(curl -fsSL https://raw.githubusercontent.com/robbyrussell/oh-my-zsh/master/tools/install.sh)"
```

### docker
[下载docker desktop](https://www.docker.com/)

## Ubuntu初始化
```bash
# 更新源
apt update
# 生成ssh key
ssh-keygen -t rsa -C name@name.com
```
### git
```bash
apt install git
```
### nvm
```bash
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.40.3/install.sh | bash
```
### node
```bash
nvm install v22
```
### nrm
```bash
npm i -g nrm
```

### conda
```bash
# 获取当前系统处理器
uname -m
```
[conda安装](https://www.anaconda.com/docs/getting-started/miniconda/install#linux)
```bash
mkdir -p ~/miniconda3
wget https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-x86_64.sh -O ~/miniconda3/miniconda.sh
bash ~/miniconda3/miniconda.sh -b -u -p ~/miniconda3
rm ~/miniconda3/miniconda.sh
```


### nginx
```bash
apt install nginx
```
> 默认配置文件地址 /etc/nginx/nginx.conf

```bash
# 启动
sudo systemctl start nginx
sudo service nginx start
# 停止
sudo systemctl stop nginx
sudo service nginx stop
# 重启
sudo systemctl restart nginx
sudo service nginx restart
# 查看状态
sudo systemctl status nginx
sudo service nginx status
# 测试配置文件
sudo nginx -t
```
### redis
```bash
apt install redis-server
```
> 默认配置文件地址 /etc/redis/redis.conf
```bash
# 启动
sudo systemctl start redis
sudo service redis start
# 停止
sudo systemctl stop redis
sudo service redis stop
# 重启
sudo systemctl restart redis
sudo service redis restart
# 查看状态
sudo systemctl status redis
sudo service redis status

# 连接
redis-cli -h 127.0.0.1 -p 6379 -a "password"
# 查看密码
config get requirepass
# 设置密码
config set requirepass xxxxxx
```
配置文件
```bash
# 后台启动
daemonize yes

# 远程连接 + ipv6
bind 0.0.0.0 ::

# 检查后台进程是否正在运行
ps -ef | grep redis
# 检查端口是否再监听
netstat -lntp | grep 6379
# 杀死进程，即强制结束进程
kill -9 PID
```

### mongodb
[官方安装教程](https://www.mongodb.com/zh-cn/docs/manual/tutorial/install-mongodb-on-ubuntu/)
默认配置文件地址
- conf: /etc/mongod.conf
- log: /var/log/mongodb/mongod.log
- dbPath: /var/lib/mongodb
```bash
# 启动
sudo systemctl start mongod
sudo service mongod start
# 停止
sudo systemctl stop mongod
sudo service mongod stop
# 重启
sudo systemctl restart mongod
sudo service mongod restart
# 查看状态
sudo systemctl status mongod
sudo service mongod status

# 数据备份
mongodump    --host=xx.xx.xx.xx    --port=27017    --username=USERNAME    --password="PASSWORD"    --out=/opt/backup/mongodump-1
# 拷贝数据
mongorestore /opt/backup/mongodump-1
# 本地连接
mongosh
# 创建admin账号
use admin
db.createUser({ user: 'USER', pwd: 'PASSWORD', roles: ['userAdminAnyDatabase'] })
# 创建数据库账号
use DATABASE
db.createUser({ user: 'USER', pwd: 'PASSWORD', roles: ['dbOwner'] })
```
角色
```
数据库用户角色：read、readWrite
数据库管理角色：dbAdmin、dbOwner、userAdmin
集群管理角色：clusterAdmin、clusterManager、clusterMonitor、hostManager
备份恢复角色：backup、restore
所有数据库角色：readAnyDatabase、readWriteAnyDatabase、userAdminAnyDatabase、dbAdminAnyDatabase
超级用户角色：root
内部角色：__system

read：允许用户对指定数据库进行读取操作。
readWrite：允许用户对指定数据库进行读写操作。
dbAdmin：允许用户对指定数据库进行管理操作，如索引创建、集合删除等。
userAdmin：允许用户管理数据库中的用户。
dbOwner：允许用户对指定数据库拥有所有权限，相当于readWrite和dbAdmin的组合。
clusterAdmin：[admin]允许用户执行集群级别的管理操作，如添加和删除分片、查看配置等。
clusterManager：允许用户执行集群管理操作，但不能对系统和配置数据库进行操作。
backup：[admin]允许用户执行备份操作。
restore：[admin]允许用户执行还原操作。
root：[admin]超级管理员角色，拥有所有数据库和集群的管理权限。
```

### docker
```bash
apt install docker.io
```