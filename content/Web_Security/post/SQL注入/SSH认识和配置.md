---
title: "SSH认识和配置"
date: 2022-12-18T17:35:33+08:00
tags: ["SSH"]
draft: false
featured_image: "/images/Landscape with rainbow.jpg"
---

# 导读

最初接触ssh是在登录github远程仓库时，知道除了https还有ssh方法（ssh的建立安全隧道的作用）；然后再次接触到ssh是因为在看见switchomega插件的时候想去了解代理和翻墙的原理，知道了也可以用ssh进行（ssh的绕过防火墙的作用）。

以上提到的都是ssh的作用，既然要学作用，干脆把ssh系统性的都学了一遍。

# SSH-是什么

> Secure Shell，一种用于加密两台计算机之间通信并支持各种身份验证机制的**网络协议**。、

- 实现途径：把明文的网络协议套用在它里面
- 架构：采取S/C（Server/client），即服务器/客户端模式



# SSH-作用概览

## 用途

> 建立加密隧道。

- 登录服务器
- **端口转发**（port forwarding）。

## 概述

> 专为远程登录会话和两台电脑之间通过安全的链接进行数据交换的网络协议。

```ruby
      ssh客户端(ssh)                       ssh服务端(sshd)

     +----------+                       +---------+
     |          |                       |         |
     |          |                       |         |
     |          |                       |         |
     |          |                       |         |
     +---+---+--+                       +-+---+---+
         |   |                            |   |
         |   |                            |   |
         |   +----------------------------+   |
         |                                    |
         |        ssh加密了的TCP通信            |
         +------------------------------------+

                    SSH连接
```

- 有效防止IP欺骗和DNS欺骗

### 所做准备

- OpenSSH：（服务端）

  - 提供加密通话的计算机程序。（SSH只是一个协议，而OpenSSH是其开源实现）

  - **作为服务端（sshd）需要安装OpenSSH**

- 客户端（ssh）：

  - Linux系统：

    直接使用`SSH`命令

  - Windows

    - Putty/Putty Tray
    - Bitvise（免费）
    - Xshell（被爆后门多）
    - Cmder（集成 Git For Windows、Unix命令等，通过ssh agent来配置）

## 登录远程服务器作用

### 密码登录远程服务器

> 基本密码登录的客户端和服务端配置操作

- 客户端：[SSH 客户端 - SSH 教程 - 网道 (wangdoc.com)](https://wangdoc.com/ssh/client)

- 服务端：[SSH 服务器 - SSH 教程 - 网道 (wangdoc.com)](https://wangdoc.com/ssh/server)

### 秘钥（SSH Key）登录远程服务器

- 作用：**方便的登录到ssh服务器，而无需发送密码到网络中**，所以安全。（不走HTTPS的方式，所以不用每次都输入密码）

- 原理：SSH利用SSH Key来进行之前提到的基于密钥的安全验证

  即当本地机器登录远程主机时，本地主机向远程主机发起一个登录请求，远程主机收到消息后，随机生成一个字符串进行公钥加密，并发给本地，本地对这个字符串进行私钥解密，再发给远程主机，远程主机对比是否相同，若相同则认证成功。

- 步骤：

  - 在客户端生成公钥和私钥
  - 在服务端的配置文件中加入公钥

- **每台主机都要生成一个秘钥**

[SSH 密钥登录 - SSH 教程 - 网道 (wangdoc.com)](https://wangdoc.com/ssh/key)

#### github配置SSH

> 步骤
>
> > 1. 使用秘钥生成工具生成rsa私钥（在`id_rsa`私钥、`id_rsa.pub`公钥、`known_hosts`文件里）和秘钥
> > 2. 将rsa公钥添加到代码托管平台
> > 3. 将rsa秘钥添加到ssh-agent中，为ssh client指定使用的秘钥文件

``````
Linux命令


cd ~/..sh    #切换工作目录到“home/用户名/..sh”
# “~”表示进入到当前用户的HOME目录，即"home/用户名"
#以“.”开头，表示隐藏的文件或目录，用“ls”默认看不到



ls      #打印当前目录清单(list的缩写)
#如果已经配置了秘钥，则会出现三个文件

ssh-keygen -t rsa -C     #如果本机客户端没有ssh key，则使用此命令生成

cat id_rsa.pub     #利用cat命令，查看读取一个文本文件的内容
#把读取到的秘钥信息粘贴在github-setting-秘钥-新建秘钥中


ssh -T git@github.com   #验证是否安装成功

``````

# 

## 端口转发作用

- **建立了一条安全的隧道，将原本不加密的数据都进行了加密。**比如端口转发访问Telnet、FTP 等明文服务，数据传输就都会加密。
- **绕过防火墙。**因为作为数据通信的加密跳板。[跳转](#2)

## 端口转发的方式（ssh绑定）

[SSH 端口转发 - SSH 教程 - 网道 (wangdoc.com)](https://wangdoc.com/ssh/port-forwarding)

- local 本地转发
- remote 远程转发
- dynamic 动态绑定

### SSH-翻墙作用-VPN

<a name="2"></a>

> 利用端口转发中的动态转发原理
>
> 动态体现在：代理服务器的目标转发地址是不固定的，根据客户端的请求而定的。

https://blog.csdn.net/promising_Li/article/details/79259624

实例：

1. 假设客户机上的SSH会监听本地的一个端口7001。

2. 小明在内网中，想要访问油管网站www.youtube.com:80。

3. 直接访问当然访问不了，所以在发送访问请求时，本机会把这个请求告知7001端口的SSH进程。

   通过socks协议实现。通过在浏览器或者其他应用程序上配置socks代理。（代理指向127.0.0.1，端口7001）

4. 7001进程上的SSH会建立SSH加密链接发送到墙外服务器的SSH上，由于是动态绑定的，所以代理服务器服务器会将www.youtube.com:80的请求发送给www.youtube.com上的80端口。

5. 代理服务器在收到回复后会原路返回客户机上的SSH，客户机上的SSH再返回需要访问外网的应用程序



