---
title: OpenWrt 安装与配置 Passwall2 教程
tags:
  - OpenWrt
  - Passwall
  - LuCI
  - 代理
  - GFW
  - 软路由
categories:
  - 运维部署
  - 网络配置
date: 2023-10-27 10:00:00
---

> 本文提供一份在 OpenWrt 系统上安装和配置 Passwall2 插件的详细指南。Passwall2 是一款功能强大的网络代理客户端，可以帮助您轻松管理和转发网络流量。

<!-- more -->

## 简介

### 什么是 Passwall2?

Passwall2 是 OpenWrt 平台上一个广受欢迎的 LuCI 插件，它集成了多种代理协议（如 Trojan, VLESS, VMess, Shadowsocks 等），并提供了一个用户友好的图形界面来管理网络流量。通过它，您可以实现对路由器下所有设备进行透明代理，而无需在每个设备上单独配置。

### 准备工作

在开始之前，请确保您已准备好以下几项：

1.  **一台已刷入 OpenWrt 的路由器**：并确保其已连接到互联网。
2.  **SSH 访问权限**：您需要能够通过 SSH 登录到您的 OpenWrt 路由器。
3.  **一个可用的代理服务节点**：您需要擁有代理服務器的訂閱連結或詳細配置資訊。

## 安装 Passwall2

安装 Passwall2 主要有两种方法：手动上传 `.ipk` 文件进行安装（最可靠）或通过添加软件源自动安装。

### 方法一：手动上传 IPK 文件安装 (最推荐)

此方法最为可靠，可以避免因软件源失效或版本不兼容导致的问题，也是解决安装错误的最终手段。

#### 第 1 步：确定路由器 CPU 架构

这是最关键的一步，装错架构的软件包将无法运行。

1.  登录 OpenWrt 管理界面 (LuCI)。
2.  在 **「状态」->「总览」** 页面，找到 **「系统」** 信息卡。
3.  记下 **「架构」** (Architecture) 这一行的信息。常见的有 `x86_64`, `aarch64_cortex-a53`, `aarch64_cortex-a72` 等。

#### 第 2 步：下载 Passwall2 相关文件

你需要从 Passwall2 的开发者 GitHub 发布页下载所有必需的文件。

