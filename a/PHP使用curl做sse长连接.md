---
layout: post
title:  "PHP使用curl做sse长连接"
date:   2018-08-16 14:50:42
categories: 技术
tags: php curl sse 

---


最近使用到sse的技术。在php里面使用curl来完成sse还是挺简单的。但是最近碰到一个问题。我找了好久才找到解决办法。下面记录一下。




## php使用curl的完整代码
```php
$url = '';    // 一个支持sse的地址
$ch = curl_init($url);
curl_setopt($ch, CURLOPT_WRITEFUNCTION, function($ch,$data){
    $data_len = strlen($data);
    // .......
    return $data_len;    // 必须返回接收到的数据的长度。不然就会被断开连接
});
// 执行
curl_exec($ch);    // 程序会停在这里。然后就开始一直调用上面你写的方法了。


//关闭连接
curl_close($ch);
```

但是现在遇到一个问题就是。如果我一直和我的服务器保持连接没问题。我们一直可以沟通。那要是我们断网了啦？遇到问题了怎么办？它既然动都不动。继续等待哦。这得等到什么时候才可以啊？所以就需要增加多几个参数。


### 解决如下
```php
$url = '';    // 一个支持sse的地址
$ch = curl_init($url);
// 检查是否或者。每10秒发送一次心跳
curl_setopt($ch,CURLOPT_TCP_KEEPALIVE,1);   // 开启
curl_setopt($ch,CURLOPT_TCP_KEEPIDLE,10);   // 空闲10秒问一次
curl_setopt($ch,CURLOPT_TCP_KEEPINTVL,10);  // 每10秒问一次

curl_setopt($ch, CURLOPT_WRITEFUNCTION, function($ch,$data){
    $data_len = strlen($data);
    // .......
    return $data_len;    // 必须返回接收到的数据的长度。不然就会被断开连接
});
// 执行
curl_exec($ch);    // 程序会停在这里。然后就开始一直调用上面你写的方法了。


//关闭连接
curl_close($ch);
```

这样就可以觉得问题了，curl就会定时自动去判断到底还在不在线。如果不在线了，已经断了。那么就会跳出`curl_exec()`。

你可以用`curl_error()`打出错误来看看


## 参考文档
* [https://www.cnblogs.com/cswuyg/p/3653263.html](https://www.cnblogs.com/cswuyg/p/3653263.html)
* [https://curl.haxx.se/libcurl/c/CURLOPT_TCP_KEEPIDLE.html](https://curl.haxx.se/libcurl/c/CURLOPT_TCP_KEEPIDLE.html)
* [https://www.html5rocks.com/en/tutorials/eventsource/basics/#toc-reconnection-timeout](https://www.html5rocks.com/en/tutorials/eventsource/basics/#toc-reconnection-timeout)
* [http://www.runoob.com/php/func-curl_setopt.html](http://www.runoob.com/php/func-curl_setopt.html)