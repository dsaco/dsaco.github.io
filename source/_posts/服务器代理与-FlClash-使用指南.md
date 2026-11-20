---
title: 服务器代理与 FlClash 使用指南
date: 2026-11-20 10:30:00
tags:
  - 服务器
  - Docker
  - 网络代理
  - FlClash
categories:
  - 技术实践
---

自己有一台海外服务器，可以在上面部署常驻代理，再用电脑上的 FlClash 自动分流：国内网站直连，需要代理的网站通过服务器访问，家里的打印机和 NAS 保持本地连接。

本文分成两部分：第一部分介绍服务器如何部署代理；第二部分介绍个人电脑如何使用 FlClash。只负责使用的人，可以直接从第二部分开始。

<!-- more -->

## 一、如何在服务器上配置代理

### 1. 先理解各个组件的作用

本文采用 **sing-box + Caddy + FlClash**，代理协议使用 **Trojan over WebSocket over TLS**。

| 组件 | 安装位置 | 作用 |
| --- | --- | --- |
| sing-box | 海外服务器 | 验证客户端密码，将请求转发到目标网站 |
| Caddy | 海外服务器 | 提供 HTTPS 入口、申请和续期证书，将指定路径交给 sing-box |
| FlClash | 自己的电脑 | 根据规则决定直连或使用代理 |

一次代理请求的路径是：

```text
电脑上的应用
  → FlClash 判断分流规则
  → HTTPS 加密连接到服务器的 Caddy
  → sing-box 验证密码并转发
  → 目标网站
```

这里的 **Trojan** 是代理协议名称。TLS 负责电脑到服务器之间的传输加密，WebSocket 让代理连接可以通过 Caddy 的 HTTPS 入口转发。它们不保证任何网络环境下都能连接，实际效果仍取决于线路和网络策略。

这是一套常驻服务。部署完成后，日常使用只需要打开 FlClash，不需要再建立 SSH 隧道或运行启动脚本。

### 2. 准备服务器、域名和 Docker

本文示例环境为 Ubuntu 24.04，sing-box 固定使用 `1.14.4`，Caddy 使用 `2-alpine`。个人使用的资源开销通常较小，可以和已有网站共用服务器；大量下载时主要关注带宽、流量额度和 CPU 使用情况。

准备以下内容：

- 一台能通过 SSH 管理的 Linux 服务器。
- 一个域名，例如 `proxy.example.com`，A 记录指向服务器公网 IPv4。
- Docker Engine 和 Docker Compose 插件。
- 云平台防火墙和系统防火墙允许入站 TCP 80、443；SSH 管理入口按实际需要开放。

如果使用 AWS Lightsail，建议先绑定静态 IPv4，再配置域名解析，避免停止并重新启动实例后公网 IP 改变。只有服务器、DNS 和防火墙都支持 IPv6 时，才添加 AAAA 记录。

