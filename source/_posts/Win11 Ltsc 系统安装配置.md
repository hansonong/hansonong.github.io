---
title: Win11 LTSC 系统安装配置
date: 2026-04-02
tags:
  - Windows
  - 系统配置
categories:
  - 工具教程
---

## 一、系统激活（KMS）

1. 右击开始图标，选择 **以管理员身份运行 PowerShell**。
2. 依次输入以下命令并回车执行：

```powershell
slmgr -ipk M7XTQ-FN8P6-TTKYV-9D4CC-J462D
slmgr -skms kms.0t.net.cn
slmgr -ato
```

---

## 二、重置 Microsoft Store

1. 按下 `Win + S`，搜索 **PowerShell**，点击右侧 **以管理员身份运行**。
2. 输入以下命令并回车执行：

```powershell
wsreset -i
```

---

## 三、恢复旧版右键菜单

Win11 默认右键菜单为简化版，恢复为经典完整菜单：

```powershell
reg add "HKCU\Software\Classes\CLSID\{86ca1aa0-34aa-4e8b-a509-50c905bae2a2}\InprocServer32" /f /ve
```

> 重启资源管理器或注销重登后生效：`taskkill /f /im explorer.exe & start explorer.exe`

---

## 四、关闭系统遥测与广告

以管理员身份运行 PowerShell，执行：

```powershell
# 关闭诊断数据收集
Set-ItemProperty -Path "HKLM:\SOFTWARE\Policies\Microsoft\Windows\DataCollection" -Name "AllowTelemetry" -Value 0 -Force

# 关闭个性化广告
Set-ItemProperty -Path "HKCU:\Software\Microsoft\Windows\CurrentVersion\AdvertisingInfo" -Name "Enabled" -Value 0 -Force
```

---

## 五、关闭自动更新（可选）

LTSC 版本更新频率较低，若需彻底关闭自动更新：

```powershell
# 禁用 Windows Update 服务
sc config wuauserv start= disabled
Stop-Service wuauserv
```

> ⚠️ 建议仅在测试/离线环境中使用，生产环境请保留安全更新。

---

## 六、开启剪贴板历史

```powershell
Set-ItemProperty -Path "HKCU:\Software\Microsoft\Clipboard" -Name "EnableClipboardHistory" -Value 1 -Force
```

或直接按 `Win + V` 按提示开启，之后按 `Win + V` 可查看剪贴板历史记录。

---

## 七、关闭休眠（节省磁盘空间）

```powershell
powercfg -h off
```

---

## 八、开启长路径支持

解除 Windows 260 字符路径限制，对开发环境友好：

```powershell
Set-ItemProperty -Path "HKLM:\SYSTEM\CurrentControlSet\Control\FileSystem" -Name "LongPathsEnabled" -Value 1 -Force
```

---

## 九、配置远程桌面（RDP）

```powershell
# 开启远程桌面
Set-ItemProperty -Path "HKLM:\System\CurrentControlSet\Control\Terminal Server" -Name "fDenyTSConnections" -Value 0

# 放行防火墙规则
Enable-NetFirewallRule -DisplayGroup "Remote Desktop"
```

---

## 十、安装包管理器 winget / Scoop

### winget（系统内置，确认可用）

```powershell
winget --version
```

### Scoop（推荐开发者使用）

```powershell
Set-ExecutionPolicy RemoteSigned -Scope CurrentUser
irm get.scoop.sh | iex
```

安装常用工具示例：

```powershell
scoop install git curl 7zip vim
```

---

## 十一、安装 WSL2（适合开发者）

```powershell
wsl --install
```

> 默认安装 Ubuntu，重启后完成初始化。可通过 `wsl --list --online` 查看可用发行版。

---

## 十二、开启 Hyper-V（虚拟化）

```powershell
Enable-WindowsOptionalFeature -Online -FeatureName Microsoft-Hyper-V-All -NoRestart
```

重启后生效。

---

## 十三、系统性能优化

```powershell
# 调整为"最佳性能"视觉效果
$path = "HKCU:\Software\Microsoft\Windows\CurrentVersion\Explorer\VisualEffects"
Set-ItemProperty -Path $path -Name "VisualFXSetting" -Value 2

# 设置高性能电源计划
powercfg -setactive 8c5e7fda-e8bf-4a96-9a85-a6e23a8c635c
```

---

## 十四、其他实用设置

| 功能 | 操作路径 |
|------|----------|
| 开启夜间模式 | 设置 → 系统 → 屏幕 → 夜间模式 |
| 设置时区 | 设置 → 时间和语言 → 日期和时间 |
| 关闭锁屏广告 | 设置 → 个性化 → 锁屏界面 → 关闭"在锁屏界面获取花絮、提示" |
| 开启存储感知 | 设置 → 系统 → 存储 → 存储感知 |
| 任务栏移至左侧 | 设置 → 个性化 → 任务栏 → 任务栏行为 → 对齐方式：靠左 |
