---
layout: post
title: Ubuntu开启wifi热点
category: linux
---

### 主要有三种方法
Using Ap-hotspot, an open-source app from github: see this post.
Using KDE connection editor, see the post.
Using Unity’s Default Network Manager with a little hack. See below

1. 使用ap-hotspot工具，不知为何一直在starting状态，貌似版本升级了不能用了(放弃)
2. 使用kde-nm-connection-editor，亲测可用，之前一直在用，最近突然坏了，怎么也连不上，原因未知
3. 使用unity自带的网络编辑器，只用做一点点微小的调整，方便可用

### 就直接转别人的了
[http://ubuntuhandbook.org/index.php/2014/09/3-ways-create-wifi-hotspot-ubuntu/](http://ubuntuhandbook.org/index.php/2014/09/3-ways-create-wifi-hotspot-ubuntu/)

其中最为关键的就是最后一部

	sudo gedit /etc/NetworkManager/system-connections/xxxxx
    改 mode=ap
