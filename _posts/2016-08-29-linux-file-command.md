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
## 文件操作命令
### 创建一个空文件命令 touch
- 详细用法：
```bash
linux-vnja: # touch --help
用法：touch 选项 ()... 文件...
Update the access and modification times of each FILE to the current time.

A FILE argument that does not exist is created empty, unless -c or -h
is supplied.

A FILE argument string of - is handled specially and causes touch to
change the times of the file associated with standard output.

Mandatory arguments to long options are mandatory for short options too.
-a只更改访问时间
-c, --no-create不创建任何文件
-d, --date=字符串使用指定字符串表示时间而非当前时间
-f(忽略)
-h, --no-dereference会影响符号链接本身，而非符号链接所指示的目的地
(当系统支持更改符号链接的所有者时，此选项才有用)
-m只更改修改时间
-r, --reference=FILE   use this file's times instead of current time
-t STAMP               use [CC ()YY]MMDDhhmm.ss () instead of current time
--time=WORD        change the specified time:
WORD is access, atime, or use: equivalent to -a
WORD is modify or mtime: equivalent to -m
--help显示此帮助信息并退出
--version显示版本信息并退出

请注意，-d 和-t 选项可接受不同的时间/日期格式。
实例：
linux-vnja: # touch zhou.txt             #创建一个文件
linux-vnja: # touch 1.txt 2.txt            #创建两个文件
linux-vnja: # touch /home/text.mp5        #在home下创建text.mp5文件
linux-vnja: # touch 3..9glong.txt           #创建3glong.txt，4glong.txt一直到9glong.txt
linux-vnja: # touch 3glong.com,cn,org,cc
linux-vnja: # ls
1.txt      3glong.com  5glong.txt  9glong.txt     inst-sys  .viminfo
2.txt      3glong.org  6glong.txt  .bashhistory  .local    .Xauthority
3glong.cc  3glong.txt  7glong.txt  bin            test      zhou.txt
3glong.cn  4glong.txt  8glong.txt  .gnupg         test.txt
```
### 创建目录命令 mkdir
	常用的也就一个-p参数    递归创建
	实例：  mkdir -p 1/2/3
	
### 复制命令  cp
- 用法：
	
