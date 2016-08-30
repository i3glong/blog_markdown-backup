---
title: Centos7.2搭建GlusterFS集群文件系统及九种储存卷简介
tags:
  - GlusteFS
  - Centos7.2
  - 分布式
categories:
  - Linux
  - 储存
permalink: glusterfs
date: 2016-08-30 07:45:24
---
<Excerpt in index | 首页摘要> 
GlusterFS 是一个集群的文件系统，支持 PB 级的数据量。GlusterFS 通过 RDMA 和 TCP/IP 方式将分布到不同服务器上的存储空间汇集成一个大的网络并行文件系统。+<!-- more -->
<The rest of contents | 余下全文>

`实验环境:` 准备七台虚拟机各额外添加三块硬盘
关闭selinux   关闭防火墙
`系统环境:` centos7.2  最小化安装
更改主机名和hosts文件实现个主机互相解析
172.16.8.145 node1.3glong.com 
172.16.8.146 node2.3glong.com 
172.16.8.147 node3.3glong.com 
172.16.8.148 node4.3glong.com
172.16.8.149 node5.3glong.com 
172.16.8.150 node6.3glong.com 
172.16.8.151 client.3glong.com

- 新加三块硬盘
``` bash 
[root@node1 ~]# lsblk
NAME            MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
sda               8:0    0   20G  0 disk 
├─sda1            8:1    0  500M  0 part /boot
└─sda2            8:2    0 19.5G  0 part 
  ├─centos-root 253:0    0 17.5G  0 lvm  /
  └─centos-swap 253:1    0    2G  0 lvm  [SWAP]
sdb               8:16   0  200G  0 disk 
sdc               8:32   0  200G  0 disk 
sdd               8:48   0  200G  0 disk 
sr0              11:0    1 1024M  0 rom  
硬盘格式化及开机自动挂载
```
## 硬盘分区
``` bash
[root@client ~]# pssh -h ip.txt -i "mkfs.xfs -i size=512 /dev/sdb"
[root@client ~]# pssh -h ip.txt -i "mkfs.xfs -i size=512 /dev/sdc"
[root@client ~]# pssh -h ip.txt -i "mkfs.xfs -i size=512 /dev/sdd"
[root@client ~]# pssh -h ip.txt -i "mkdir -p /glusterfs/{sdb,sdc,sdd}"
[root@client ~]# pssh -h ip.txt -i "echo /dev/sdb /glusterfs/sdb xfs defaults 0 0 >> /etc/fstab"
[root@client ~]# pssh -h ip.txt -i "echo /dev/sdc /glusterfs/sdc xfs defaults 0 0 >> /etc/fstab"
[root@client ~]# pssh -h ip.txt -i "echo /dev/sdd /glusterfs/sdd xfs defaults 0 0 >> /etc/fstab"
[root@client ~]# pssh -h ip.txt -i "mount -a"
[root@client ~]# pssh -h ip.txt -i "lsblk"
[1] 20:41:01 [SUCCESS] 172.16.8.145
NAME            MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
sda               8:0    0   20G  0 disk 
├─sda1            8:1    0  500M  0 part /boot
└─sda2            8:2    0 19.5G  0 part 
  ├─centos-root 253:0    0 17.5G  0 lvm  /
  └─centos-swap 253:1    0    2G  0 lvm  [SWAP]
sdb               8:16   0  200G  0 disk /glusterfs/sdb
sdc               8:32   0  200G  0 disk /glusterfs/sdc
sdd               8:48   0  200G  0 disk /glusterfs/sdd
sr0              11:0    1 1024M  0 rom  
[2] 20:41:01 [SUCCESS] 172.16.8.147
NAME            MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
sda               8:0    0   20G  0 disk 
├─sda1            8:1    0  500M  0 part /boot
└─sda2            8:2    0 19.5G  0 part 
  ├─centos-root 253:0    0 17.5G  0 lvm  /
  └─centos-swap 253:1    0    2G  0 lvm  [SWAP]
sdb               8:16   0  200G  0 disk /glusterfs/sdb
sdc               8:32   0  200G  0 disk /glusterfs/sdc
sdd               8:48   0  200G  0 disk /glusterfs/sdd
sr0              11:0    1 1024M  0 rom  
[3] 20:41:01 [SUCCESS] 172.16.8.146
NAME            MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
sda               8:0    0   20G  0 disk 
├─sda1            8:1    0  500M  0 part /boot
└─sda2            8:2    0 19.5G  0 part 
  ├─centos-root 253:0    0 17.5G  0 lvm  /
  └─centos-swap 253:1    0    2G  0 lvm  [SWAP]
sdb               8:16   0  200G  0 disk /glusterfs/sdb
sdc               8:32   0  200G  0 disk /glusterfs/sdc
sdd               8:48   0  200G  0 disk /glusterfs/sdd
sr0              11:0    1 1024M  0 rom  
[4] 20:41:01 [SUCCESS] 172.16.8.150
NAME            MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
sda               8:0    0   20G  0 disk 
├─sda1            8:1    0  500M  0 part /boot
└─sda2            8:2    0 19.5G  0 part 
  ├─centos-root 253:0    0 17.5G  0 lvm  /
  └─centos-swap 253:1    0    2G  0 lvm  [SWAP]
sdb               8:16   0  200G  0 disk /glusterfs/sdb
sdc               8:32   0  200G  0 disk /glusterfs/sdc
sdd               8:48   0  200G  0 disk /glusterfs/sdd
sr0              11:0    1 1024M  0 rom  
[5] 20:41:01 [SUCCESS] 172.16.8.148
NAME            MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
sda               8:0    0   20G  0 disk 
├─sda1            8:1    0  500M  0 part /boot
└─sda2            8:2    0 19.5G  0 part 
  ├─centos-root 253:0    0 17.5G  0 lvm  /
  └─centos-swap 253:1    0    2G  0 lvm  [SWAP]
sdb               8:16   0  200G  0 disk /glusterfs/sdb
sdc               8:32   0  200G  0 disk /glusterfs/sdc
sdd               8:48   0  200G  0 disk /glusterfs/sdd
sr0              11:0    1 1024M  0 rom  
[6] 20:41:01 [SUCCESS] 172.16.8.149
NAME            MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
sda               8:0    0   20G  0 disk 
├─sda1            8:1    0  500M  0 part /boot
└─sda2            8:2    0 19.5G  0 part 
  ├─centos-root 253:0    0 17.5G  0 lvm  /
  └─centos-swap 253:1    0    2G  0 lvm  [SWAP]
sdb               8:16   0  200G  0 disk /glusterfs/sdb
sdc               8:32   0  200G  0 disk /glusterfs/sdc
sdd               8:48   0  200G  0 disk /glusterfs/sdd
sr0              11:0    1 1024M  0 rom  

```
``` bash

[root@client ~]# pssh -h ip.txt -i "yum install -y http://mirrors.aliyun.com/epel/epel-release-latest-7.noarch.rpm"
[root@client ~]# pssh -h ip.txt -i "yum install -y curl http://download.gluster.org/pub/gluster/glusterfs/LATEST/EPEL.repo/glusterfs-epel.repo -o /etc/yum.repos.d/glusterfs-epel.repo"
[root@client ~]# pssh -h ip.txt -i "yum -y install glusterfs-server samba"
[root@client ~]# pssh -h ip.txt -i "systemctl enable glusterd.service"
[root@client ~]# pssh -h ip.txt -i "systemctl enable rpcbind"
[root@client ~]# pssh -h ip.txt -i "systemctl restart rpcbind"
[root@client ~]# pssh -h ip.txt -i "systemctl start glusterd.service"

切换至任意节点
[root@node1 ~]# gluster peer probe node2.3glong.com
peer probe: success. 
[root@node1 ~]# gluster peer probe node3.3glong.com
peer probe: success. 
[root@node1 ~]# gluster peer probe node4.3glong.com
peer probe: success. 
[root@node1 ~]# gluster peer probe node5.3glong.com
peer probe: success. 
[root@node1 ~]# gluster peer probe node6.3glong.com
peer probe: success. 
[root@node1 ~]# gluster peer status
Number of Peers: 5

Hostname: node2.3glong.com
Uuid: ecdfd9e1-6695-4b16-92de-815b598fd8e4
State: Peer in Cluster (Connected)

Hostname: node3.3glong.com
Uuid: 39029856-dd70-4134-b636-5d013218327f
State: Peer in Cluster (Connected)

Hostname: node4.3glong.com
Uuid: cb52ef8b-1db5-4639-aa31-19788e4f65c3
State: Peer in Cluster (Connected)

Hostname: node5.3glong.com
Uuid: f8107ca5-1381-4411-baf6-6dc859d6e2eb
State: Peer in Cluster (Connected)

Hostname: node6.3glong.com
Uuid: 520e4057-1c29-4981-98c8-d9ef1abf51a4
State: Peer in Cluster (Connected)
[root@node1 ~]# 

```


