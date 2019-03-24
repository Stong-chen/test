---
layout: post
title:  "php知识点"
date:   2018-08-06 10:31:02
categories: php
tags: php 笔记

---

这里记录一些php经常可能会使用到的一些小方法或者函数的使用方法之类的。总的来说，就是为了让以后遇到同类问题可以快速的查找到。





* 在php开始出设置中国时区，避免后面用到时间函数出错
```php
ini_set('date.timezone','Asia/Shanghai');    //设置亚洲上海时间
function_exists('date_default_timezone_set') && date_default_timezone_set('PRC'); //设置中国时区
```