``` bash
cp 选项 ()... -T () 源文件 目标文件
或：cp 选项 ()... 源文件... 目录
或：cp 选项 ()... -t 目录 源文件...
Copy SOURCE to DEST, or multiple SOURCE(s) to DIRECTORY.

Mandatory arguments to long options are mandatory for short options too.
  -a, --archive 等于-dR --preserve=all
  --attributes-only 仅复制属性而不复制数据      --backup=CONTROL 为每个已存在的目标文件创建备份
 ()  -b 类似--backup 但不接受参数
  --copy-contents 在递归处理是复制特殊文件内容
  -d 等于--no-dereference --preserve=links
  -f, --force                  if an existing destination file cannot be
 opened, remove it and try again (this option
 is ignored when the -n option is also used)
  -i, --interactive            prompt before overwrite (overrides a previous -n
  option)
  -H                           follow command-line symbolic links in SOURCE
  -l, --link                   hard link files instead of copying
  -L, --dereference            always follow symbolic links in SOURCE
  -n, --no-clobber 不要覆盖已存在的文件(使前面的 -i 选项失效)
  -P, --no-dereference 不跟随源文件中的符号链接
  -p 等于--preserve=模式,所有权,时间戳
  --preserve=属性列表 保持指定的属性(默认：模式,所有权,时间戳)，如果
 ()可能保持附加属性：环境、链接、xattr 等
  --sno-preserve=属性列表 不保留指定的文件属性
  --parents 复制前在目标目录创建来源文件路径中的所有目录
  -R, -r, --recursive 递归复制目录及其子目录内的所有内容
  --reflink=WHEN () 控制克隆/CoW 副本。请查看下面的内如。
  --remove-destination 尝试打开目标文件前先删除已存在的目的地
文件 (相对于 --force 选项)
  --sparse=WHEN 控制创建稀疏文件的方式
  --strip-trailing-slashes 删除参数中所有源文件/目录末端的斜杠
  -s, --symbolic-link 只创建符号链接而不复制文件
  -S, --suffix=后缀 自行指定备份文件的后缀
  -t,  --target-directory=目录 将所有参数指定的源文件/目录
   复制至目标目录
  -T, --no-target-directory 将目标目录视作普通文件
  -u, --update 只在源文件比目标文件新，或目标文件
不存在时才进行复制
  -v, --verbose 显示详细的进行步骤
  -x, --one-file-system 不跨越文件系统进行操作
  -Z, --context=CTX ()          set SELinux security context of destination
 file to default type, or to CTX if specified
  --help 显示此帮助信息并退出
  --version 显示版本信息并退出

默认情况下，源文件的稀疏性仅仅通过简单的方法判断，对应的目标文件目标文件也
被为稀疏。这是因为默认情况下使用了--sparse=auto 参数。如果明确使用
--sparse=always 参数则不论源文件是否包含足够长的0 序列也将目标文件创文
建为稀疏件。
使用--sparse=never 参数禁止创建稀疏文件。

当指定了--reflink=always () 参数时执行轻量化的复制，即只在数据块被修改的
情况下才复制。如果复制失败或者同时指定了--reflink=auto，则返回标准复制模式。

The backup suffix is '', unless set with --suffix or SIMPLEBACKUPSUFFIX.
The version control method may be selected via the --backup option or through
the VERSIONCONTROL environment variable.  Here are the values:

  none, off       不进行备份(即使使用了--backup 选项)
  numbered, t     备份文件加上数字进行排序
  existing, nil   若有数字的备份文件已经存在则使用数字，否则使用普通方式备份
  simple, never   永远使用普通方式备份

有一个特别情况：如果同时指定--force 和--backup 选项，而源文件和目标文件
是同一个已存在的一般文件的话，cp 会将源文件备份。
实例：
linux-vnja: # cd /home
linux-vnja:/home # ls
long  suse  test  text.mp5
linux-vnja:/home # ls test
.bashhistory  bin  etc  .gnupg  inst-sys  .local  .snapshots  test  .viminfo
linux-vnja:/home # mkdir cpcp
linux-vnja:/home # ls
cpcp  long  suse  test  text.mp5
linux-vnja:/home # cp -v text.mp5 cpcp              #拷贝并显示过程
"text.mp5" -> "cpcp/text.mp5"
linux-vnja:/home # ls cpcp
text.mp5
linux-vnja:/home # cp -v text.mp5 cpcp/text.mp4      #拷贝并重命名
"text.mp5" -> "cpcp/text.mp4"
linux-vnja:/home # ls cpcp
text.mp4  text.mp5
linux-vnja:/home # cp -r test cpcp            #拷贝整个文件夹
linux-vnja:/home # ls cpcp/test/
.bashhistory  bin  etc  .gnupg  inst-sys  .local  .snapshots  test  .viminfo
linux-vnja:/home # ls
cpcp  long  suse  test  text.mp5
linux-vnja:/home # ls suse
.bashhistory  bin      .emacs  .inputrc  .profile
.bashrc        .config  .fonts  .local
linux-vnja:/home # cp cpcp/text.mp4 cpcp/text.mp5 suse      #拷贝两个文件
linux-vnja:/home # ls suse
.bashhistory  bin      .emacs  .inputrc  .profile  text.mp5
.bashrc        .config  .fonts  .local    text.mp4
```
### 移动重命名命令  mv

- 用法：

``` bash
mv 选项 ()... -T () 源文件 目标文件
或：mv 选项 ()... 源文件... 目录
或：mv 选项 ()... -t 目录 源文件...
Rename SOURCE to DEST, or move SOURCE(s) to DIRECTORY.

Mandatory arguments to long options are mandatory for short options too.
  --backup=CONTROL ()       为每个已存在的目标文件创建备份
  -b                           类似--backup 但不接受参数
  -f, --force                  覆盖前不询问
  -i, --interactive            覆盖前询问
  -n, --no-clobber             不覆盖已存在文件
如果您指定了-i、-f、-n 中的多个，仅最后一个生效。
  --strip-trailing-slashes 去掉每个源文件参数尾部的斜线
  -S, --suffix=SUFFIX 替换常用的备份文件后缀
  -t, --target-directory=DIRECTORY  move all SOURCE arguments into DIRECTORY
  -T, --no-target-directory    treat DEST as a normal file
  -u, --update                 move only when the SOURCE file is newer
 than the destination file or when the
 destination file is missing
  -v, --verbose                explain what is being done
  -Z, --context                set SELinux security context of destination
 file to default type
  --help 显示此帮助信息并退出
  --version 显示版本信息并退出

实例：
linux-vnja:/home # ls
cpcp  long  suse  test  text.mp5
linux-vnja:/home # mv text.mp5 text.mp8            #重命名
linux-vnja:/home # ls
cpcp  long  suse  test  text.mp8         
linux-vnja:/home # mv text.mp8 suse                 #移动到sure目录
linux-vnja:/home # ls
cpcp  long  suse  test
linux-vnja:/home # ls sure
.bashhistory  bin      .emacs  .inputrc  .profile  text.mp5
.bashrc        .config  .fonts  .local    text.mp4  text.mp8
linux-vnja:/home # mv suse/text.mp5 .        #移动到当前目录
linux-vnja:/home # ls
cpcp  long  suse  test  text.mp5

```

