---
title: Ubuntu/Debian 服务器安装 Docker 基础环境（脚本化）
categories:
  - 容器与虚拟化
  - 运维部署
tags:
  - Ubuntu
  - Debian
  - Docker
  - Docker Compose
  - Shell
---

> 这篇文档将原始安装脚本整理为可执行的服务器部署手册，用于在 Debian/Ubuntu 上快速安装 Docker Engine 与 Docker Compose 插件。

<!-- more -->

## 1. 适用范围

- 系统：Debian / Ubuntu
- 场景：新机器初始化、容器宿主机准备
- 权限：需要 `root`（或 `sudo`）

## 2. 前置检查

执行安装前，建议先确认以下信息：

```shell
cat /etc/os-release
uname -m
df -h
```

说明：

- `os-release`：确认是否 Debian/Ubuntu
- `uname -m`：确认架构（如 `amd64` / `arm64`）
- `df -h`：确认磁盘空间是否充足

## 3. 一键安装脚本

将以下内容保存为 `bootstrap-docker.sh`：

```bash
#!/bin/bash

set -e

log_info() {
  echo -e "\033[32m[信息]\033[0m $1"
}

log_error() {
  echo -e "\033[31m[错误]\033[0m $1"
  exit 1
}

# 检查是否以 root 用户运行
if [ "$(id -u)" -ne 0 ]; then
  log_error "此脚本需要以 root 权限运行。请使用 'sudo ./bootstrap-docker.sh'"
fi

log_info "--- 开始准备 Docker 运行环境 ---"

# 步骤 0: 检查操作系统
if [ -f /etc/os-release ]; then
  . /etc/os-release
  if [[ "$ID" != "debian" && "$ID" != "ubuntu" ]]; then
    log_error "此脚本目前仅为 Debian/Ubuntu 设计。检测到操作系统为: $ID"
  fi
fi

# 步骤 1: 安装必要工具
log_info "正在更新 apt 包索引并安装基础工具..."
apt-get update
apt-get install -y ca-certificates curl gnupg lsb-release

# 步骤 2: 安装 Docker Engine
if command -v docker >/dev/null 2>&1; then
  log_info "Docker 已安装，跳过安装步骤。"
else
  log_info "正在安装 Docker Engine (官方推荐方式)..."

  keyrings_dir="/usr/share/keyrings"
  install -m 0755 -d "$keyrings_dir"
  curl -fsSL "https://download.docker.com/linux/${ID}/gpg" | gpg --dearmor -o "${keyrings_dir}/docker-archive-keyring.gpg"
  chmod a+r "${keyrings_dir}/docker-archive-keyring.gpg"

  echo \
    "deb [arch=$(dpkg --print-architecture) signed-by=${keyrings_dir}/docker-archive-keyring.gpg] https://download.docker.com/linux/${ID} \
    $(lsb_release -cs) stable" | \
    tee /etc/apt/sources.list.d/docker.list > /dev/null

  log_info "正在更新包索引并安装 Docker..."
  apt-get update
  apt-get install -y docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin

  log_info "Docker 安装成功。"
fi

# 步骤 3: 验证 Docker Compose
if ! docker compose version >/dev/null 2>&1; then
  log_error "Docker Compose 插件安装失败或未找到。"
else
  log_info "Docker Compose 插件已成功安装并验证。"
fi

log_info "================ 环境准备完成 ================"
log_info "Docker 宿主环境已就绪。"
exit 0
```

## 4. 执行方式

```shell
chmod +x bootstrap-docker.sh
sudo ./bootstrap-docker.sh
```

## 5. 安装完成后验证

```shell
docker --version
docker compose version
systemctl status docker --no-pager
```

可选功能验证：

```shell
docker run --rm hello-world
```

## 6. 脚本做了什么

- 自动检查是否 `root` 执行，避免权限不足
- 自动识别 Debian/Ubuntu，非目标系统直接退出
- 安装基础依赖：`ca-certificates`、`curl`、`gnupg`、`lsb-release`
- 添加 Docker 官方 GPG key 与 APT 源
- 安装组件：Docker Engine、CLI、containerd、buildx、compose 插件
- 安装后检查 `docker compose version`，失败立即提示

## 7. 常见问题排查

### 7.1 `Permission denied` 或 `Got permission denied while trying to connect to the Docker daemon`

先确认是否使用 `sudo` 执行，或把当前用户加入 `docker` 组：

```shell
sudo usermod -aG docker $USER
newgrp docker
```

### 7.2 `Could not resolve` / 下载超时

说明网络或 DNS 异常，先检查连通性：

```shell
curl -I https://download.docker.com
```

### 7.3 `Package has no installation candidate`

通常是源未正确写入或 `apt-get update` 未成功，建议重新执行：

```shell
sudo apt-get update
sudo apt-cache policy docker-ce
```

## 8. 上线前检查清单

- [ ] 系统版本确认是 Debian/Ubuntu
- [ ] Docker 与 Docker Compose 版本正常输出
- [ ] `docker run hello-world` 通过
- [ ] 防火墙与安全组策略满足业务端口要求
- [ ] 镜像仓库访问策略（公网/代理）确认完成