Docker 可按照 [Ubuntu 官方安装说明](https://docs.docker.com/engine/install/ubuntu/)安装。安装后检查：

```bash
sudo docker version
sudo docker compose version
sudo systemctl enable --now docker
```

**下面的完整 Compose 示例适用于 80、443 端口空闲的服务器。** 如果已有 Caddy、Nginx 或网站占用了这些端口，先看本节后面的“与已有网站共用服务器”，不要再启动第二个占用相同端口的 Caddy。

### 3. 创建部署目录和密码

```bash
sudo install -d -m 700 /opt/tokyo-proxy
sudo chown "$(id -u):$(id -g)" /opt/tokyo-proxy
cd /opt/tokyo-proxy

openssl rand -hex 32
```

最后一条命令生成随机密码。把它保存在自己的密码管理器中，并替换下文服务端和客户端里的 `REPLACE_WITH_RANDOM_PASSWORD`，两端必须一致。

本文全部域名和密码都是示例值。实际密码、私钥以及可直接使用的客户端配置，不要提交到公开仓库。

### 4. 配置 sing-box

在部署目录创建 `config.json`：

```json
{
  "log": {
    "level": "warn",
    "timestamp": true
  },
  "dns": {
    "servers": [
      {
        "type": "udp",
        "tag": "public-dns",
        "server": "1.1.1.1"
      }
    ]
  },
  "inbounds": [
    {
      "type": "trojan",
      "tag": "personal-proxy",
      "listen": "::",
      "listen_port": 8443,
      "users": [
        {
          "name": "personal",
          "password": "REPLACE_WITH_RANDOM_PASSWORD"
        }
      ],
      "transport": {
        "type": "ws",
        "path": "/proxy-tunnel"
      }
    }
  ],
  "outbounds": [
    {
      "type": "direct",
      "tag": "direct"
    }
  ],
  "route": {
    "default_domain_resolver": "public-dns",
    "rules": [
      {
        "action": "resolve"
      },
      {
        "ip_is_private": true,
        "action": "reject"
      }
    ],
    "final": "direct"
  }
}
```

这里有三个需要理解的配置：

- `8443` 是容器内部的代理端口，后续不会直接映射到公网。
- `/proxy-tunnel` 是 WebSocket 路径，客户端、Caddy 和 sing-box 必须一致。路径可以自定义，真正的身份验证依靠密码。
- 路由先解析域名，再拒绝私网目标，避免代理被用来访问服务器的内部服务。

sing-box 这一层不配置 TLS，因为 TLS 已经在前面的 Caddy 终止。电脑到 Caddy 的公网连接仍然使用 HTTPS。

### 5. 配置 Caddy

在同一目录创建 `Caddyfile`，将域名替换成自己的：

```caddyfile
proxy.example.com {
    handle /proxy-tunnel {
        reverse_proxy proxy-server:8443
    }

    handle {
        respond "OK" 200
    }
}
```

Caddy 会自动申请和续期证书。前提是域名已经解析到本机，外网可以访问 80、443，且没有其他服务抢占端口。

直接在浏览器访问域名时会看到 `OK`。**看到 `OK` 只说明 HTTPS 入口正常，不代表代理认证和转发已经通过验证。** 真正的代理测试要等客户端连接后进行。

### 6. 使用 Docker Compose 启动

创建 `compose.yaml`：

```yaml
services:
  proxy-server:
    image: ghcr.io/sagernet/sing-box:v1.14.4
    command: ["run", "-c", "/etc/sing-box/config.json"]
    restart: unless-stopped
    volumes:
      - ./config.json:/etc/sing-box/config.json:ro
    cap_drop: [ALL]
    security_opt: [no-new-privileges:true]
    mem_limit: 256m
    logging:
      driver: json-file
      options:
        max-size: "5m"
        max-file: "2"

  caddy:
    image: caddy:2-alpine
    restart: unless-stopped
    depends_on:
      - proxy-server
    ports:
      - "80:80/tcp"
      - "443:443/tcp"
    volumes:
      - ./Caddyfile:/etc/caddy/Caddyfile:ro
      - caddy_data:/data
      - caddy_config:/config
    logging:
      driver: json-file
      options:
        max-size: "5m"
        max-file: "2"

volumes:
  caddy_data:
  caddy_config:
```

两个服务自动加入同一个 Compose 网络，Caddy 可以通过服务名 `proxy-server` 访问 sing-box。公网只开放 Caddy 的 TCP 80 和 443；Trojan 的 UDP 转发也可以封装在这条连接中，不需要为它单独开放公网 UDP 8443。

配置文件包含密码，启动前收紧权限：

```bash
sudo chown root:root config.json
sudo chmod 600 config.json

sudo docker compose pull
sudo docker compose run --rm proxy-server check -c /etc/sing-box/config.json
sudo docker compose run --rm --no-deps caddy caddy validate --config /etc/caddy/Caddyfile --adapter caddyfile
sudo docker compose up -d
sudo docker compose ps
```

`config.json` 设为 root 所有，是为了让这个固定版本镜像中的 root 进程在移除额外 capabilities 后仍能读取文件。后续需要通过 `sudo` 编辑它；如果换成其他 UID 运行容器，需要同步调整文件所有者。

`restart: unless-stopped` 配合 Docker 开机启动，让服务在服务器重启后自动恢复。如果自己手动停止了容器，需要再次执行 `sudo docker compose up -d`。

查看启动和证书情况：

```bash
sudo docker compose logs --tail 100 proxy-server
sudo docker compose logs --tail 100 caddy
curl --fail --show-error https://proxy.example.com/
```

### 7. 与已有网站共用服务器

如果服务器已经通过 Caddy 运行 Sub2API 或其他网站，可以复用它的 HTTPS 入口。

先保留已有 Caddy 服务，只部署上面 Compose 文件中的 `proxy-server`。让它加入现有 Caddy 使用的 Docker 网络。下面是需要并入代理 Compose 文件的网络配置片段，`EXISTING_CADDY_NETWORK` 要替换为实际网络名称：

```yaml
services:
  proxy-server:
    # image、command、volumes 等保留前面的代理服务配置
    networks:
      - existing-web

networks:
  existing-web:
    external: true
    name: EXISTING_CADDY_NETWORK
```

可以通过 `sudo docker network ls` 和 `sudo docker inspect <现有Caddy容器名>` 确认网络。不要猜测网络名，也不要复制新建服务器示例中的第二个 Caddy 服务。

例如已有网站由 `sub2api:8080` 提供，可以在原来的站点配置中增加代理路径：

```caddyfile
proxy.example.com {
    handle /proxy-tunnel {
        reverse_proxy proxy-server:8443
    }

    handle {
        reverse_proxy sub2api:8080 {
            flush_interval -1
        }
    }
}
```

只有 `/proxy-tunnel` 交给代理服务，其他请求仍然交给原网站。这里用 `handle` 保留完整路径；不要换成会剥离前缀的 `handle_path`，否则可能与 sing-box 配置的 WebSocket 路径不一致。

修改前备份原文件，保留原站点需要的其他配置。修改后在已有 Caddy 容器里执行检查和热加载，容器名按实际情况替换：

```bash
CADDY_CONTAINER='替换成现有Caddy容器名'
sudo docker exec "$CADDY_CONTAINER" caddy validate --config /etc/caddy/Caddyfile --adapter caddyfile
sudo docker exec "$CADDY_CONTAINER" caddy reload --config /etc/caddy/Caddyfile --adapter caddyfile
```

完成后分别验证原网站和代理连接。代理工作正常，不代表网站的接口和流式响应也一定正常。

### 8. 日常维护和流量计费

```bash
cd /opt/tokyo-proxy

# 查看运行状态和资源占用
sudo docker compose ps
sudo docker stats --no-stream

# 修改代理配置后先检查，再重启代理服务
sudo docker compose run --rm proxy-server check -c /etc/sing-box/config.json
sudo docker compose restart proxy-server
```

保存好部署配置和 Caddy 的证书数据卷。更新镜像时先查看版本说明，确认配置兼容，不要直接追随未经检查的 `latest`。

代理和同一实例上的网站共用机器资源与流量额度。例如从网站下载约 1GB 文件，服务器会先接收约 1GB，再向电脑发送约 1GB；如果套餐按入站和出站合计消耗额度，就会占用约 2GB，另有协议开销。超额费用如何计算，需要以所在区域和套餐的计费规则为准。

## 二、个人如何使用 FlClash

### 1. 安装客户端

从 [FlClash 官方仓库](https://github.com/chen08209/FlClash)的 [Releases 页面](https://github.com/chen08209/FlClash/releases)下载适合自己系统的安装包。

- Windows：选择对应 CPU 架构的 Windows 安装包。
- macOS：Apple Silicon 选择 ARM64，Intel Mac 选择 x64。

不同版本界面可能略有变化，但配置、规则模式、系统代理、虚拟网卡和启动按钮的作用相同。

### 2. 准备客户端配置文件

如果服务器由别人维护，获取对方提供的配置文件后，直接跳到下一步导入。

如果按第一部分自己部署，将下面内容保存为 `Tokyo-Home.yaml`。替换其中所有 `proxy.example.com`、密码和 WebSocket 路径，使其与服务端一致。

```yaml
mixed-port: 7890
allow-lan: false
bind-address: 127.0.0.1
mode: rule
log-level: info
ipv6: false

proxies:
  - name: Tokyo-Server
    type: trojan
    server: proxy.example.com
    port: 443
    password: "REPLACE_WITH_RANDOM_PASSWORD"
    sni: proxy.example.com
    skip-cert-verify: false
    udp: true
    network: ws
    ws-opts:
      path: /proxy-tunnel
      headers:
        Host: proxy.example.com

proxy-groups:
  - name: Tokyo
    type: select
    proxies:
      - Tokyo-Server

rule-providers:
  cn-domains:
    type: http
    behavior: domain
    url: https://raw.githubusercontent.com/Loyalsoldier/clash-rules/release/direct.txt
    path: ./rules/tokyo-cn-domains.yaml
    interval: 86400
    proxy: Tokyo
  cn-ip:
    type: http
    behavior: ipcidr
    url: https://raw.githubusercontent.com/Loyalsoldier/clash-rules/release/cncidr.txt
    path: ./rules/tokyo-cn-ip.yaml
    interval: 86400
    proxy: Tokyo

rules:
  # 自己指定的直连域名放在最前面，按实际需要替换或删除
  - DOMAIN,api.example.com,DIRECT
  - DOMAIN,my-server.example.net,DIRECT
  - DOMAIN,localhost,DIRECT
  - DOMAIN-SUFFIX,local,DIRECT
  - DOMAIN-SUFFIX,lan,DIRECT
  - IP-CIDR,127.0.0.0/8,DIRECT,no-resolve
  - IP-CIDR,10.0.0.0/8,DIRECT,no-resolve
  - IP-CIDR,172.16.0.0/12,DIRECT,no-resolve
  - IP-CIDR,192.168.0.0/16,DIRECT,no-resolve
  - IP-CIDR,169.254.0.0/16,DIRECT,no-resolve
  - DOMAIN-SUFFIX,cn,DIRECT
  - RULE-SET,cn-domains,DIRECT
  - RULE-SET,cn-ip,DIRECT
  - MATCH,Tokyo

tun:
  enable: true
  stack: mixed
  auto-route: true
  auto-detect-interface: true
  dns-hijack:
    - any:53

dns:
  enable: true
  ipv6: false
  enhanced-mode: fake-ip
  fake-ip-range: 198.18.0.1/16
  fake-ip-filter:
    - '*.lan'
    - '*.local'
    - localhost
  default-nameserver:
    - 223.5.5.5
    - 119.29.29.29
  nameserver:
    - https://dns.alidns.com/dns-query
  proxy-server-nameserver:
    - https://dns.alidns.com/dns-query
```

客户端使用的分流规则来自 [Loyalsoldier/clash-rules](https://github.com/Loyalsoldier/clash-rules)，由内核通过东京节点下载并缓存，每天检查更新。第一次启动需要等待规则下载完成。

不要把十几万条规则全部展开到主配置文件里。虽然内核可以处理，某些客户端在导入和解析大型 YAML 时会明显卡顿。上面的配置只引用规则文件，日常导入更轻便。

配置包含代理密码，拿到文件的人也可能使用这个节点。通过可信方式传给自己的其他设备；如果需要给不同的人使用，服务端应配置独立用户和密码，方便单独撤销。

### 3. 导入、选中、启动

在 FlClash 中依次操作：

1. 打开 **配置 → 添加配置 → 文件**，导入 `Tokyo-Home.yaml`。
2. 选中刚刚导入的配置。
3. 返回仪表盘，选择 **规则** 模式。
4. 开启 **系统代理**和 **虚拟网卡**。
5. 点击启动按钮，确认状态变为 **运行中**，运行时长开始增加。

**开关打开和连接启动是两件事。** 如果右下角还是播放图标、运行时长为零，说明只是设置好了选项，还没有真正启动代理。

FlClash 的界面设置可能覆盖 YAML 中的对应选项，所以还要检查仪表盘上的实际状态。首次使用虚拟网卡时，Windows 可能需要管理员权限或服务模式，macOS 可能要求安装辅助服务并进行管理员授权；根据当前版本的提示操作。

### 4. 系统代理、虚拟网卡和规则模式分别是什么

| 功能 | 实际作用 |
| --- | --- |
| 系统代理 | 告诉遵循系统代理设置的应用，将请求交给 FlClash |
| 虚拟网卡（TUN） | 在网络层接管流量，让不遵循系统代理设置的程序也能进入分流流程 |
| 规则模式 | 按规则逐条决定直连、代理或其他处理方式 |
| 全局模式 | 将交给客户端处理的流量统一交给全局策略，不再按这份规则逐条分流 |
| 直连模式 | 将交给客户端处理的流量直接连接目标 |

虚拟网卡是软件创建的网络接口，不需要购买硬件。开启它也不等于全部流量都通过海外服务器。

例如在本文配置中：

```text
访问国内网站       → FlClash → 直连
访问指定直连域名   → FlClash → 直连
访问其他网站       → FlClash → 东京服务器 → 网站
访问家里的 NAS     → FlClash → 局域网直连
```

“国内”是通过域名和 IP 规则表判断的，不是客户端自动理解了网站的用途。规则表可能有遗漏，可以用手动规则修正。

本例主要处理 IPv4。应用自行建立的 VPN、特殊路由和 IPv6 等情况，需要结合实际环境检查，不能只凭一个开关判断所有流量的出口。

### 5. 指定某个网站始终直连

将规则放到 `rules` 最前面：

```yaml
rules:
  - DOMAIN,api.example.com,DIRECT
  - DOMAIN,my-server.example.net,DIRECT
  # 后面继续放原来的国内域名、IP 和兜底规则
```

规则从上到下匹配，命中后就使用该规则。`DOMAIN` 只匹配这个完整域名；如果希望主域名及其所有子域名都直连，可以使用：

```yaml
rules:
  - DOMAIN-SUFFIX,example.com,DIRECT
```

不要为了让一个子域名直连，无意中把整个域名下的其他服务也改成直连。修改后重新加载配置，并到请求记录里查看该域名是否显示 `DIRECT`。

FlClash 通常会把导入文件复制到自己的配置目录。修改电脑上最初那份 YAML 后，需要重新导入或在客户端中更新当前配置，不能默认它会同步变化。

### 6. 设置开机自动连接

在 **工具 → 应用程序**中，按当前版本找到并开启：

- **自启动**：登录系统后启动 FlClash。
- **自动运行**：FlClash 启动后运行代理。
- **静默启动**：可选，让客户端在后台启动。

这些设置需要在每台电脑上分别打开，不会因为导入同一个 YAML 就自动同步。系统要求的网络权限也需要在每台电脑上分别授权。

配置完成后的日常操作是：需要使用时运行 FlClash，不需要时暂停连接。服务器上的代理持续运行，不需要每次 SSH 登录。

### 7. 确认代理和分流真的生效

先检查仪表盘：状态为运行中，选中正确配置，规则模式和虚拟网卡已开启。出口 IP 显示海外服务器地址，可以证明该次检测经过了服务器，但不能单独证明所有分流都正确。

再分别验证国外和国内请求。在 macOS、Linux 终端中运行；Windows PowerShell 请把 `curl` 写成 `curl.exe`：

```bash
# 显式不使用 HTTP/SOCKS 代理，验证虚拟网卡是否能接管
curl --noproxy '*' --max-time 20 https://checkip.amazonaws.com

# 验证国外服务可访问，正常响应应为 204
curl --noproxy '*' --max-time 20 -o /dev/null -sS \
  -w 'HTTP %{http_code}, total %{time_total}s\n' \
  https://www.google.com/generate_204

# 访问国内 IP 查询站，并结合请求记录检查 DIRECT
curl --noproxy '*' --max-time 20 https://myip.ipip.net
```

上面的第二条是 Unix shell 的多行写法。Windows PowerShell 中可使用下面这条单行命令：

```powershell
curl.exe --noproxy "*" --max-time 20 -o NUL -sS -w "HTTP %{http_code}, total %{time_total}s\n" https://www.google.com/generate_204
```

`--noproxy '*'` 只是不使用 curl 的应用层代理，不会绕过虚拟网卡。因此在 TUN 正常接管时，即使这样运行，国外请求仍应显示服务器出口。

最后打开 FlClash 的 **请求／连接记录**：国内网站应显示 `DIRECT`，国外网站应显示 `Tokyo` 或 `Tokyo-Server`。指定直连的域名也要实际访问一次再检查。

如果所在网络本来就有其他 VPN 或上游代理，“直连”代表绕过本次配置的东京节点，并不承诺出口一定是家里的公网 IP。

### 8. 如何理解延迟和测速

仪表盘里 Google 显示 `600ms`，通常表示客户端执行一次服务可用性检测花了约 0.6 秒。具体测量范围取决于客户端实现，可能包含建连、TLS 握手和服务响应，不能直接当作“家里到东京服务器的纯网络延迟”。

同样，仪表盘实时显示的 `6KB/s` 或 `2KB/s` 是当时正在传输的数据量，不是线路速度上限。没有下载任务时，速度很低是正常现象。

可以通过下载一个固定大小的测试文件测吞吐量。下面下载 10MB 并丢弃内容：

```bash
curl --noproxy '*' --fail --location --max-time 60 -sS \
  -o /dev/null \
  -w 'bytes=%{size_download}\nbytes_per_second=%{speed_download}\nseconds=%{time_total}\n' \
  'https://speed.cloudflare.com/__down?bytes=10000000'
```

Windows PowerShell 对应命令：

```powershell
curl.exe --noproxy "*" --fail --location --max-time 60 -sS -o NUL -w "bytes=%{size_download}\nbytes_per_second=%{speed_download}\nseconds=%{time_total}\n" "https://speed.cloudflare.com/__down?bytes=10000000"
```

`speed_download` 的单位是字节／秒。换成常见的 Mbps：

```text
Mbps = 字节/秒 × 8 ÷ 1,000,000

例如 960,000 字节/秒 ≈ 0.96 MB/s ≈ 7.68 Mbps
```

测速前确认该请求实际走代理。单连接测速反映的是电脑、当前网络、服务器和测试站点共同形成的结果，不等于服务器带宽上限；测试会实际消耗流量。

### 9. 常见问题

| 现象 | 优先检查 |
| --- | --- |
| 开关都开了，但没有流量 | 是否点击了启动按钮，状态是否为运行中 |
| 浏览器可以，其他软件不行 | 虚拟网卡是否真正启动，系统授权是否完成 |
| 节点无法连接 | 域名解析、TCP 443、防火墙、证书、密码和 WebSocket 路径是否匹配 |
| 证书报错 | 域名、SNI、系统时间和证书链，不要通过关闭证书校验来掩盖问题 |
| 国内网站也走代理 | 是否选成全局模式，规则集是否下载完成，自定义规则是否排在兜底规则前面 |
| 导入配置后界面卡住 | 主 YAML 是否塞入了大量内联规则，优先改用 `rule-providers` |
| 重启电脑后没有自动连接 | 当前电脑的自启动、自动运行和系统权限是否已经设置 |
| 显示 `198.18.0.1` | 本例中属于虚拟网卡／Fake-IP 使用的内部地址，公网出口应看网络检测结果 |

参考资料：[sing-box Trojan 入站](https://sing-box.sagernet.org/configuration/inbound/trojan/)、[Caddy reverse_proxy](https://caddyserver.com/docs/caddyfile/directives/reverse_proxy)、[Mihomo 文档](https://wiki.metacubex.one/)、[FlClash](https://github.com/chen08209/FlClash)、[分流规则来源](https://github.com/Loyalsoldier/clash-rules)。
