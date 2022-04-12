---
title: Linux cluster setup
date: 2022-04-12 17:17:26
categories: 
- code
tags:
- linux
---

# 搭建Linux集群

- 当前主力机是m1芯片的macbook pro，所以linux镜像用的是arm64位的centos7，虚拟机软件选用VMware Fusion。本次目标搭建三台虚拟机，实现网络互通。

<!--more-->

## 搭建准备

- vmware
- Centos7 arm64镜像
- Royal TSX（用于管理SSH连接）

## 具体实施

1. 直接把镜像拖入vmware，开始进行安装（不要选择最小化安装）。

2. 正常开机之后，一般是无法正常联网，这个时候我们首先选择桥接模式的wifi模式，然后可以用`dhclient`来给当前虚拟机一个可用的动态ip，注意这个ip每次开机都会变化。也可以自己指定ip。

3. 编辑网络配置文件

   需要更改的地方是把BOOTPROTO=dhcp改为static，另外ONBOOT改为yes，然后添加IPADDR、NETMASK、GATEWAY、DNS1。网络可用的表现是虚拟机能正常上网，能和宿主机互相通信。

   ```shell
   vim /etc/sysconfig/network-scripts/ifcfg-ens160
   ```

   ```properties
   TYPE=Ethernet
   PROXY_METHOD=none
   BROWSER_ONLY=no
   BOOTPROTO=static
   DEFROUTE=yes
   IPV4_FAILURE_FATAL=no
   IPV6INIT=yes
   IPV6_AUTOCONF=yes
   IPV6_DEFROUTE=yes
   IPV6_FAILURE_FATAL=no
   NAME=ens160
   UUID=60593f15-8470-433d-933c-c0a1cc25faa3
   DEVICE=ens160
   ONBOOT=yes
   IPADDR=192.168.1.201
   NETMASK=255.255.255.0
   GATEWAY=192.168.1.1
   DNS1=119.29.29.29
   ```

   ```shell
   systemctl restart network.service
   ```

4. 编辑hostname

   ```shell
   vim /etc/hostname
   ```

5. 编辑hosts

   ```shell
   vim /etc/hosts
   ```

6. 关闭防火墙

   ```shell
   # 临时关闭
   systemctl stop firewalld
   # 永久关闭
   systemctl disable firewalld.service
   ```

7. 至此，单台虚拟机就准备完毕，这个时候将其关机，然后创建完整克隆，然后再改下克隆机的ip，确保虚拟机之间可以互相通信。
