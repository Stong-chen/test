---
layout: post
title:  "使用ssh秘钥登录linux"
date:   2018-12-05 10:46:02
categories: 技术
tags: linux ssh rsa

---

我想大家和我一样经常会用到`putty`登录自己的linux系统。每一次都要输入密码，觉得很麻烦。而且我还觉得不安全。一直想配置通过秘钥来登录，就可以不输入密码了。方便很多。下面就是我的配置过程。





## 实现原理
用户在本地生成自己的秘钥对，然后将公钥保存到远程主机上。登录的时候，远程主机会向用户发送一段随机字符串，用户用自己的私钥加密后，再发回去。远程主机用事先储存的公钥进行解密，如果成功，就证明用户是可信的，直接允许登录shell，不再要求密码。[（摘至阮一峰ssh原理与应用）](http://www.ruanyifeng.com/blog/2011/12/ssh_remote_login.html)

## 实现过程
### 生成秘钥对

- **已有自己的秘钥对**

> 很多开发人员在本地都已经有自己的秘钥对了。所以不需要再重新生成一对秘钥。、

使用[PuTTY](https://www.ssh.com/ssh/putty/download)的`PuTTYgen`工具将你已有的私钥转为ppk格式的文件。
![](https://raw.githubusercontent.com/Stong-chen/Stong-chen.github.io/master/file/2018-12-05/5c07453bb4ed0.png)

加载你已有的私钥(id_rsa)。选择的使用所有类型的文件。打开之后可以看到你的秘钥信息。然后点击*save private key*保存你的私钥。按照ppk格式保存

- **使用puttygen创建一个秘钥对**

直接使用`puttygen`这个工具帮我们创建一个秘钥对（我不知道为什么那么慢）

![puttygen](https://raw.githubusercontent.com/Stong-chen/Stong-chen.github.io/master/file/2018-12-05/5c074483662f8.png "puttygen")

将公钥复制下来保存到文本文档或者其他地方(等一下有用)
将私钥保存为ppk的格式。等一下有用

### 配置服务器
先使用密码登录你的Linux服务器。然后配置`/etc/ssh/sshd_config`文件。
```
vi /etc/ssh/sshd_config
```
配置成如下
```shell
RSAAuthentication yes
PubkeyAuthentication yes
# root用户也可以通过ssh登录
PermitRootLogin yes
```
> 这三项一般都是配置正确的。你看一下是否和我的一样就可以了

将公钥配置到`authorized_keys`
```
# 编辑用户目录下面的文件
vi ~/.ssh/authorized_keys
# 然后将你的公钥复制进去
# 如果里面已经有其他的公钥，就回车一行添加你的公钥
# 如果你没有这个目录和文件。执行如下
# ssh-keygen -t rsa
# 然后狂按回车。中途你想设置密码就不要狂按回车。慢慢按回车
```
重新ssh服务
```shell
service sshd restart
```

### putty私钥登录
![](https://raw.githubusercontent.com/Stong-chen/Stong-chen.github.io/master/file/2018-12-05/5c074c9ab005c.png)
点击open。然后看看你是否直接就进去了



## 参考文章
* [http://www.ruanyifeng.com/blog/2011/12/ssh_remote_login.html](http://www.ruanyifeng.com/blog/2011/12/ssh_remote_login.html)
* [https://hyjk2000.github.io/2012/03/16/how-to-set-up-ssh-keys/](https://hyjk2000.github.io/2012/03/16/how-to-set-up-ssh-keys/)