### 分布式卷

> 分布卷也称为哈希卷，多个文件在多个 brick 上使用哈希算法随机存储。
> 应用场景:   大量小文件
> 优点：       读/写性能好
> 缺点：       如果存储或服务器故障，数据将丢失
> 不指定卷类型，默认是分布卷
``` bash
[root@node1 ~]# gluster volume create dis_vol \
> node1.3glong.com:/glusterfs/sdb/dv1 \
> node2.3glong.com:/glusterfs/sdb/dv1 \
> node3.3glong.com:/glusterfs/sdb/dv1
volume create: dis_vol: success: please start the volume to access data
[root@node1 ~]# gluster volume info dis_vol
 
Volume Name: dis_vol
Type: Distribute
Volume ID: 2a4af66a-1f10-4af7-b103-bf2425ce2c45
Status: Created
Number of Bricks: 3
Transport-type: tcp
Bricks:
Brick1: node1.3glong.com:/glusterfs/sdb/dv1
Brick2: node2.3glong.com:/glusterfs/sdb/dv1
Brick3: node3.3glong.com:/glusterfs/sdb/dv1
Options Reconfigured:
performance.readdir-ahead: on

```
### 条带卷

> 类似与RAID0
> 将文件分成条带，存放在多个 brick上，默认条带大小128K
> 应用场景:   大文件
> 优点：       适用于大文件存储
> 缺点：       可靠性低，brick 故障会导致数据全部丢失

