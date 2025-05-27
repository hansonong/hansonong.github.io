---
title: Linux服务器基础设置
tags:
  - Linux
  - Ubuntu
  - Debian
  - SSH
  - 防火墙
  - 端口管理
categories:
  - 运维部署
  - 服务器管理
---

> 设置端口允许IP访问，免密登录，端口占用查询。


<!-- more -->

### 设置端口允许IP访问
```shell
#允许IP 104.168.76.12 访问 9529 端口
firewall-cmd --permanent --add-rich-rule="rule family="ipv4" source address="104.168.76.12" port protocol="tcp" port="9529" accept"
#重新载入一下防火墙设置，使设置生效
firewall-cmd --reload
#查看已设置规则
firewall-cmd --zone=public --list-rich-rules
```

### 免密登录
##### 生成密钥

```shell
ssh-keygen -t rsa -C "topnoah@gmail.com"
```

##### 复制到Linux服务器

```shell
type id_rsa.pub | ssh root@远程服务器 "cat >> .ssh/authorized_keys"
```

<!-- more -->

##### 复制公钥到指定服务器
可以直接将本地公钥复制到指定服务器
```shell
ssh-copy-id root@vps-ubuntu
```

##### 端口占用查询

查看所有的服务端口（LISTEN，ESTABLISHED）
```shell
netstat -ap
```

查看指定端口，可以结合grep命令：
```shell
netstat -ap | grep 8080
```

也可以使用lsof命令：
```shell
lsof -i:8888
```

若要关闭使用这个端口的程序，使用kill + 对应的pid
```shell
kill -9 PID
```
