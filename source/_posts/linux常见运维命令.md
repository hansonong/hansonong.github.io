---
title: Linux常见运维命令
tags:
  - Linux
  - 运维
  - 常用命令
categories:
  - 运维部署
  - Linux
---

> 本文总结了 Linux 环境下，日常运维中最常用的命令，涵盖了系统状态、文件管理、网络排查、进程管理以及服务权限等方面内容。

<!-- more -->

### 1. 系统状态与性能查看

排查服务器性能和运行情况时最常用的指令：

```shell
# 查看系统整体运行状态（CPU负载、内存、进程信息等）
top 
# 退出 top 按 'q'

# 查看内存使用情况（-h 表示以较为易读的 M/G 格式显示）
free -h

# 查看磁盘空间挂载及使用情况
df -h

# 统计目录或文件占用的磁盘空间大小（-s 汇总，-h 易读格式）
# 例如查看当前目录总大小
du -sh *

# 查看系统内核版本、主机名等信息
uname -a

# 查看 Linux 发行版信息
cat /etc/os-release
```

### 2. 文件与目录操作

```shell
# 查看当前所在路径
pwd

# 列表展示目录内容（-l 详细信息，-a 包含隐藏文件，-h 文件大小易读）
ls -lah

# 创建单层或多层目录
mkdir -p /path/to/directory

# 复制文件或目录（-r 递归复制目录）
cp -r /source/path /destination/path

# 移动文件或重命名
mv /old/name /new/name

# 删除文件或目录（-r 递归，-f 强制不提示，注意慎用 rm -rf）
rm -rf /path/to/remove

# 查找文件（按名称）
find / -name "filename.txt"

# 压缩和解压缩 tar.gz 文件
# 打包并压缩
tar -czvf archive.tar.gz /path/to/dir
# 解压文件
tar -xzvf archive.tar.gz
```

### 3. 文件查看与内容检索

```shell
# 查看所有文件内容（适合小文件）
cat filename.txt

# 分页查看文件内容（适合大文件）
less filename.txt

# 查看文件尾部的 100 行（常用于查看最新日志）
tail -n 100 filename.txt

# 实时监控文件尾部的新增内容
tail -f filename.log

# 在文件中检索关键字（-i 忽略大小写，-n 显示行号）
grep -in "keyword" filename.txt

# 配合管道符检索进程或动态数据
# 例如在所有进程中搜索 nginx
ps -ef | grep nginx
```

### 4. 进程与服务管理

```shell
# 查看所有正在运行的系统进程详细信息
ps -ef 
# 或
ps aux

# 杀死指定 PID 的进程
kill <PID>

# 强制杀死进程
kill -9 <PID>

# 后台运行命令，且退出终端不中断
nohup command &

# systemd 服务管理（以 nginx 为例）
systemctl start nginx    # 启动
systemctl stop nginx     # 停止
systemctl restart nginx  # 重启
systemctl status nginx   # 查看状态
systemctl enable nginx   # 设置开机自启
systemctl disable nginx  # 取消开机自启
```

### 5. 网络连接与排查

```shell
# 查看网络接口配置和 IP 地址
ip a
# 旧版系统可能使用 ifconfig

# 检查网络连通性
ping google.com

# 检查本地端口占用及网络连接状态（常用组合：-tuln 查监听端口）
ss -tuln
# 或使用 netstat
netstat -tulnp

# 查看指定端口被哪个进程占用（极其常用）
lsof -i :8080
# 或结合 netstat / ss 进行过滤查看
netstat -tunlp | grep 8080

# 测试目标主机的指定端口是否开放
telnet <IP> <Port>

# 命令行模拟 HTTP 请求（测试 Web 接口）
curl -I http://localhost
```

### 6. 用户与权限管理

```shell
# 添加新用户
sudo useradd username

# 修改用户密码
sudo passwd username

# 更改文件或目录的拥有者（Owner:Group）
sudo chown -R user:group /path/to/directory

# 更改文件或目录权限
# 777 为最高权限（慎用），一般文件 644，目录 755
sudo chmod 755 /path/to/file_or_dir

# 切换用户
su - username
```

### 7. 系统日志查看

排查问题首选的参考凭据：

```shell
# 查看系统内核及硬件日志
dmesg

# 跟踪查看系统消息日志（Debian/Ubuntu）
tail -f /var/log/syslog

# 跟踪查看系统消息日志（CentOS/RHEL）
tail -f /var/log/messages

# 使用 journalctl 查看所有 systemd 服务的日志
journalctl -xe

# 查阅特定服务的日志记录（例：sshd）
journalctl -u sshd
```

### 8. 文件传输与下载

```shell
# 远程复制文件到本地（若要上传则对调本地与远程路径）
scp user@remote_IP:/remote/path/file.txt /local/path/

# 递归传输整个目录
scp -r /local/path/dir user@remote_IP:/remote/path/

# 增量同步文件/目录（非常适合备份；-a 归档，-v 详细，-z 压缩传输）
rsync -avz /local/path/ user@remote_IP:/remote/path/

# 在终端下载网络资源
wget https://example.com/file.zip

# 支持断点续传的下载
wget -c https://example.com/file.zip
```

### 9. 计划任务 (Crontab)

```shell
# 编辑当前用户的定时任务
crontab -e

# 列出当前用户的定时任务
crontab -l

# 【补充】crontab 书写规范示例（分 时 日 月 周 命令）：
# 每天凌晨 2 点执行脚本
# 0 2 * * * /bin/bash /path/to/script.sh >> /path/to/log.log 2>&1
```

### 10. 防火墙基础管理

```shell
# --- Ubuntu/Debian 系 (UFW) ---
sudo ufw status                # 查看防火墙状态及规则
sudo ufw enable                # 启用防火墙
sudo ufw allow 80/tcp          # 放行 80 端口 (TCP 协议)
sudo ufw delete allow 80/tcp   # 删除放行规则

# --- CentOS/RHEL 系 (firewalld) ---
sudo firewall-cmd --state      # 查看运行状态
# 永久放行 8080 端口
sudo firewall-cmd --zone=public --add-port=8080/tcp --permanent 
sudo firewall-cmd --reload     # 重载防火墙使配置生效
```