1.  **访问项目发布页**：[xiaorouji/openwrt-passwall2 Releases](https://github.com/xiaorouji/openwrt-passwall2/releases)
2.  **下载核心文件**：在最新版本中，找到并下载以下 **3 个关键文件**：
    * **LuCI 界面程序**: `luci-app-passwall2_*.ipk`
    * **中文语言包**: `luci-i18n-passwall2-zh-cn_*.ipk`
    * **核心依赖包**: 根据你的 CPU 架构，下载对应的 `passwall_packages_ipk_*.zip` 文件。例如，`x86_64` 架构就下载 `passwall_packages_ipk_x86_64.zip`。**如果找不到完全匹配的，请选择 `generic` (通用) 版本**，例如 `passwall_packages_ipk_aarch64_generic.zip`。
3.  **解压依赖包**：将下载的 `passwall_packages_ipk_*.zip` 文件解压缩，你会得到一个包含大量 `.ipk` 文件的文件夹。

#### 第 3 步：上传并安装

1.  登录 OpenWrt 管理界面，进入 **「系统」->「文件传输」**。
2.  将上一步下载和解压出的**所有 `.ipk` 文件**（包括界面、语言包和所有依赖文件）上传到路由器的 `/tmp` 目录下。
3.  通过 SSH 登录到 OpenWrt，执行以下命令进行安装：
    ```bash
    opkg install /tmp/*.ipk
    ```
4.  安装完成后，刷新 LuCI 页面，即可在 **「服务」** 菜单下找到 Passwall2。

---

### 方法二：添加软件源安装 (备选方案)

此方法较为便捷，但可能因源的维护问题而失效。

1.  **SSH 登录到 OpenWrt**
    打开终端（在 Windows 上可以是 PowerShell 或 PuTTY），输入以下命令登录到您的路由器。默认 IP 通常是 `192.168.1.1`，请根据您的实际情况修改。
    ```bash
    ssh root@192.168.1.1
    ```

2.  **编辑 OPKG 配置文件**
    执行以下命令，编辑自定义软件源配置文件：
    ```bash
    vi /etc/opkg/customfeeds.conf
    ```
    按 `i` 键进入编辑模式，添加以下软件源（注意：此源可能失效，请自行寻找可用源）：
    ```
    src/gz passwall_packages [https://github.com/xiaorouji/openwrt-passwall-packages/releases/download/1.23-5](https://github.com/xiaorouji/openwrt-passwall-packages/releases/download/1.23-5)
    src/gz passwall_luci [https://github.com/xiaorouji/openwrt-passwall/releases/download/v4.73-1](https://github.com/xiaorouji/openwrt-passwall/releases/download/v4.73-1)
    ```
    添加后，按 `Esc` 键，然后输入 `:wq` 保存并退出。

3.  **更新软件列表并安装**
    执行以下命令来更新软件列表并安装 Passwall2 及其依赖。
    ```bash
    opkg update
    opkg install luci-app-passwall2
    ```

## 配置 Passwall2

安装成功后，在 LuCI 界面的 **「服务」->「Passwall2」** 中进行配置。

1.  **添加节点订阅**
    * 点击 **「节点订阅」** 标签页。
    * 勾选 **「启用」**。
    * 在 **「订阅列表」** 中，点击「添加」，填入你的订阅链接，设置备注，然后点击「保存」。
    * 回到页面顶部，点击 **「保存并应用」**。
    * 稍等片刻，然后点击 **「手动订阅」** 按钮，系统会自动拉取节点信息。成功后，节点会出现在下方的「节点列表」中。

2.  **基础设置**
    * 切换到 **「基本设置」** 标签页。
    * **主开关**: 勾选 **「启用」** 来开启 Passwall2 服务。
    * **TCP 节点 / UDP 节点**: 从下拉菜单中选择一个你想要使用的节点。UDP 节点通常用于游戏或视频通话，如果不需要可以设为「禁用」。
    * **代理模式**:
        * **GFWList 模式**: 推荐模式，仅被 GFW 屏蔽的网站流量会通过代理。
        * **全局代理**: 所有流量都通过代理，可能会影响国内网站的访问速度。
    * **DNS**: 建议设置为防污染的 DNS，例如 `8.8.8.8` 或使用加密的 `DOH` (DNS over HTTPS)。

3.  **保存配置**
    完成所有设置后，点击页面底部的 **「保存并应用」** 按钮。等待片刻，Passwall2 就会开始工作。

## 常见问题 (Troubleshooting)

1.  **安装时提示 `opkg_install_cmd: Cannot install package...` 或错误码 255？**
    * **原因**: 绝大多数情况是 **依赖缺失** 或 **架构不匹配**。
    * **解决方案**: 请严格按照 **「方法一：手动上传 IPK 文件安装」** 的步骤操作。确保将 `passwall_packages_ipk_*.zip` 解压后的 **所有** 依赖文件都上传到 `/tmp` 目录再执行安装命令。

2.  **Passwall2 已启用，但无法访问外网？**
    * **检查节点**: 在「节点列表」中，点击节点右侧的「在线测试」(小地球图标)，看是否能正常连接。
    * **检查路由器时间**: SSH 登录后输入 `date` 命令，检查系统时间是否准确。不正确的时间会导致证书验证失败。
    * **检查 DNS**: 确保 DNS 设置正确，避免 DNS 污染。

3.  **某些国内网站打不开或速度很慢？**
    * **原因**: 可能该网站的服务器在海外，被代理规则错误地匹配了。
    * **解决方案**: 在 **「访问控制」** 标签页中，将该网站的域名添加到 **「强制走 WAN 口的域名」** 列表中，使其直连。

希望这份详细的指南能帮助您顺利完成 Passwall2 的安装与配置！