> stripe = brick
``` bash

[root@node1 ~]# gluster volume create str_vol stripe 3 \
> node2.3glong.com:/glusterfs/sdb/sv3 \
> node1.3glong.com:/glusterfs/sdb/sv3 \
> node3.3glong.com:/glusterfs/sdb/sv3
volume create: str_vol: success: please start the volume to access data
[root@node1 ~]# gluster volume info str_vol
 
Volume Name: str_vol
Type: Stripe
Volume ID: 52c3269d-7145-4fc5-abfb-75e30d091ef6
Status: Created
Number of Bricks: 1 x 3 = 3
Transport-type: tcp
Bricks:
Brick1: node2.3glong.com:/glusterfs/sdb/sv3
Brick2: node1.3glong.com:/glusterfs/sdb/sv3
Brick3: node3.3glong.com:/glusterfs/sdb/sv3
Options Reconfigured:
performance.readdir-ahead: on
```
### 复制卷
> 类似于  RAID1
> 多个文件在多个brick上复制多份，brick 的数目要与需要复制的份数相等，建议brick分布在不同的服务器上。
> 应用场景:   对可靠性和读性能要求高的场景
> 优点：       读性能好
> 缺点：       写性能差

> replica = brick
``` bash
[root@node1 ~]# gluster volume create rep_vol replica 3 \
> node1.3glong.com:/glusterfs/sdb/rv2 \
> node2.3glong.com:/glusterfs/sdb/rv2 \
> node3.3glong.com:/glusterfs/sdb/rv2
volume create: rep_vol: success: please start the volume to access data
[root@node1 ~]# gluster volume info rep_vol
 
Volume Name: rep_vol
Type: Replicate
Volume ID: 9607e500-7bf1-4460-982e-57ef3caa9ff3
Status: Created
Number of Bricks: 1 x 3 = 3
Transport-type: tcp
Bricks:
Brick1: node1.3glong.com:/glusterfs/sdb/rv2
Brick2: node2.3glong.com:/glusterfs/sdb/rv2
Brick3: node3.3glong.com:/glusterfs/sdb/rv2
Options Reconfigured:
performance.readdir-ahead: on
```

