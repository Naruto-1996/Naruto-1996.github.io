---
title: linux(ubuntu) - 使用fail2ban 为nginx保驾护航
date: 2020-12-09 15:40:46
categories:
- Fail2ban
tags:
- Linux
- ubuntu
---

#### Linux(ubuntu) - 使用fail2ban 为nginx保驾护航

#### 1、安装

Fail2ban软件包包含在默认的Ubuntu 20.04存储库中。 要安装它，请以root或具有sudo特权的用户身份输入以下命令：

```
sudo apt update
sudo apt install fail2ban
```

安装完成后，Fail2ban服务将自动启动。 您可以通过检查服务状态来验证它：

```
sudo systemctl status fail2ban
```

输出将如下所示：

```
 fail2ban.service - Fail2Ban Service
     Loaded: loaded (/lib/systemd/system/fail2ban.service; enabled; vendor preset: enabled)
     Active: active (running) since Wed 2020-08-19 06:16:29 UTC; 27s ago
       Docs: man:fail2ban(1)
   Main PID: 1251 (f2b/server)
      Tasks: 5 (limit: 1079)
     Memory: 13.8M
     CGroup: /system.slice/fail2ban.service
             └─1251 /usr/bin/python3 /usr/bin/fail2ban-server -xf start
```

#### 2、Fail2ban配置

默认的`Fail2ban`安装带有两个配置文件， `/etc/fail2ban/jail.conf` 和 `/etc/fail2ban/jail.d/defaults-debian.conf`。 不建议修改这些文件，因为更新软件包时它们可能会被覆盖。

最好的做法是：

```
sudo cp /etc/fail2ban/jail.conf /etc/fail2ban/jail.local 
或者新建
sudo vim /etc/fail2ban/jail.local 
```

然后修改 jail.local的内容

```
[nginx-req-limit]

enabled = true
filter = nginx-req-limit
action = iptables-multiport[name=ReqLimit, port="http,https", protocol=tcp]
logpath = /var/log/nginx/access.log
findtime = 60
maxretry = 600
bantime = 600
```

新增文件：`/etc/fail2ban/filter.d/nginx-req-limit.conf`

```
# Fail2Ban configuration file
#
# supports: ngx_http_limit_req_module module

[Definition]

failregex =  -.*- .*HTTP/1.* .* .*$

# Option: ignoreregex
# Notes.: regex to ignore. If this regex matches, the line is ignored.
# Values: TEXT
#
ignoreregex =
```
#### 将IP地址列入白名单

您可以将要排除在外的IP地址，IP范围或主机添加到 `ignoreip` 指示。 在这里，您应该添加您的本地PC IP地址和所有其他要列入白名单的计算机。

`/etc/fail2ban/jail.local`

```
ignoreip = 127.0.0.1/8 ::1 123.123.123.123 192.168.1.0/24
```

* 每次编辑配置文件时，都需要重新启动Fail2ban服务以使更改生效：

```
sudo systemctl restart fail2ban
```

#### Fail2ban客户端 命令

* 检查监狱状态：

`sudo fail2ban-client status sshd`

* 解禁IP：

`sudo fail2ban-client set sshd unbanip 23.34.45.56`

* 禁止IP

`sudo fail2ban-client set sshd banip 23.34.45.56`

