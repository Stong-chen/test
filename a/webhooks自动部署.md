---
layout: post
title:  "webhooks自动部署"
date:   2018-10-29 17:41:50
categories: 技术
tags: webhooks github gitee gitlib linux

---

开发`app`项目的时候，我们的接口是多人开发的。但是`app`调用接口又必须固定一个域名。为了多人协作开发。所以使用了`webhooks`的方法来运维项目。这个方法真的非常的方便。下面就和大家详细讲解一下





### 使用环境

一般的APP开发都是前后端分开的。前端工程师专门做前端的事情，后端工程师专门为前端提供接口。前端工程师看我们的接口文档来进行开发。这样就是有效的配合完成。
但是问题来了。如果只有一个后端工程师的话，我们可以有很多解决办法让我们的接口可以直接放前端访问。但是我们多人开发的时候怎么办啦？我们之前用的是ftp在线同步服务器上面的文件，这样的话，如果A和B同时需要修改同一个文件，那么就会后提交者就会把先提交者的代码给替换了。这样先提交者代码又不见了。因为这个问题一定要解决。
这里就需要用到`webhooks`的技术了。

### 环境需要

- gitee账号
- 项目部署服务器(linux centos7)

### 实现步骤

1. `gitee`创建项目，并配置`webhooks`
2. 服务器进入`www`用户
3. 生成一对秘钥
4. 克隆项目到网站目录
5. 编写`php`执行文件
6. 本地克隆项目
7. 测试推送，查看网站是否有变化

### 实现详细过程

#### 创建`webhooks`

![Screenshot_1](https://stong-chen.github.io/file/Screenshot_1.png)

![Screenshot_2](https://stong-chen.github.io/file/Screenshot_2.png)

> 这里需要输入一个`URL`地址。和一个密码。这个密码不是必须的。虽然我们不知道这个密码有什么用。等一下我们测试一下就知道了。

#### 服务器生成秘钥对

首先我们要搞清楚，你的`Nginx`到底是被哪个用户来执行的。

```shell
# 查询配置文件路径
find / -name nginx.conf

# 打印查询文件
more /www/server/nginx/conf/nginx.conf
```

> 通过查看配置文件，我知道我的执行用户是`www`

然后需要从`root`进入到`www`用户。生成一对秘钥

```shell
# 编辑用户文件
vim /etc/passwd

# 查询www用户在哪一行
# 在命令模式下执行
/www

# 查询到了这样的结果。则表示www用户不准登录
# www:x:1000:1000::/home/www:/sbin/nologin

# 修改www用户为可登陆用户
# 按i   进去输入模式
# 将/sbin/nologin 改为 /bin/bash
# www:x:1000:1000::/home/www:/bin/bash
# 按esc退出编辑模式
# :wq     保存退出

# 切换到www用户
su www

# 创建秘钥对
ssh-keygen -t rsa -C "811403726@qq.com"
# 狂按回车就可以了

# 查看公钥
cat ~/.ssh/id_rsa.pub
```

照着上面的操作。我们得到了一串字符串。这是公钥。我们需要在项目的ssh里面添加这一条公钥。

![5bed8841c23da](https://raw.githubusercontent.com/Stong-chen/Stong-chen.github.io/master/file/2018-11-15/5bed8841c23da.png)

将公钥粘贴在这里



#### 服务器克隆项目

然后在`www`用户下。将这个项目克隆下来

```shell
git clone git@gitee.com:Z_Chen/test.git

# 将文件夹的权限修改一下
chmod -R 755 test/
```

现在已经不需要在`www`用户里面去了。回到我们的`root`

```shell
su 
# 输入密码就可以回到root
```

修改`/etc/passwd`文件，将`www`用户改为不登录

```shell
vim /etc/passwd

# 改为
# www:x:1000:1000::/home/www:/sbin/nologin
```



#### 编辑`webhooks`的请求文件

在`https://test.zrongdong.com/`网页创建`test.php`的文件。然后接受参数。并将参数保存起来

```php
$f = fopen('log.txt', 'a');
fwrite($f, file_get_contents('php://input') . PHP_EOL);
fclose($f);
```

点击测试

![Screenshot_3](https://stong-chen.github.io/file/Screenshot_3.png)

解析`json`数据发现，刚才我们输入的`password`就携带在`json`数据的`password`里面。我们就可以通过这个来做验证，是否为我们`gitee`的请求数据

> 这里我只是提供一个方法。如果你用的不是`gitee`。用的是`github`或者`gitlab`。都可以通过这个方法来找出你出入的那个密码到底被放在哪里用来请求你的。



然后你可以用这个`password`来做安全验证。下面我就不做了。简单点

```php
exec("cd /www/test && git fetch && git reset --hard origin/master", $array);
print_r($array);
```

现在。你再测试`webhooks`。然后`php`就会帮你自动的将`git`上面的最新版本给拉下来。也不会出现什么冲突的操作。



以后你的每一次`push`都会在服务器上面自动部署了。是不是非常方便



### 总结

学会使用这个方法，会让你的项目部署事半功倍。以后部署项目就不用这么麻烦了。团队协作开发也更加方便了。



### 参考文章

> 在`google`搜索**webhooks 自动部署**很多相关的文章的。



### 原文声明

> 作者:张晨
>
> email:93593814@qq.com