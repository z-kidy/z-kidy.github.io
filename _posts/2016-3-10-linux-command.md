---
layout: post
title: Linux个人笔记,常用指令
category: linux
---



## Linux系统中的/proc文件系统有什么用？

> /proc文件系统是一个基于内存的文件系统，其维护着关于当前正在运行的内核状态信息，其中包括CPU、内存、分区划分、I/O地址、直接内存访问通道和正在运行的进程。这个文件系统所代表的并不是各种实际存储信息的文件，它们指向的是内存里的信息。/proc文件系统是由系统自动维护的。

## env

查看环境变量

## find

>搜索文件及目录，在特定的目录下搜索并显示指定名称的文件和目录 
    
    find / -name man：意思是说从根目录开始搜索名称为man的文件或目录 
    
    find /home -amin -10：十分钟内存取的文件或目录 
    find /home -atime -10：十小时内存取的文件或目录 
    find /home -cmin -10：十分钟内更改过的文件或目录 
    find /home -ctime +10：十小时前更改过的文件或目录 
    find /home -mtime -10：十天内更改过的文件或目录 
    
    find /usr -size +10M: /usr目录下大小大于10m的文件或目录
    
    find / -name core -exec rm {} \;  在整个目录树下查找文件"core"，如发现则无需提示直接删除它们

    find / -type f | wc -l  统计指定目录的文件个数
    
    
## 如何查看Linux的默认网关？

+ route -n
+ netstat -nr

>我们可以查看默认网关。除了默认的网关信息，这两个命令还可以显示当前的路由表。

<!-- more -->

## 特殊字符 \, "" , '', `` 

+ 转义字符\ 
    
    ls /mnt/win1/My\Documents 

+ 单引号：不处理任何变量和命令 
    
    echo 'Welcome $NAME, the date is date' 

+ 双引号：处理变量但不处理命令 
    
    echo "Welcome $NAME, the date is date" 

+ 反引号：把引号中的每个单词作为一个命令，如果是变量则先求值然后作为一个命令处理 
    
    echo "Welcome $NAME, the date is `date`"

## awk
> awk是一个强大的文本分析工具，相对于grep的查找，sed的编辑，awk在其对数据分析并生成报告时，显得尤为强大。简单来说awk就是把文件逐行的读入，以空格为默认分隔符将每行切片，切开的部分再进行各种分析处理。

使用方法 awk '{pattern + action}' {filenames}

假设last -n 5的输出如下

    [root@www ~]# last -n 5 <==仅取出前五行
    root     pts/1   192.168.1.100  Tue Feb 10 11:21   still logged in
    root     pts/1   192.168.1.100  Tue Feb 10 00:46 - 02:28  (01:41)
    root     pts/1   192.168.1.100  Mon Feb  9 11:41 - 18:30  (06:48)
    dmtsai   pts/1   192.168.1.100  Mon Feb  9 11:41 - 11:41  (00:00)
    root     tty1                   Fri Sep  5 14:09 - 14:10  (00:01)
    如果只是显示最近登录的5个帐号

    #last -n 5 | awk  '{print $1}'
    root
    root
    root
    dmtsai
    root

[参考链接](http://www.cnblogs.com/ggjucheng/archive/2013/01/13/2858470.html)



## 查阅历史记录 

- history，查看使用过的命令的历史记录 
- history 5，此项说明会显示最近使用的5个命令 
- !5，此项说明执行历史编号为5的命令 
- !ls，此项说明执行最后一次以“ls”开头的命令


## 文件权限 
    
    ls -l中显示的内容如下： 
    -rwxrw-r‐-1 root root 1213 Feb 2 09:39 abc 

- 10个字符确定不同用户能对文件干什么 
- 第一个字符代表文件（-）、目录（d），链接（l） 
- 其余字符每3个一组（rwx），读（r）、写（w）、执行（x） 
- 第一组rwx：文件所有者的权限是读、写和执行 
- 第二组rw-：与文件所有者同一组的用户的权限是读、写但不能执行 
- 第三组r--：不与文件所有者同组的其他用户的权限是读不能写和执行 

也可用数字表示为：r=4，w=2，x=1 因此rwx=4+2+1=7 

- 1 表示连接的文件数 
- root 表示用户 
- root表示用户所在的组 
- 1213 表示文件大小（字节） 
- Feb 2 09:39 表示最后修改日期 
- abc 表示文件名 

## 改变权限的命令 

    chmod 改变文件或目录的权限 
    chmod 755 abc：赋予abc权限rwxr-xr-x 
    chmod u=rwx，g=rx，o=rx abc：同上u=用户权限，g=组权限，o=不同组其他用户权限 
    chmod u-x，g+w abc：给abc去除用户执行的权限，增加组写的权限 
    chmod a+r abc：给所有用户添加读的权限 

    改变所有者（chown）和用户组（chgrp）命令 

    chown xiaoming abc：改变abc的所有者为xiaoming 
    chgrp root abc：改变abc所属的组为root 
    chown root ./abc：改变abc这个目录的所有者是root 
    chown ‐R root ./abc：改变abc这个目录及其下面所有的文件和目录的所有者是root 

    改变用户所在组 

    在添加用户时，可以指定将该用户添加到哪个组中，同样用root的管理权限可以改变某个用户所在的组 

    - usermod ‐g 组名 用户名 
    你可以用 - usermod ‐d 目录名 用户名，改变该用户登录的初始目录
    
## 挂载命令

    mount [-parameters] [设备名称] [挂载点] 
    umount [挂载点]
    
## grep，在文本中查询内容 
 
      
      netstat -tanp | grep :80  我常用此命令查看80端口占用情况
      
      
## netstat

> Netstat 命令用于显示各种网络相关信息，如网络连接，路由表，接口状态 (Interface Statistics)，masquerade 连接，多播成员 (Multicast Memberships) 等等。

http://www.cnblogs.com/ggjucheng/archive/2012/01/08/2316661.html

## ps aux

+ a 显示所有与终端相关的进程,由终端发起的.

+ x 显示所有与终端无关的进程.

+ u 显示用户导向的用户列表.

+ VSZ 虚拟内存集,进程占用的虚拟内存空间
+ RSS     物理内存集,进程战用实际物理内存空间.

## 符号链接和硬链接的区别

ln 1.txt 2.txt

>硬链接:不同路径的文件指定的是同一个inode
硬链接不能跨分区存在
硬链接不能链接到目录(要避免循环引用)
>删除硬链接只会减少文件被硬链接的次数,源文件不会动.

修改１，２会跟着变，　但删除１，２不受影响

ln -s 1.txt 2.txt

>软链接(符号链接):是一个独立的文件,有自己独立的inode
引用的是路径本身,不是inode
删除软链接,跟源文件没任何关系
软链接没有存储任何数据,只是存储了访问文件的另一种路径.
软链接可以对目录创建,也可以跨分区
>源文件被删除,软连接将不可用.

相当windows下的快捷方式，修改２，１会跟着变，但删除１，２就坏了，不可再用


