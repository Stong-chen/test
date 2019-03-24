---
layout: post
title:  "Apache地址重新问题"
date:   2018-09-02 14:09:03
categories: 技术
tags: Apache mod_rewrite .htaccess

---

首先，我们想实现的功能是，在访问thinkphp框架的时候，我们不用在url里面输入index.php这个文件了，直接让apache默认的帮我们加上去。试了很久一直没有成功，一直提示`no input file specified`真的是醉了。后来找到一个大神的解决方案搞定了这个问题。




### URL重写
这是[thinkphp官网](https://www.kancloud.cn/manual/thinkphp/1866)提供的方法

1. httpd.conf配置文件中加载了mod_rewrite.so模块
2. AllowOverride None 将None改为 All
3. 把下面的内容保存为.htaccess文件放到应用入口文件的同级目录下

```
<IfModule mod_rewrite.c>
 RewriteEngine on
 RewriteCond %{REQUEST_FILENAME} !-d
 RewriteCond %{REQUEST_FILENAME} !-f
 RewriteRule ^(.*)$ index.php/$1 [QSA,PT,L]
</IfModule>
```

### 出现问题
当我这样配置好了之后，我去掉`index.php`访问网站。网站还是提示了一个错误`no input file specified`

这到底是为什么啦？明明按照网上的方法都配置好了啊。我再继续搜索。
[这篇文章](http://phpstudy.php.cn/a.php/165.html)解决了我们的问题。

文章里面说啊。我们上面写的规则，在`fastcgi`模式下，就会出错。刚好。我们的模式就是`fastcgi`这个模式。所以我们按照他的方法修改

```
<IfModule mod_rewrite.c>
  Options +FollowSymlinks
  RewriteEngine On

  RewriteCond %{REQUEST_FILENAME} !-d
  RewriteCond %{REQUEST_FILENAME} !-f
  RewriteRule ^(.*)$ index.php [L,E=PATH_INFO:$1]
</IfModule>
```
没毛病，成功修改了我的问题。完美。

### 参考文章
[http://www.thinkphp.cn/topic/30538.html](http://www.thinkphp.cn/topic/30538.html)
[https://www.kancloud.cn/manual/thinkphp/1866](https://www.kancloud.cn/manual/thinkphp/1866)
[http://phpstudy.php.cn/a.php/165.html](http://phpstudy.php.cn/a.php/165.html)