### 分布式条带卷
> 多个文件在多个节点哈希存储，每个文件再分条带在多个brick上存储
> 应用场景:   读/写性能高的大量大文件场景
> 优点：       高并发支持
> 缺点：       没有冗余，可靠性差

> brick 数是 stripe 的倍数

``` bash
[root@node1 ~]# gluster volume create dir_str_vol stripe 4 \
> node1.3glong.com:/glusterfs/sdb/dsv4 \
> node2.3glong.com:/glusterfs/sdb/dsv4 \
> node3.3glong.com:/glusterfs/sdb/dsv4 \
> node4.3glong.com:/glusterfs/sdb/dsv4 \
> node5.3glong.com:/glusterfs/sdb/dsv4 \
> node6.3glong.com:/glusterfs/sdb/dsv4 \
> node1.3glong.com:/glusterfs/sdc/dsv4 \
> node2.3glong.com:/glusterfs/sdc/dsv4
volume create: dir_str_vol: success: please start the volume to access data
[root@node1 ~]# gluster volume info dir_str_vol
 
Volume Name: dir_str_vol
Type: Distributed-Stripe
Volume ID: f05a1acb-ec28-40fd-aa57-c7562715a218
Status: Created
Number of Bricks: 2 x 4 = 8
Transport-type: tcp
Bricks:
Brick1: node1.3glong.com:/glusterfs/sdb/dsv4
Brick2: node2.3glong.com:/glusterfs/sdb/dsv4
Brick3: node3.3glong.com:/glusterfs/sdb/dsv4
Brick4: node4.3glong.com:/glusterfs/sdb/dsv4
Brick5: node5.3glong.com:/glusterfs/sdb/dsv4
Brick6: node6.3glong.com:/glusterfs/sdb/dsv4
Brick7: node1.3glong.com:/glusterfs/sdc/dsv4
Brick8: node2.3glong.com:/glusterfs/sdc/dsv4
Options Reconfigured:
performance.readdir-ahead: on
```
### 分布式复制卷
> 多个文件在多个节点哈希存储，在多个brick 复制多份存储。
> 应用场景:   大量文件读和可靠性要求高的场景
> 优点：       高可靠性，读性能高
> 缺点：       牺牲存储空间，写性能差

> brick 数量是 replica 的倍数
> 多个文件存储的时候在4个 brick 上哈希存储，每个文件存储 2份

