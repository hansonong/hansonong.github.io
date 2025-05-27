---
title: wget拉取网页源码
date: 2024-06-10
tags:
  - wget
  - 工具
  - 网络
categories:
  - 工具教程
---

在开发或运维过程中，经常需要获取网页的源代码。`wget` 是一个常用的命令行工具，可以方便地拉取网页源码。

<!-- more -->

## 基本用法

使用 `wget` 拉取网页源码的基本命令如下：

```bash
wget https://example.com
```

该命令会将网页源码保存为当前目录下的 `index.html` 文件。

## 保存为指定文件名

可以使用 `-O` 参数指定保存的文件名：

```bash
wget -O mypage.html https://example.com
```

## 拉取带有 User-Agent 的网页

有些网站会校验 User-Agent，可以通过 `--user-agent` 参数指定：

```bash
wget --user-agent="Mozilla/5.0" https://example.com
```

## 递归下载整个网站

如果需要下载整个网站，可以加上 `-r` 参数：

```bash
wget -r https://example.com
```

## 完整下载
```bash
wget --mirror --convert-links --adjust-extension --page-requisites --no-parent http://www.tengfenwujin.com/
```
- --mirror：开启镜像下载，相当于 -r -N -l inf --no-remove-listing，会递归下载整个网站。
- --convert-links：下载后转换页面中的链接，使本地浏览时链接有效。
- --adjust-extension：自动调整保存文件的扩展名（如 .html）。
- --page-requisites：下载显示网页所需的所有资源（如图片、CSS、JS）。
- --no-parent：不下载父目录的内容，只下载指定目录及其子目录。
- `http://www.tengfenwujin.com/`：目标网站地址。

该命令用于完整镜像一个网站到本地，保证本地浏览时页面和资源都能正常显示。

## 常用参数说明

- \`-O <file>\`：指定输出文件名
- \`-r\`：递归下载
- \`--user-agent=<agent>\`：指定 User-Agent
- \`-c\`：断点续传
- \`-q\`：安静模式，不输出信息



## 参考链接

- [GNU Wget 官方文档](https://www.gnu.org/software/wget/manual/wget.html)
```