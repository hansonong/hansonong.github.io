---
title: Linux Docker常见运维命令
tags:
  - Docker
  - Linux
  - 运维
  - 常用命令
categories:
  - 容器与虚拟化
  - 运维部署
---

> 本文总结了在 Linux 环境下，日常运维中最常用的 Docker 及 Docker Compose 相关管理与排错命令。

<!-- more -->

### 1. 服务管理

控制 Docker 守护进程的基础操作（以 systemd 为例）：

```shell
# 启动 Docker 服务
sudo systemctl start docker

# 停止 Docker 服务
sudo systemctl stop docker

# 重启 Docker 服务
sudo systemctl restart docker

# 设置开机自启
sudo systemctl enable docker

# 查看 Docker 服务状态
sudo systemctl status docker

# 查看 Docker 系统全局信息（包含镜像集、容器数、存储驱动等情况）
docker info
```

### 2. 镜像管理 (Image)

```shell
# 查看本地已下载的镜像列表
docker images
# 或者
docker image ls

# 拉取（下载）镜像，不加tag默认拉取latest版本
docker pull <镜像名>:<tag>
# 示例：docker pull nginx:1.24

# 删除指定本地镜像
docker rmi <镜像ID或镜像名>

# 强制删除镜像（即使有容器依赖）
docker rmi -f <镜像ID或镜像名>

# 清除所有虚悬镜像（<none>:<none> 未被使用的且没有名字的镜像）
docker image prune
```

### 3. 容器管理 (Container)

```shell
# 查看当前正在运行的容器
docker ps

# 查看所有容器（包括已停止的）
docker ps -a

# 启动、停止、重启容器
docker start <容器ID或名称>
docker stop <容器ID或名称>
docker restart <容器ID或名称>

# 强制停止（杀死）运行中的容器
docker kill <容器ID或名称>

# 删除已停止的指定容器
docker rm <容器ID或名称>

# 强制删除正在运行的容器
docker rm -f <容器ID或名称>

# 运行一个容器（以交互模式、后台运行并进行端口映射为例）
# -d 后台运行
# -p 宿主机端口:容器内端口
# --name 设定容器名
docker run -d -p 8080:80 --name mynginx nginx
```

### 4. 日志与排错 (Logs & Inspection)

```shell
# 查看容器运行日志
docker logs <容器ID或名称>

# 实时查看（跟踪）容器日志的最后 100 行
docker logs -f --tail 100 <容器ID或名称>

# 查看容器内的进程信息
docker top <容器ID或名称>

# 实时显示容器的资源占用情况（CPU、内存、网络I/O等）
docker stats <容器ID或名称>
# 不带参数默认显示所有正在运行的容器资源占用：docker stats

# 查看容器的详细配置和元数据（返回 JSON 格式数据）
docker inspect <容器ID或名称>

# 进入正在运行的容器内部执行命令（通常用于排查环境）
docker exec -it <容器ID或名称> /bin/bash
# 某些精简镜像如果没有 bash，可以使用 sh：docker exec -it <容器ID或名称> /bin/sh
```

### 5. 网络与数据卷管理 (Network & Volumes)

```shell
# 查看系统所有 Docker 网络
docker network ls

# 查看某个网络的详细情况
docker network inspect <网络名称>

# 查看系统所有数据卷
docker volume ls

# 删除未使用的数据卷
docker volume prune
```

### 6. 系统清理 (System Prune)

清理命令可以帮助释放服务器存储空间，非常实用。

```shell
# 清理所有停止的容器、未被任何容器使用的网络、未被打标签（dangling）的镜像
docker system prune

# 深度清理（包含系统上未被使用的所有的镜像、容器、网络等），并强制执行不提示
docker system prune -a -f

# 清理所有闲置数据卷（慎用，可能删掉你需要保留但暂时没挂载的持久化数据）
docker volume prune
```

### 7. Docker Compose 常用命令

如果你使用 `docker-compose.yml` 编排多容器服务，在配置文件的同级目录下运行：

```shell
# 构建并后台启动所有服务
docker-compose up -d

# 停止并移除容器、网络以及配置文件中声明的默认卷
docker-compose down

# 仅停止运行中的容器，不移除它们
docker-compose stop
# 对应启动
docker-compose start

# 重启项目中的所有服务
docker-compose restart

# 查看服务运行日志
docker-compose logs -f
```
