---
title: ubuntu安装mysql
date: 2025-02-10 15:58:02
tags:
  - MySQL
  - Ubuntu
  - 数据库
  - 安装教程
categories:
  - 数据库
  - 运维部署
---

> 本文介绍如何在ubuntu上安装mysql

<!-- more -->


#### 1. 更新apt-get源
首先，确保你的系统包列表是最新的
```bash
sudo apt-get update
```

#### 2. 安装mysql
运行以下命令安装MySQL服务器
```bash
sudo apt-get install mysql-server
```

#### 3. 启动并启用MySQL服务
安装完成后，MySQL服务会自动启动。你可以通过以下命令检查其状态
```bash
sudo systemctl status mysql
```
如果MySQL没有自动启动，可以使用以下命令启动它
```bash
sudo systemctl start mysql
```
为了让MySQL在系统启动时自动运行，启用它
```bash
sudo systemctl enable mysql
```

#### 4. 运行安全脚本（推荐）
MySQL安装后，建议运行安全脚本来设置root密码并提高安全性
```bash
sudo mysql_secure_installation
```
1. 按照提示完成以下操作：
2. 设置root用户的密码。
3. 移除匿名用户。
4. 禁止远程root登录。
5. 删除测试数据库。
6. 重新加载权限表。

#### 5. 登录MySQL
安装完成后，可以使用以下命令登录MySQL
```bash
mysql -u root -p
```
输入你设置的root密码即可进入MySQL命令行

#### 6. 配置远程访问（可选）
如果需要从远程访问MySQL，需要修改MySQL配置文件并授权远程用户。

##### 6.1 修改配置文件
编辑MySQL配置文件：
```bash
sudo nano /etc/mysql/mysql.conf.d/mysqld.cnf
```
```sql
# 以下是mysqld.cnf文件的示例
[mysqld]
bind-address = 0.0.0.0
port = 3307
```
找到bind-address行，将其值从127.0.0.1改为0.0.0.0（允许所有IP访问）或指定允许访问的IP地址。
保存并退出，然后重启MySQL服务。要修改端口号，可以在配置文件中找到port行，将其值改为你想要的端口号。
```bash
sudo systemctl restart mysql
```
如果无法远程访问，执行如下命令
```bash
UPDATE mysql.user SET host='%' WHERE user='root';
FLUSH PRIVILEGES;
```
##### 6.2 授权远程用户
登录MySQL后，运行以下命令授权远程访问：
```sql
CREATE USER 'username'@'%' IDENTIFIED BY 'password';
GRANT ALL PRIVILEGES ON *.* TO 'username'@'%' WITH GRANT OPTION;
FLUSH PRIVILEGES;
```
将username和password替换为你想要的用户名和密码。

#### 7. 测试MySQL
安装完成后，可以通过以下命令测试MySQL是否正常运行：
```bash
mysql -u root -p -e "SHOW DATABASES;"
```
如果显示数据库列表，说明MySQL安装成功。

#### 8. 卸载MySQL（可选）
   如果需要卸载MySQL，可以运行以下命令：
```bash
sudo apt remove --purge mysql-server mysql-client mysql-common
sudo apt autoremove
sudo rm -rf /etc/mysql /var/lib/mysql
```
通过以上步骤，你应该可以在Ubuntu上成功安装并配置MySQL。如果有任何问题，请检查日志文件（/var/log/mysql/error.log）以获取更多信息。