---
title: Ubuntu/Debian 系统登录 GitHub GHCR
categories:
  - 容器与虚拟化
  - 运维部署
tags:
  - Ubuntu
  - Debian
  - Docker
  - GHCR
  - GitHub
---

> 记录在 Ubuntu / Debian 系统中登录 GitHub Container Registry（GHCR）的完整流程，包含拉取、推送与常见报错排查。

<!-- more -->

## 1. 前置条件

在开始前，请确认：

- 已安装 Docker（`docker --version` 可正常输出）
- 有 GitHub 账号
- 已创建可用于 GHCR 的 Personal Access Token（PAT）

可先检查环境：

```shell
docker --version
whoami
```

## 2. 创建 GitHub PAT（关键）

GHCR 推荐使用 **PAT** 登录，而不是账号密码。

1. 打开 GitHub：`Settings` -> `Developer settings` -> `Personal access tokens`
2. 建议创建 **Fine-grained token**（或使用 classic token）
3. 至少授予以下权限（按需选择）：
   - 拉取镜像：`read:packages`
   - 推送镜像：`write:packages`
   - 删除镜像：`delete:packages`（可选）
4. 复制并妥善保存 Token（页面关闭后通常无法再次完整查看）

## 3. 登录 GHCR

建议使用 `--password-stdin`，避免 Token 出现在命令历史中。

```shell
echo "YOUR_GITHUB_TOKEN" | docker login ghcr.io -u YOUR_GITHUB_USERNAME --password-stdin
```

示例（将用户名和 token 替换为你自己的）：

```shell
echo "ghp_xxxxxxxxxxxxxxxxxxxx" | docker login ghcr.io -u hansonong --password-stdin
```

登录成功会输出：`Login Succeeded`。

## 4. 验证是否可拉取 GHCR 镜像

```shell
docker pull ghcr.io/OWNER/IMAGE:TAG
```

例如：

```shell
docker pull ghcr.io/octocat/hello-world:latest
```

如果是私有镜像，请确保当前 Token 对该仓库有访问权限。

## 5. 推送镜像到 GHCR（可选）

### 5.1 给本地镜像打 GHCR 标签

```shell
docker tag local-image:latest ghcr.io/YOUR_GITHUB_USERNAME/local-image:latest
```

### 5.2 推送

```shell
docker push ghcr.io/YOUR_GITHUB_USERNAME/local-image:latest
```

> 提示：部分仓库可能要求镜像命名与仓库策略一致，推送失败时先检查仓库权限与命名规则。

## 6. 退出登录与清理凭据

```shell
docker logout ghcr.io
```

如需进一步清理本机凭据，可检查 Docker 配置文件：

```shell
cat ~/.docker/config.json
```

## 7. 常见报错与排查

### 7.1 `unauthorized` / `authentication required`

常见原因：

- 用户名写错（必须是 GitHub 用户名，不是邮箱）
- Token 无效、过期或复制不完整
- 使用了账号密码而非 PAT

建议处理：重新生成 Token，并用 `--password-stdin` 重新登录。

### 7.2 `denied: permission denied` 或 `403 Forbidden`

常见原因：

- Token 权限不够（缺少 `write:packages` 或 `read:packages`）
- 对目标组织/仓库无访问权限

建议处理：补齐权限并确认仓库可见性（public/private）与组织策略。

### 7.3 网络超时 / TLS 相关错误

常见原因：

- DNS 或网络代理配置异常
- 服务器时间不正确导致 TLS 校验失败

建议处理：

```shell
curl -I https://ghcr.io
```

若请求失败，先排查网络连通性、代理配置和系统时间。

## 8. 安全建议

- Token 遵循最小权限原则，只给必要 scope
- 不要把 Token 写入脚本明文或提交到 Git 仓库
- 定期轮换 Token
- 在共享主机上使用后及时 `docker logout ghcr.io`

