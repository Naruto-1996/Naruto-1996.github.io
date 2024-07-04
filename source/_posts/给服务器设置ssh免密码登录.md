---
title: 给服务器设置ssh免密码登录
date: 2020-12-09 09:25:02
categories:
- Linux
tags:
- SSH
- Linux
---


#### 给服务器设置ssh免密码登录

#### SSH是什么？

> SSH 为 Secure Shell 的缩写，由IETF的网络工作小组（Network Working Group）所制定；SSH为建立在应用层和传输层基础上的安全协议。
SSH是目前较可靠，专为远程登录会话和其他网络服务提供安全性的协议。利用SSH协议可以有效防止远程管理过程中的信息泄露问题。
SSH最初是UNIX系统上的一个程序，后来又迅速扩展到其他操作平台。SSH在正确使用时可弥补网络中的漏洞。SSH客户端适用于多种平台。
几乎所有UNIX平台—包括HP-UX、Linux、AIX、Solaris、Digital UNIX、Irix，以及其他平台，都可运行SSH。

* 简单来理解，就是我拥有一台服务器，我现在想要登录上去做一些事情，那就得使用ssh登录到远程的服务器上，才能在上面进行操作。

#### SSH 服务端以及客户端配置

##### 启动 sshd 服务

>一开始在远程服务器上面，需要查看一下他的sshd服务启动了没有，如果没有启动，任何客户端主机是连接不上来的，一般如果是自己在云厂商处购买了主机，主机启动的时候就会把sshd服务启动起来。但有可能自己在测试环境搭建机器的时候，是没有默认启动的，这时候就需要在测试机器的终端看一下，命令如下

```
ubuntu@VM-0-16-ubuntu:~$ ps -ef | grep sshd
root      1221     1  0 Dec07 ?        00:00:00 /usr/sbin/sshd -D
root     15649  1221  0 09:21 ?        00:00:00 sshd: ubuntu [priv]
ubuntu   15740 15649  0 09:21 ?        00:00:00 sshd: ubuntu@pts/0
ubuntu   24863 15741  0 10:24 pts/0    00:00:00 grep --color=auto sshd
```

>这里看到第一行,sshd已经启动起来了,进程号是 1221

* 如果没有启动的话,那就启动一下,命令如下

```
ubuntu@VM-0-16-ubuntu:~$ service sshd start
```

#### 登录服务器主机的方式

#### 1、客户端使用密码的方式登录目标主机

```
➜  ~ ssh root@192.168.0.187
root@192.168.0.187's password:
```
输入密码即可

#### 2、客户端使用密钥方式登录目标主机(免密码)

>使用场景：
>
>1、如果某个运维人员临时需要登录一台机器，但是机器的管理员并不想把密码暴露给他，所以会让这个运维人员发一个自己的公钥给自己，帮他添加进去，这个运维人员就可以顺利的登录机器了。在运维做完了自己的事情之后，机器的管理员会把他从公钥列表中删掉，这样一来整个过程，密码没有暴露，运维也在这段时间内登上了机器，很完美。
>
>2、因为自己懒，不想敲密码,想直接使用自己在电脑上配置的ssh_xxx来登录

#### 使用ssh-keygen生成密匙对

```
➜  ~ cd .ssh 
➜  .ssh 
➜  .ssh ssh-keygen -t rsa
Generating public/private rsa key pair.
Enter file in which to save the key (/Users/zhaochao/.ssh/id_rsa): id_rsa
Enter passphrase (empty for no passphrase):
```

回车以后，询问是否要输入一个密码来保护这个密匙，为了方便起见，我就不输入了，不然每次使用这个密匙文件还要输一遍密码,直接一路回车就ok

到了这里，可以看到我们已经创建好了密匙对

```
➜  .ssh ls
id_rsa      id_rsa.pub  known_hosts
```
我们可以使用`cat`命令来获取这个id_rsa.pub