``` bash
[root@node1 ~]# gluster volume create dir_rep_vol replica 2 \
> node1.3glong.com:/glusterfs/sdb/drv5 \
> node2.3glong.com:/glusterfs/sdb/drv5 \
> node3.3glong.com:/glusterfs/sdb/drv5 \
> node4.3glong.com:/glusterfs/sdb/drv5 \
> node5.3glong.com:/glusterfs/sdb/drv5 \
> node6.3glong.com:/glusterfs/sdb/drv5 \
> node1.3glong.com:/glusterfs/sdc/drv5 \
> node2.3glong.com:/glusterfs/sdc/drv5
volume create: dir_rep_vol: success: please start the volume to access data
[root@node1 ~]# gluster volume info dir_rep_vol
 
Volume Name: dir_rep_vol
Type: Distributed-Replicate
Volume ID: a3562200-8911-4c7f-bc2c-ce79c491236d
Status: Created
Number of Bricks: 4 x 2 = 8
Transport-type: tcp
Bricks:
Brick1: node1.3glong.com:/glusterfs/sdb/drv5
Brick2: node2.3glong.com:/glusterfs/sdb/drv5
Brick3: node3.3glong.com:/glusterfs/sdb/drv5
Brick4: node4.3glong.com:/glusterfs/sdb/drv5
Brick5: node5.3glong.com:/glusterfs/sdb/drv5
Brick6: node6.3glong.com:/glusterfs/sdb/drv5
Brick7: node1.3glong.com:/glusterfs/sdc/drv5
Brick8: node2.3glong.com:/glusterfs/sdc/drv5
Options Reconfigured:
performance.readdir-ahead: on
```
### 分布式条带复制卷
> 多个大文件在多个节点哈希存储，存储时分成条带，并且保存多份。
> 应用场景:   大量大文件并且对可靠性要求高的场景
> 优点：       读/写/可靠性比较均衡
> 缺点：       牺牲存储空间，写性能相对差

> brick 的数量是 stripe、replica 的乘积的倍数
> 多个文件存储的时候在 4 组 brick 上哈希存储，每个文件存储2份，并且划分条带。
```bash

[root@node1 ~]# gluster volume create dis_str_rep_vol stripe 2 replica 2 \
> node1.3glong.com:/glusterfs/sdb/dsrv6 \
> node2.3glong.com:/glusterfs/sdb/dsrv6 \
> node3.3glong.com:/glusterfs/sdb/dsrv6 \
> node4.3glong.com:/glusterfs/sdb/dsrv6 \
> node5.3glong.com:/glusterfs/sdb/dsrv6 \
> node6.3glong.com:/glusterfs/sdb/dsrv6 \
> node1.3glong.com:/glusterfs/sdb/dsrv6 \
> node2.3glong.com:/glusterfs/sdc/dsrv6
volume create: dis_str_rep_vol: success: please start the volume to access data
[root@node1 ~]# gluster volume info dis_str_rep_vol
 
Volume Name: dis_str_rep_vol
Type: Distributed-Striped-Replicate
Volume ID: 0c4596ed-988c-4b36-8955-c10beea7ddf3
Status: Created
Number of Bricks: 2 x 2 x 2 = 8
Transport-type: tcp
Bricks:
Brick1: node1.3glong.com:/glusterfs/sdb/dsrv6
Brick2: node2.3glong.com:/glusterfs/sdb/dsrv6
Brick3: node3.3glong.com:/glusterfs/sdb/dsrv6
Brick4: node4.3glong.com:/glusterfs/sdb/dsrv6
Brick5: node5.3glong.com:/glusterfs/sdb/dsrv6
Brick6: node6.3glong.com:/glusterfs/sdb/dsrv6
Brick7: node1.3glong.com:/glusterfs/sdc/dsrv6
Brick8: node2.3glong.com:/glusterfs/sdc/dsrv6
Options Reconfigured:
performance.readdir-ahead: on
```

### 条带复制卷
> 一个大文件存储的时候划分条带，并且保存多份。
> 应用场景:   超大文件，并且对可靠性要求高
> 优点：       大文件存储，可靠性高
> 缺点：       牺牲存储空间，写性能差

> brick 的数量是 stripe、replica 的乘积