### 删除文件命令rm

- 用法：

```bash
rm 选项 ()... 文件...
Remove (unlink) the FILE(s).

  -f, --force           ignore nonexistent files and arguments, never prompt
  -i                    prompt before every removal
  -I                    prompt once before removing more than three files, or
  when removing recursively; less intrusive than -i,
  while still giving protection against most mistakes
  --interactive=WHEN ()  prompt according to WHEN: never, once (-I), or
  always (-i); without WHEN, prompt always
  --one-file-system 递归删除一个层级时，跳过所有不符合命令行参
数的文件系统上的文件
  --no-preserve-root  do not treat '/' specially
  --preserve-root   do not remove '/' (default)
  -r, -R, --recursive   remove directories and their contents recursively
  -d, --dir             remove empty directories
  -v, --verbose         explain what is being done
  --help 显示此帮助信息并退出
  --version 显示版本信息并退出

具体用法与mv类似
linux-vnja:/home # ls           #列出当前目录文件
cpcp  long  suse  test  text.mp5
linux-vnja:/home # cd cpcp           切换到cvcp目录
linux-vnja:/home/cpcp # ls
test  text.mp4  text.mp5
linux-vnja:/home/cpcp # rm -f text.m      #删除以text.m开头的文件
linux-vnja:/home/cpcp # ls
test
linux-vnja:/home/cpcp # rm -rf text          #文件夹名输错也没提示
linux-vnja:/home/cpcp # ls
test
linux-vnja:/home/cpcp # rm -rf test/           #删除test目录包括所有子目录文件（无交互）
linux-vnja:/home/cpcp # ls
linux-vnja:/home/cpcp #
```

## 查看文件命令
### 常用查看文件命令
- cat   -n 显示行号
  -A 包括控制字符（换行符/制表符）less
- more
- less
- head （查看前10行）     默认显示10行 加-n  指定行数
- tail     （查看后10行）     同head
- grep    过滤文件内容
实例：

``` bash
linux-vnja:/home # cd suse
linux-vnja:/home/suse # ls
.bashhistory  bin      .emacs  .inputrc  .profile  text.mp8
.bashrc        .config  .fonts  .local    text.mp4
linux-vnja:/home/suse # tail .bashrc         #默认查看后10行
export NEWSSERVER=your.news.server

If you want to use a Palm device with Linux, uncomment the two lines below.
For some (older) Palm Pilots, you might need to set a lower baud rate
e.g. 57600 or 38400; lowest is 9600 (very slow!)

export PILOTPORT=/dev/pilot
export PILOTRATE=115200

test -s /.alias && . /.alias linux-vnja:/home/suse # tail -4 .bashrc           #查看后4行
export PILOTPORT=/dev/pilot
export PILOTRATE=115200

test -s /.alias && . /.alias linux-vnja:/home/suse # cat .bashrc          #cat会在当前终端显示文件所有内容
Sample .bashrc for SuSE Linux
Copyright (c) SuSE GmbH Nuernberg

There are 3 different types of shells in bash: the login shell, normal shell
and interactive shell. Login shells read /.profile and interactive shells
read /.bashrc; in our setup, /etc/profile sources /.bashrc - thus all
settings made here will also take effect in a login shell.

NOTE: It is recommended to make language settings in /.profile rather than
here, since multilingual X sessions would not work properly if LANG is over-
ridden in every subshell.

Some applications read the EDITOR variable to determine your favourite text
editor. So uncomment the line below and enter the editor of your choice :-)
export EDITOR=/usr/bin/vim
export EDITOR=/usr/bin/mcedit

For some news readers it makes sense to specify the NEWSSERVER variable here
export NEWSSERVER=your.news.server

If you want to use a Palm device with Linux, uncomment the two lines below.
For some (older) Palm Pilots, you might need to set a lower baud rate
e.g. 57600 or 38400; lowest is 9600 (very slow!)

export PILOTPORT=/dev/pilot
export PILOTRATE=115200

test -s /.alias && . /.alias linux-vnja:/home/suse # grep '/usr/bin/vim' .bashrc
export EDITOR=/usr/bin/vim
```