```
➜  .ssh cat id_rsa.pub 
ssh-rsa AAAAB3NDzaC1yc2EAFADAADAQFcPFT++P8rOkltuIGDSle45yEyDXtDGcCVcHmHAa8TH3/1G+cGusuvREewaJWdxS95fopdi7iumedN+bOJsCIyDsskuLJtt9FaT1MlEg8C3DH+VTkVTKadOYtFF0cwSediRadqjhMQqTOq6gH1g6zTkr4eGEIPp5RaPFDLZmeGiOeYvIS+d8eIdpHckO90KWL0T0bOcjifHlle+5ia0tSo4ZP5ekp0CxTlu0McorQIU7C1cjW/Iz5xQHpYFOdkKYImQob0H0bletwGY8K4A48ioEG9bfiBwbhmCrt93SGcp40G4e6KsuqLMe+5JUqqRcOg7TqvmD/FnftFbjDz2VTGJ/WlEJ4P3HXvAR8CdKPc/DJMmTV3oxwlmdgtDLFpqW7BkydkF2wSkO1+QP4p5ua3Ned7VLlNb16JKafBXD59q1yhwtsvhDxcgmk0tL5O3H4KPCHMZ6tePcWF2wPf+K/zLqV5DF2KXjat+PMveQf6NWAP+KK2jlG8AyV2+BBTI4nC5TdRZMXGmnVOvoc/eGbBB0NohoxZbFnjIVmpLUJcXrUXmXPojjauXQ0z37Ve/uOarVCkDTMimL+GZ4HrOx2s7FR1XM0cpw== 123456@qq.com
```

#### 将公钥文件放进authorized_keys中

目标主机上如果没有.ssh文件夹，就自己创建一个，创建好之后,再创建一个authorized_keys文件。如果有的话就不用了。

```
root@wzt-dev2-PC:~/.ssh# ll
total 8
drwxr-xr-x  2 root root 4096 7月  17 19:49 ./
drwx------ 10 root root 4096 7月  17 19:49 ../
-rw-r--r--  1 root root    0 7月  17 19:49 authorized_keys
```

```
vim authorized_keys
```

将刚才的id_rsa.pub的内容放进去保存一下就可以

#### 这里需要注意

>authorized_keys 文件对权限有哟求，必须是600(-rw——-)或者644
.ssh目录 必须是700(drwx——),否则一会儿登录不成功
弄完之后检查一下权限，如果不是的话，改成响应的权限就ok了

#### 准备就绪，在客户端上登录目标主机

在登录之前，要确认一下目标主机是否允许密匙对登录，一般都是打开的，如果没有打开就自己打开

>(查看 /etc/ssh/sshd_config 文件内容 中的 PubkeyAuthentication 这一项是否为 yes,如果不是就自己修改成yes之后重启sshd服务 )
>

```
wzy@wzt-dev2-PC:~$ cat /etc/ssh/sshd_config
# Package generated configuration file
# See the sshd_config(5) manpage for details

# What ports, IPs and protocols we listen for
Port 22
# Use these options to restrict which interfaces/protocols sshd will bind to
#ListenAddress ::
#ListenAddress 0.0.0.0
Protocol 2
# HostKeys for protocol version 2
HostKey /etc/ssh/ssh_host_rsa_key
HostKey /etc/ssh/ssh_host_dsa_key
HostKey /etc/ssh/ssh_host_ecdsa_key
HostKey /etc/ssh/ssh_host_ed25519_key
#Privilege Separation is turned on for security
UsePrivilegeSeparation yes

# Lifetime and size of ephemeral version 1 server key
KeyRegenerationInterval 3600
ServerKeyBits 1024

# Logging
SyslogFacility AUTH
LogLevel INFO

# Authentication:
LoginGraceTime 120
PermitRootLogin yes
StrictModes yes

RSAAuthentication yes
PubkeyAuthentication yes   <---------------  在这里
#AuthorizedKeysFile %h/.ssh/authorized_keys

```

到此我们就配置完了。 (不用输入密码就可以登录了)

```
➜  ~ ./ssh_my_tencent 
Welcome to Ubuntu 18.04.4 LTS (GNU/Linux 4.15.0-118-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

  System information as of Wed Dec  9 10:45:26 CST 2020

  System load:  0.0               Processes:           89
  Usage of /:   5.0% of 49.15GB   Users logged in:     0
  Memory usage: 12%               IP address for eth0: 172.21.0.16
  Swap usage:   0%

 * Introducing self-healing high availability clusters in MicroK8s.
   Simple, hardened, Kubernetes for production, from RaspberryPi to DC.

     https://microk8s.io/high-availability

 * Canonical Livepatch is available for installation.
   - Reduce system reboots and improve kernel security. Activate at:
     https://ubuntu.com/livepatch
New release '20.04.1 LTS' available.
Run 'do-release-upgrade' to upgrade to it.


< Hi, How are you >
 -----------------
        \   ^__^
         \  (oo)\_______
            (__)\       )\/\
                ||----w |
                ||     ||
Last login: Wed Dec  9 09:21:16 2020 from 124.126.1.181
```