``` bash
[root@node1 ~]# gluster volume create str_rep_vol stripe 2 replica 2 \
> node1.3glong.com:/glusterfs/sdb/srv7 \
> node2.3glong.com:/glusterfs/sdb/srv7 \
> node3.3glong.com:/glusterfs/sdb/srv7 \
> node4.3glong.com:/glusterfs/sdb/srv7
volume create: str_rep_vol: success: please start the volume to access data
[root@node1 ~]# gluster volume info str_rep_vol
 
Volume Name: str_rep_vol
Type: Striped-Replicate
Volume ID: ec0d735c-7183-4338-9efd-e11db55b72de
Status: Created
Number of Bricks: 1 x 2 x 2 = 4
Transport-type: tcp
Bricks:
Brick1: node1.3glong.com:/glusterfs/sdb/srv7
Brick2: node2.3glong.com:/glusterfs/sdb/srv7
Brick3: node3.3glong.com:/glusterfs/sdb/srv7
Brick4: node4.3glong.com:/glusterfs/sdb/srv7
Options Reconfigured:
performance.readdir-ahead: on
```
### 分散卷
`类似于RAID5`
> 分散卷基于校验码，它基于条带编码，并分布到多个brick上存储。
> 应用场景:   对冗余和磁盘空间都敏感的场景
> 优点：       在冗余和磁盘空间上取得平衡
> 缺点：       要求消耗额外的资源进行验证，对性能有一定的影响

> 分散卷需要指定冗余 brick 数理，冗余数要小于 brick 数量。例如创建一个 4 个brick 冗余的分散数，
> 最多允许1个brick故障而不丢失数据。

``` bash
[root@node1 ~]# gluster volume create disperse_vol disperse 4 \
> node1.3glong.com:/glusterfs/sdb/d8 \
> node2.3glong.com:/glusterfs/sdb/d8 \
> node3.3glong.com:/glusterfs/sdb/d8 \
> node4.3glong.com:/glusterfs/sdb/d8 
There isn't an optimal redundancy value for this configuration. Do you want to create the volume with redundancy 1 ? (y/n) y
volume create: disperse_vol: success: please start the volume to access data
[root@node1 ~]# gluster volume info disperse_vol
 
Volume Name: disperse_vol
Type: Disperse
Volume ID: 091d1eba-53fa-4f6c-9056-a31f94b7a441
Status: Created
Number of Bricks: 1 x (3 + 1) = 4
Transport-type: tcp
Bricks:
Brick1: node1.3glong.com:/glusterfs/sdb/d8
Brick2: node2.3glong.com:/glusterfs/sdb/d8
Brick3: node3.3glong.com:/glusterfs/sdb/d8
Brick4: node4.3glong.com:/glusterfs/sdb/d8
Options Reconfigured:
performance.readdir-ahead: on
```
### 分布式分散卷
`类似于RAID6`
> 分布分散卷，多个文件在多个节点上哈希分布存储，存储的时候基于条带校验码并有冗余度设置
> 应用场景:   对冗余和磁盘空间都敏感的场景
> 优点：       在冗余、磁盘空间、读/写性能上取得平衡
> 缺点：       要求消耗额外的资源进行验证，对性能有一定的影响

> 5个brick分布分散卷，最多允许2个brick故障而不数据丢失

```bash
[root@node1 ~]# gluster volume create disperse_vol3 disperse 3 \
> node1.3glong.com:/glusterfs/sdb/d9 \
> node2.3glong.com:/glusterfs/sdb/d9 \
> node3.3glong.com:/glusterfs/sdb/d9 \
> node4.3glong.com:/glusterfs/sdb/d9 \
> node5.3glong.com:/glusterfs/sdb/d9 \
> node6.3glong.com:/glusterfs/sdb/d9
volume create: disperse_vol3: success: please start the volume to access data
[root@node1 ~]# gluster volume info disperse_vol3
 
Volume Name: disperse_vol3
Type: Distributed-Disperse
Volume ID: a86a3491-701f-4ba8-8651-50d7b1bfc7b5
Status: Created
Number of Bricks: 2 x (2 + 1) = 6
Transport-type: tcp
Bricks:
Brick1: node1.3glong.com:/glusterfs/sdb/d9
Brick2: node2.3glong.com:/glusterfs/sdb/d9
Brick3: node3.3glong.com:/glusterfs/sdb/d9
Brick4: node4.3glong.com:/glusterfs/sdb/d9
Brick5: node5.3glong.com:/glusterfs/sdb/d9
Brick6: node6.3glong.com:/glusterfs/sdb/d9
Options Reconfigured:
performance.readdir-ahead: on

```