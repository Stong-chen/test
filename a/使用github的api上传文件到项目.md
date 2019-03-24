---
layout: post
title:  "使用github的api上传文件到项目"
date:   2018-11-06 21:04:05
categories: 技术
tags: github git 上传文件

---

### 最初的想法

我用`github`搭建了我自己的个人博客网站。经常写博客的时候需要上传图片。我就想着把图片直接上传到github上面然后直接访问图片路径就可以了

最开始我想专门`clone`一份版本库。然后每一次需要上传一个文件的时候。都`add` `commit` ` push`一些列操作来上传一个文件上去。

后来发现`github`既然有`api`提供可以上传文件。那就不用这么麻烦啦。



### 创建token

> 想要调用`github`的`api`。那肯定要创建一个`token`才可以。不可能`github`的数据全部给你直接调用怎么行？

登录自己的账号。然后进入网站[https://github.com/settings/tokens](https://github.com/settings/tokens)

![创建token](https://stong-chen.github.io/file/Screenshot_5.png)

在这里选择`Generate new token`创建新的token

![创建token](https://stong-chen.github.io/file/Screenshot_6.png)

**下面这一步很重要。一定要复制你的`token`，它只显示一次**

![复制token](https://stong-chen.github.io/file/20181106150350.png)

这样你就得到了`token`。这个`token`对应的权限你还可以重新修改。但是这一条字符串这辈子不可能再给你先试一遍了。



### 调用api接口

> 在这里上传文件必须将文件`base64`的编码格式上传。然后自己指定文件上传的路径和名字
>
> 文档地址[https://developer.github.com/v3/repos/contents/](https://developer.github.com/v3/repos/contents/)

![上传图片](https://stong-chen.github.io/file/Screenshot_7.png)

接口地址[/repos/:owner/:repo/contents/:path](`/repos/:owner/:repo/contents/:path`)

提交类型 `put`

提交方式`Content-Type:application/json`

提交内容

```json
{
  "message": "提交说明",
  "committer": {
    "name": "名字",
    "email": "邮箱"
  },
  "content": "将文件base64编码后的字符串"
}
```



### 注意要点

1. 传输`token`有两种方式
   - 一种是URL里面传输也就是直接在URL上面传参数`?access_token=***************`
   - 一种是通过`header`头部来来传输`token` `Authorization`
2. 上传的文件必须被`base64`编码然后通过放在`content`里面
3. 必须在路径上面直接指定文件的上传路径。路径不存在`github`会自动创建


### 查考文章
- [https://segmentfault.com/a/1190000015144126](https://segmentfault.com/a/1190000015144126)
- [https://developer.github.com/v3/oauth_authorizations/#create-a-new-authorization](https://developer.github.com/v3/oauth_authorizations/#create-a-new-authorization)
- [https://developer.github.com/v3/](https://developer.github.com/v3/)
- [https://developer.github.com/v3/repos/contents/](https://developer.github.com/v3/repos/contents/)