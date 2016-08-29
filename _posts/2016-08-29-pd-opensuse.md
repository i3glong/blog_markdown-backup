---
title: Mac下parallels desktop安装openSUSE过程
tags:
  - openSUSE
  - parallels desktop
categories:
  - 折腾笔记
permalink: pd-opensuse
date: 2016-08-29 19:03:14
---
<Excerpt in index | 首页摘要> 
+MacBook最近刚重装了系统，新安装parallels desktop虚拟机第一时间安装openSUSE42.1。这篇博文就记录下过程吧。<!-- more -->
<The rest of contents | 余下全文>
## 安装前准备
- MacBook一台
- parallels desktop虚拟机
- openSUSE42.1安装镜像 

## 安装后配置

	新建虚拟机、安装系统过程略
### 第一步
配置软件源，官方源太慢我用的是阿里云的镜像源
地址是  http://mirrors.aliyun.com/
然后更新重启，安装内核devel包gcc等工具。
还有个烦人的玩意儿就是kde有下角那个提示更新的货，果断干掉。
那个包的名字是plasma5-pk-updates,删除后注销再登录世界就清净了。
### 第二步
就是虚拟机下openSUSE分辨率问题，始终就那么小是因为驱动问题。
安装parallels tools即可解决

### 第三步
快照指，因为接下来还不知道会折腾什么呢？虚拟机一定做好快照出问题好恢复。
