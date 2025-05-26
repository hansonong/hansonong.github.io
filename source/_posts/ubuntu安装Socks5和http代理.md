---
title: ubuntu安装SOCKS5和HTTP代理
tags:
  - HTTP
  - SOCKS5
  - proxy/代理
categories:
  - Linux
  - Proxy/代理
---


> ubuntu下，使用 Dante 搭建 SOCKS5 代理和 Squid 搭建 HTTP 代理。


<!-- more -->

## 使用 Dante 搭建 SOCKS5 代理

#### 安装 Dante
```bash
sudo apt update
sudo apt install dante-server
```

#### 配置 Dante（编辑配置文件）

```conf
logoutput: /var/log/danted.log

internal: 0.0.0.0 port = 1080
external: 23.27.168.239

socksmethod: username
user.notprivileged: nobody

client pass {
    from: 0.0.0.0/0 to: 0.0.0.0/0
    log: connect disconnect
}

socks pass {
    from: 0.0.0.0/0 to: 0.0.0.0/0
    protocol: tcp udp
    log: connect disconnect
    command: bind connect udpassociate
}
```

修改了 /etc/danted.conf 配置文件，需要重启 danted
```bash
sudo systemctl restart danted
```

> 注意：`external` 需根据实际网卡名修改，可用 `ip addr` 查看。

#### 添加用户并设置密码
```bash
sudo useradd -M -s /usr/sbin/nologin user2
sudo passwd user2
```


```bash

#### 启动 Dante 服务

```bash
sudo systemctl restart danted
sudo systemctl enable danted
```

#### 检查端口监听

```bash
sudo netstat -tunlp | grep 1080
```

---

## 使用 Squid 搭建 HTTP 代理

#### 安装 Squid

```bash
sudo apt update
sudo apt install squid
```

#### 配置 Squid（编辑配置文件）

```conf
http_port 3128

# 允许所有内网访问（根据实际情况调整）
acl localnet src 192.168.0.0/16
http_access allow localnet
http_access allow localhost
http_access deny all
```

#### 启动 Squid 服务

```bash
sudo systemctl restart squid
sudo systemctl enable squid
```

#### 检查端口监听

```bash
sudo netstat -tunlp | grep 3128
```

---

## 代理验证

- SOCKS5 代理可用 curl 测试：

```bash
curl -x socks5://adu:ADU908google@108.165.40.161:1080 http://ip-api.com/json
```

- HTTP 代理可用 curl 测试：

```bash
curl -x http://127.0.0.1:3128 http://ip-api.com/json
```

---

## 常见问题

- 防火墙未放行端口，需开放 1080（SOCKS5）或 3128（HTTP）端口。
- 配置文件语法错误，建议每次修改后检查日志。

---