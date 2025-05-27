---
title: Ubuntu安装Docker和docker-compose
tags:
  - Docker
  - Docker Compose
  - Ubuntu
  - 容器化
  - 安装教程
categories:
  - 容器与虚拟化
  - 运维部署
---

> 在 Ubuntu 上安装 Docker 和 Docker Compose，卸载Docker 和 Docker Compose。

<!-- more -->

### 安装 Docker

1. 更新系统软件包列表：

```shell
sudo apt update
```

2. 安装 Docker 依赖包：

```shell
sudo apt install apt-transport-https ca-certificates curl software-properties-common
```

3. 添加 Docker 的官方 GPG 密钥：

```shell
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg
```

4. 添加 Docker 的软件源：

- 对于 x86_64/amd64 架构的系统：

```shell
echo "deb [arch=amd64 signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
```

- 对于 ARM64 架构的系统（如树莓派）：

```shell
echo "deb [arch=arm64 signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
```

5. 安装 Docker：

```shell
sudo apt update
sudo apt install docker-ce docker-ce-cli containerd.io
```

6. 验证 Docker 是否成功安装：

```shell
sudo docker run hello-world
```

这将下载并运行一个简单的 Docker 镜像，并输出 "Hello from Docker!"。



### 卸载Docker

要完全卸载Ubuntu上的Docker，执行以下步骤：

1. 停止所有正在运行的Docker容器。可以使用以下命令停止所有容器：
   ```shell
   sudo docker stop $(sudo docker ps -a -q)
   ```

2. 删除所有Docker容器。运行以下命令删除所有容器：
   ```shell
   sudo docker rm $(sudo docker ps -a -q)
   ```

3. 停止Docker服务。使用以下命令停止Docker服务：
   ```shell
   sudo systemctl stop docker
   ```

4. 卸载Docker软件包。运行以下命令卸载Docker软件包：
   ```shell
   sudo apt-get purge docker-ce docker-ce-cli containerd.io
   ```

5. 删除Docker相关的配置和数据。使用以下命令删除Docker的配置和数据：
   ```shell
   sudo rm -rf /var/lib/docker
   ```

6. 删除Docker用户组。运行以下命令删除Docker用户组：
   ```shell
   sudo groupdel docker
   ```

完成以上步骤后，您的系统将不再具有Docker相关的组件和配置。请注意，这将删除所有Docker容器、镜像和数据，因此请确保您已备份重要的数据。



### 安装 Docker Compose

1. 下载 Docker Compose 的可执行文件：

```shell
sudo curl -L "https://github.com/docker/compose/releases/latest/download/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
```

2. 添加执行权限：

```shell
sudo chmod +x /usr/local/bin/docker-compose
```

3. 验证 Docker Compose 是否成功安装：

```shell
docker-compose --version
```

这将显示 Docker Compose 的版本信息。



### 卸载 Docker Compose

1. 直接删除 Docker Compose 的可执行文件：

```shell
sudo rm -rf /usr/local/bin/docker-compose
```


这就卸载了 Docker Compose