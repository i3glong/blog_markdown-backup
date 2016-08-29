---
title: linux文件管理命令
tags:
  - Linux
  - openSUSE
categories:
  - Linux
permalink: linux-file-command
date: 2016-08-29 20:23:04
---
<Excerpt in index | 首页摘要> 
linux下各目录的作用，及常用命令用法整理总结。。
+<!-- more -->
<The rest of contents | 余下全文>
## 根目录下文件夹
### 首先查看根下的各目录（以openSUSE42.1为例）
``` bash 
linux-vnja: # ls /                      #列出根目录下所以文件
bin   dev  home  lib64  opt   root  sbin     .snapshots  sys  usr
boot  etc  lib   mnt    proc  run   selinux  srv         tmp  var
```
- 各目录的用处如下：
- bin 普通用户可以使用的二进制可执行命令
- dev 设备文件
- home 存放普通用户家目录的目录
- lib64 库文件（64位）
- opt 通常存放第三方软件
- root 管理员家目录
- sbin 管理员使用的命令
- .snapshots 快照
- sys 类似于proc
- usr 系统文件
- boot 存放系统启动相关文件 kernel，grub2等
- etc 配置文件目录
- lib 标准库文件
- mnt 手动挂载设备的挂载点
- proc 伪文件系统，只存于内存，为当前系统状态。
- run 系统启动以来描述系统信息的文件如pid
- selinux 如名字一样selinux的相关文件
- srv 服务所在位置 如 ftp  Apache2
- tmp 临时目录
- var 存放日志、邮件。。
### Linux文件的时间
如下使用ls -l查看到的只是文件的创建时间
``` bash
linux-vnja: # ls -l test.txt
-rw-r--r-- 1 root root 0 6月 24 04:44 test.txt
linux-vnja: # stat test.txt
文件："test.txt"
大小：0 块：0          IO 块：4096 普通空文件
设备：24h/36dInode：70173 硬链接：1
权限：(0644/-rw-r--r--)  Uid：(    0/    root)   Gid：(    0/    root)
最近访问：2016-06-24 04:44:45.373953389 +0800
最近更改：2016-06-24 04:44:45.373953389 +0800
最近改动：2016-06-24 04:44:45.373953389 +0800
创建时间：-

最近访问：time 指最后一次打开查看的时间
最近修改：mtime 指最后一次修改文件内容的时间
删除，新增替换等。。。
最近改动：ctime 指最后一次改变文件属性的时间，（权限等）
```