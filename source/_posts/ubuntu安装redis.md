---
title: ubuntu安装redis
date: 2025-02-09 12:43:25
tags:
  - Redis
  - Ubuntu
  - 数据库
  - 安装教程
categories:
  - 数据库
  - 运维部署
---

> ubuntu安装redis

<!-- more -->

#### 安装redis


#### 1. 更新系统包列表
首先，确保系统包列表是最新的：
```shell
sudo apt update
```

#### 2. 安装Redis
使用以下命令安装Redis：
```shell
sudo apt install redis-server
```

#### 3. 启动Redis服务
安装完成后，Redis服务会自动启动。可以使用以下命令来检查Redis服务的状态：
```shell
sudo systemctl status redis-server
```
如果服务没有自动启动，可以手动启动它：
```shell
sudo systemctl start redis-server
```
#### 4. 设置Redis开机自启
为了确保Redis在系统启动时自动启动，可以启用它的自启动：
```shell
sudo systemctl enable redis-server
```

#### 5. 测试Redis
可以使用Redis自带的命令行工具redis-cli来测试Redis是否正常工作：
```shell
redis-cli
```
在Redis命令行中，输入ping，如果返回PONG，说明Redis工作正常：
```shell
127.0.0.1:6379> ping
PONG
```
PONG
#### 6. 配置Redis（可选）
Redis的配置文件位于/etc/redis/redis.conf。可以根据需要编辑此文件来更改Redis的配置。例如，你可以更改绑定的IP地址、端口号、设置密码等。

编辑配置文件：
```shell
sudo nano /etc/redis/redis.conf
```
- 默认情况下，Redis只允许本地访问。如果需要从远程访问Redis，可以编辑配置文件/etc/redis/redis.conf
  找到bind，将其注释掉：
    ```apache
    # bind 127.0.0.1
    ```

- 关闭保护，找到protected-mode，将其改为no：
    ```apache
    protected-mode no
    ```

- 修改端口，找到port，将其改为你想要的端口号：
    ```apache
    port 6379
    ```
- 设置密码，找到requirepass，将其改为你想要的密码：
    ```apache
    requirepass yourpassword
    ```

修改后，重启Redis服务以使更改生效：
```shell
sudo systemctl restart redis-server
```

#### 7. 防火墙配置（可选）
如果服务器启用了防火墙，你需要允许Redis的端口（默认是6379）通过防火墙：
```shell
sudo ufw allow 6379/tcp
```


#### 8. 卸载Redis（可选）
如果不再需要Redis，可以使用以下命令卸载它：
```shell
sudo apt remove --purge redis-server
```
这将删除Redis及其配置文件。

