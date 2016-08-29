---
title: 解决LINUX 双网卡无法上网问题小记
tags:
  - Linux
  - 双网卡
categories:
  - Linux
permalink: linux-dual-network
date: 2016-08-29 21:21:06
---
<Excerpt in index | 首页摘要> 
教室的电脑网段是172.16.30.0/24      一些学习资料和yum源的ip是172.16.8.100
默认的情况下教室的电脑是可以互通以及连接教学ftp172.16.8.100
现在需要上网可是用安卓手机usb共享电脑上网后发现，172.16.30.0网段是通的，但就是上不了网，要上网就只能禁用eth0+<!-- more -->
<The rest of contents | 余下全文>

## 场景：
教室的电脑网段是172.16.30.0/24      
一些学习资料和yum源的ip是172.16.8.100
默认的情况下教室的电脑是可以互通以及连接教学ftp172.16.8.100
现在需要上网可是用安卓手机usb共享电脑上网后发现，172.16.30.0网段是通的，但就是上不了网，要上网就只能禁用eth0。。。。

### 网卡：
- 电脑eth0  通过网线连接内网  172.16.30.0/24     网关172.16.30.254
- 手机usb0  通过wifi连接外网  192.168.42.0/24    网关192.168.42.129
- 最终效果：电脑连接老师VNC 正常连接教学资料ftp：172.16.8.100 正常 连接外网正常

- 包括kvm里192.168.122.0 网段也可以连通ftp了  而且连通外网正常
## 解决过程：
``` sh
[root@localhost ~]# route       #查看路由表
Kernel IP routing table
Destination     Gateway         Genmask         Flags Metric Ref    Use Iface
172.16.30.0     *               255.255.255.0   U     1      0        0 eth0
192.168.42.0    *               255.255.255.0   U     1      0        0 usb0
192.168.122.0   *               255.255.255.0   U     0      0        0 virbr0
default         bogon           0.0.0.0         UG    0      0        0 eth0
[root@localhost ~]# route add default gw 192.168.42.129  #添加网关
[root@localhost ~]# route
Kernel IP routing table
Destination     Gateway         Genmask         Flags Metric Ref    Use Iface
172.16.30.0     *               255.255.255.0   U     1      0        0 eth0
192.168.42.0    *               255.255.255.0   U     1      0        0 usb0
192.168.122.0   *               255.255.255.0   U     0      0        0 virbr0
default         bogon           0.0.0.0         UG    0      0        0 usb0
default         bogon           0.0.0.0         UG    0      0        0 eth0
[root@localhost ~]# route add -net 192.168.42.0 netmask 255.255.255.0 dev usb0  #设置192.168.42.0网段默认使用usb0网卡
[root@localhost ~]# ping 172.16.8.100   #这个时候还是不通
PING 172.16.8.100 (172.16.8.100) 56(84) bytes of data.
^C
--- 172.16.8.100 ping statistics ---
2 packets transmitted, 0 received, 100% packet loss, time 1437ms

[root@localhost ~]# route add -net 172.16.8.0 netmask 255.255.255.0 dev eth0  #设置172.16.8.0网段默认使用eth0网卡     
[root@localhost ~]# ping 172.16.8.100
PING 172.16.8.100 (172.16.8.100) 56(84) bytes of data.
64 bytes from 172.16.8.100: icmp_seq=1 ttl=63 time=2.60 ms
^C
--- 172.16.8.100 ping statistics ---
1 packets transmitted, 1 received, 0% packet loss, time 934ms
rtt min/avg/max/mdev = 2.609/2.609/2.609/0.000 ms
[root@localhost ~]# route
Kernel IP routing table
Destination     Gateway         Genmask         Flags Metric Ref    Use Iface
172.16.30.0     *               255.255.255.0   U     1      0        0 eth0
192.168.42.0    *               255.255.255.0   U     0      0        0 usb0
192.168.42.0    *               255.255.255.0   U     1      0        0 usb0
172.16.8.0      *               255.255.255.0   U     0      0        0 eth0
192.168.122.0   *               255.255.255.0   U     0      0        0 virbr0
default         bogon           0.0.0.0         UG    0      0        0 usb0
default         bogon           0.0.0.0         UG    0      0        0 eth0
[root@localhost ~]# 

```