---
title: Ubuntu 安装 Node.js 环境
date: 2024-06-10
tags:
  - Ubuntu
  - Node.js
  - npm
  - pnpm
categories:
  - 前端
---

## 1. 更新系统

```bash
sudo apt update
```

安装 NodeSource 源（以 Node.js 22.x 为例）
```bash
curl -fsSL https://deb.nodesource.com/setup_22.x | sudo -E bash -
sudo apt install -y nodejs
```

## 2. 验证安装

```bash
node -v
npm -v
```

## 3. 安装 pnpm

```bash
npm install -g pnpm
```
## 4. 验证 pnpm 安装

```bash
pnpm -v
```

## 5. 使用 nvm 管理Node.js 版本
```bash
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.40.3/install.sh | bash
source ~/.bashrc
nvm --version
```

安装 Node.js 指定版本
```bash
nvm install 22
nvm use 22
nvm alias default 22
```

验证 Node.js 最新版本
```bash
nvm install --lts
nvm use --lts
nvm alias default lts/*
node -v
```

nvm chakra 列出已安装的 Node.js 版本
```bash
nvm ls
```

nvm 卸载Node.js 版本
```bash
nvm uninstall 22
```

## 6. npm 升级到最新版本
```bash
npm install -g npm@latest
```

## 7. 使用 npm 安装 pnpm
```bash
npm install -g pnpm
```

升级到最新版本
```bash
npm install -g pnpm@latest
```

## 8. 参考文献
- [Node.js 官方文档](https://nodejs.org/en/docs/)