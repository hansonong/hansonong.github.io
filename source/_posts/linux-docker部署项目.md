---
title: linux-docker部署项目
date: 2025-02-07 10:44:01
tags:
 - Linux
 - Docker
 - Mysql
 - Redis
 - RabbitMQ
 - Java
 - Maven
 - Nginx
 - minio
categories:
    - 服务器部署
---

Linux Docker部署项目，包括Docker安装、Mysql、Redis、RabbitMQ、Java、Maven、Nginx、minio等环境准备。
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
wget https://dlcdn.apache.org/maven/maven-3/3.9.9/binaries/apache-maven-3.9.9-bin.tar.gz
tar -zxvf apache-maven-3.9.9-bin.tar.gz
mv apache-maven-3.9.9 /opt/
```

### 安装 Nginx
```shell
sudo apt update
sudo apt install nginx
```

### 安装 minio
```shell
docker run -d --name minio --restart=always -p 9000:9000
```