---
layout: post
title: 阿里云服务器 mysql 数据库为什么用 DBeaver 等软件远程连接不上？
categories: [数据库]
description: 阿里云服务器 mysql 数据库为什么用 DBeaver 等软件远程连接不上？
keywords: MySQL，数据库，阿里云
---

解决 DBeaver 远程连接阿里云 MySQL 数据库时出现“无法连接到数据库”、“时间超时”等问题。

这里解决的具体问题是，在 host、端口、用户、密码等信息设置正确的时候，DBeaver 连接的时候会报错“Communications link failure  The last packet sent successfully to the server was 0 milliseconds ago. The driver has not received any packets from the server.   Can not read response from server. Expected to read 4 bytes, read 0 bytes before connectio”这样的信息。

查了很多博主的答案，大家都聚焦在 mysql 超时的问题，类似让修改 my.cnf 文件，增加 wait_timeout=31536000 这样的配置，也就是增加了连接超时时间。我按照这样的方法去尝试之后发现是行不通的，前前后后折腾了一会儿。由于自己薄弱的计算机网络知识。只能采取对比法排除，现在出问题的服务器是新开的，之前旧的服务器是能够正常连接的，所以对比了两个服务器的配置，发现新开的服务器的配置和旧的服务器配置是一模一样的，但是新开的服务器就是无法连接，而旧的服务器却可以正常连接。

最后发现是阿里云的安全组的问题。

首先，阿里云的安全组是默认关闭的，需要手动添加开启。

其次，阿里云的安全组默认只开两个端口，也就是 SSH 的 22 和 RDP 的 3389。

![ssh and rdp](/images/blog/01_just_ssh_rdp.png)

mysql 的端口默认是 3306，在你新开阿里云服务器后，mysql 的 3306 端口并没有开启，需要手动开启。

![mysql port](/images/blog/02_mysql_port.png)

阿里云的安全组需要手动添加规则，添加规则的目的是允许从指定的 IP 地址访问指定的端口。阿里云服务器开放端口给 IP 使用，可以参考阿里云的官方文档：[阿里云服务器开放全部端口给所有 IP 使用教程](https://developer.aliyun.com/article/793961)

在添加了 mysql 的端口后，DBeaver 就可以正常连接了。

由于自己薄弱的计算机网络知识，导致浪费了很多时间，不过最终找到了问题所在，也在这里记录一下，以备后用。
