---
title: ubuntu安装rabbitMQ
date: 2025-02-09 16:54:51
tags:
  - RabbitMQ
  - Ubuntu
  - 消息队列
  - 安装教程
categories:
  - 消息中间件
  - 运维部署
---

> ubuntu安装rabbitMQ

<!-- more -->

### 安装RabbitMQ

#### 更新软件包列表

首先，确保系统包列表是最新的

```shell
sudo apt update
```

#### 安装 Erlang（RabbitMQ 的依赖项）

RabbitMQ 是使用 Erlang 编写的，因此需要安装 Erlang

```shell
sudo apt install erlang
```

#### 添加 RabbitMQ 仓库

```shell
echo "deb https://dl.bintray.com/rabbitmq/debian bionic main" | sudo tee /etc/apt/sources.list.d/bintray.rabbitmq.list
```

添加 RabbitMQ 公钥

```shell
wget -O- https://www.rabbitmq.com/rabbitmq-release-signing-key.asc | sudo apt-key add -
```

再次更新软件包列表

```shell
sudo apt update
```

#### 安装 RabbitMQ

使用以下命令安装 RabbitMQ

```shell
sudo apt install rabbitmq-server
```

#### 启用并启动 RabbitMQ 服务

启用 RabbitMQ 服务并在系统启动时启动它

```shell
sudo systemctl enable rabbitmq-server
sudo systemctl start rabbitmq-server
```

#### 检查 RabbitMQ 的状态

```shell
sudo systemctl status rabbitmq-server
```

#### 启用RabbitMQ管理插件

RabbitMQ提供了一个Web管理界面，可以通过以下命令启用：

```shell
sudo rabbitmq-plugins enable rabbitmq_management
```

#### 设置 RabbitMQ 的管理用户

使用以下命令设置 RabbitMQ 的管理用户

```shell
sudo rabbitmqctl add_user admin password
```

设置用户标签

```shell
sudo rabbitmqctl set_user_tags admin administrator
```

设置权限

```shell
sudo rabbitmqctl set_permissions -p / admin ".*" ".*" ".*"
```

#### 修改 RabbitMQ 端口号

默认情况下，RabbitMQ 使用 5672 端口。如果需要更改端口号，请编辑配置文件

```shell
sudo nano /etc/rabbitmq/rabbitmq.conf
```

在文件中添加以下行，将端口号更改为 5673

```shell
# 监听端口
listeners.tcp.port = 5673
# 管理界面端口
management.tcp.port = 15673
```

通过以上步骤，您将在 Ubuntu 系统上安装并运行 RabbitMQ。