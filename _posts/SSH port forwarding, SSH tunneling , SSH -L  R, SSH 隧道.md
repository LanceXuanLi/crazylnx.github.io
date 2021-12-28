```
---
layout: post
title:  SSH port forwarding, SSH tunneling , SSH -L / R, SSH 隧道
date: 2021-12-28
categories:
- SSH
tags: [SSH]
---
```



作者：李楠轩

日期：2021年12月26日

> 最近帮远程实习的女朋友配置在家访问公司主机的SSH 隧道，我发现虽然用了很多年SSH，然而只是使用简单的SSH登录功能，复杂的SSH功能并没有很好的应用。这篇博客记录了帮助女朋友搭建SSH隧道过程中踩的坑并对SSH隧道技术做最新中文的介绍和讲解。 (2021年12月）
>
> 本文涉及： SSH -L, SSH -R 



## SSH 隧道 

> SSH port forwarding is a mechanism in [SSH](https://www.ssh.com/ssh/) for tunneling application ports from the client machine to the server machine, or vice versa. It can be used for *adding encryption to legacy applications*, *going through firewalls*, and some system administrators and IT professionals use it for *opening backdoors* into the internal network from their home machines. It can also be abused by hackers and malware to open access from the Internet to the internal network. See the [SSH tunneling](https://www.ssh.com/ssh/tunneling/) page for a broader overview.  
>
> ——摘自 https://www.ssh.com/academy/ssh/tunneling/example

以上的介绍主要有一下知识点：

- SSH隧道(SSH Tunneling)又叫SSH port forwarding

- 主要用来建立隧道来连接两个主机

- 主要功能是跨过firewalls和打通内网机器和远程机器的网络连接。

  ![image-20211226190019891](https://tencentimg-1300649502.cos.ap-beijing.myqcloud.com/blog-image/ image-20211226190019891.png)

  比如公司的服务器配置只能在公司网络下访问，而自己的笔记本放在公司里，家里的台式机想要访问公司的服务器，可以使用SSH隧道来完成这个目标。



## SSH Local forwarding 本地转发

本地转发指把本地主机的端口转发到其他主机，公司里通常不允许直接访问到产品线的服务器，需要通过跳板机来达到安全访问。

### 跳板机为服务器本身

服务器B不但是跳板机，也是需要访问的内部资源。

当转发本地主机A端口8050到远程主机B端口8060后，访问本地主机端口8050，相当于访问主机B端口8060 （这个例子本身）

![image-20211226194019819](https://tencentimg-1300649502.cos.ap-beijing.myqcloud.com/blog-image/ image-20211226194019819.png)



### 跳板机为其他服务器

服务器C为需要访问的主机，而服务器B为跳板机，服务器A为自己的外网电脑。

![image-20211227122354995](https://tencentimg-1300649502.cos.ap-beijing.myqcloud.com/blog-image/ image-20211227122354995.png)



### 本地转发的相关命令

```shell
ssh -L [local_hostname:]local_port:remote_host:remote_port user@ssh_server_hostname -p 22 -g

# local_hostname 可以被省略
# local_port为本地的端口
# remote_host, remote_port为最终想要到达的服务端ip地址, 端口号
# user@ssh_server_hostname 跳板机的信息。
# -p 22 跳板机的访问端口号
# -g GatewayPorts 带上-g后代表允许其他电脑访问[local_hostname:]local_port相当于访问remote_host:remote_port
```



### SSH隧道需求

需要具备本地主机和远程主机的登录权限。

并且远程主机允许 incoming port forwarding

#### Steps by steps

主机A 101.43.39.33 

跳板机B 110.40.198.22  

主机C  110.40.198.23  

将主机A的8050端口本地转发到主机C的8060端口

1. 登录到主机A

2. `ssh -p 22 user@101.43.39.33 `  ***p 后的端口为连接服务器的端口，默认是22，但是有可能会是别的端口号，需要确认***

3. 登录服务器后输入

   ```shell
   ssh -L 8050:110.40.198.23:8060 user@110.40.198.22 -p 22 -g
   
   ```
# ssh -L 主机1的port:主机C的ip:主机Cport 跳板机B -p 跳板机B的port -g
   ```
   
   






   ```