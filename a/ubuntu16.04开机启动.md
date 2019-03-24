---
layout: post
title:  "ubuntu16.04开机启动"
date:   2018-08-08 14:23:09
categories: ubuntu
tags: ubuntu 开机启动 linux

---

我需要在ubuntu开机的时候，就将我的ss打开。免得我还需要去打一条命令才能开启，这样会觉得很麻烦。如果我将这个命令加入开启启动。那么就不怕了。以后我开机就可以直接科学上网了。




## ubuntu16.04开机启动

* 新建一个文件`shadowsocks_start.sh` <br >
> 前面这些注解是必须加入的，好像是16.04规定的。我也不是很清楚，总之加进去了总是没错的。最后一条命令就是我的ss启动命令了good

```shell
#!/bin/sh
### BEGIN INIT INFO
# Provides:          svnd.sh
# Required-start:    $local_fs $remote_fs $network $syslog
# Required-Stop:     $local_fs $remote_fs $network $syslog
# Default-Start:     2 3 4 5
# Default-Stop:      0 1 6
# Short-Description: starts the svnd.sh daemon
# Description:       starts svnd.sh using start-stop-daemon
### END INIT INFO
sslocal -c /etc/shadowsocks.json -d start
```

* 将文件复制到启动目录下面去并设置权限

```shell
cp ./shadowsocks_start.sh /etc/init.d/shadowsocks_start.sh
chmod 777 /etc/init.d/shadowsocks_start.sh
```

* 将文件加入开机启动队列，并设置启动的优先级

```shell
sudo update-rc.d shadowsocks_start.sh defaults 99
```

如果没有提示错误，那么就是设置成功了。我这是的优先级是99。是比较低的优先级，让别人先启动了我再启动也不急。

如果想要取消开机启动
```shell
sudo update-rc.d -f shadowsocks_start.sh remove
```


## 参考文档
* [https://www.linuxidc.com/Linux/2017-09/147178.htm](https://www.linuxidc.com/Linux/2017-09/147178.htm)
* [http://www.linuxdiyf.com/linux/26896.html](http://www.linuxdiyf.com/linux/26896.html)