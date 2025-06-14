---
title: linux-docker部署项目
date: 2025-02-07 10:44:01
tags:
  - Linux
  - Docker
  - MySQL
  - Redis
  - RabbitMQ
  - Java
  - Maven
  - Nginx
  - MinIO
  - 环境搭建
  - 部署教程
categories:
  - 运维部署
  - 容器与虚拟化
---

> Linux Docker部署项目，包括Docker安装、Mysql、Redis、RabbitMQ、Java、Maven、Nginx、minio等环境准备。
<!-- more -->


### 安装 Docker
```shell
sudo apt update
sudo apt install apt-transport-https ca-certificates curl software-properties-common
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg
echo "deb [arch=amd64 signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
sudo apt update
sudo apt install docker-ce docker-ce-cli containerd.io
```

### 安装docker-compose
```shell
sudo curl -L "https://github.com/docker/compose/releases/latest/download/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
sudo chmod +x /usr/local/bin/docker-compose
docker-compose --version
```

### 安装 Docker Mysql
```shell
docker run -d --name mysql --restart=always -p 3306:3306 -e MYSQL_ROOT_PASSWORD=poiu0987  mysql:latest
```

### 安装 Docker Redis
```shell
docker run -d  --name redis --restart=always -p 6379:6379 redis --requirepass "poiu0987"
```

### 安装 Docker RabbitMQ
```shell
docker run -d --name rabbitmq --restart=always -p 15672:15672 -p 5672:5672 rabbitmq:latest
```
安装好rabbitmq后，启用延时插件
- 下载插件
```shell
curl -LO https://github.com/rabbitmq/rabbitmq-delayed-message-exchange/releases/download/v4.1.0/rabbitmq_delayed_message_exchange-4.1.0.ez
```
- 拷贝插件到RabbitMQ的插件目录
```shell
docker cp rabbitmq_delayed_message_exchange-4.1.0.ez rabbitmq:/opt/rabbitmq/plugins/
```
- 查看插件列表
```shell
rabbitmq-plugins list
```
- 启用插件
```shell
rabbitmq-plugins enable rabbitmq_delayed_message_exchange
```

### 安装 Java
```shell
sudo apt update
sudo add-apt-repository ppa:openjdk-r/ppa
sudo apt update
sudo apt-get install openjdk-21-jre
sudo apt-get install openjdk-21-jdk
java --version
```

### 安装 Maven
```shell
wget https://dlcdn.apache.org/maven/maven-3/3.9.10/binaries/apache-maven-3.9.10-bin.tar.gz
tar -zxvf apache-maven-3.9.10-bin.tar.gz
mv apache-maven-3.9.10 /opt/
```

### 安装 Nginx
```shell
sudo apt update
sudo apt install nginx
```