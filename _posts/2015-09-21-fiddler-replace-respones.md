---
layout: post
title: Fiddler--替换response
tags: [Fiddler]
---

前端调试经常会遇到需要调试线上代码，chrome的控制台可以简单调试，但是刷新一下代码就恢复了。用fiddler可以替换请求的respones，就可以随便调试了。

比如我线上某个js需要大范围修改，我就可以拿到线上这个 js 的绝对路径，如http://example.com/js/a.js

把 a.js 拷到本地修改完。

* 打开fiddler，点击AutoResponder，并且勾选下面的`enable automatic respones` `Unmatched requests passthrough`

![fiddler](/public/images/2015-09-21/01.jpg)

* 点击 `Add Rule`

* 编辑下面的`Rule Editor` 

第一个输入要替换的线上地址，支持正则匹配；第二个输入要把第一个替换掉的地址

![fiddler](/public/images/2015-09-21/02.jpg)

* 设置浏览器代理为`127.0.0.1:8888`， 推荐[Proxy SwitchyOmega](https://chrome.google.com/webstore/detail/proxy-switchyomega/padekgcemlokbadohgkifijomclgjgif?utm_source=chrome-app-launcher-info-dialog)

![fiddler](/public/images/2015-09-21/03.jpg)

* 刷新页面，a.js 已经是替换掉的本地js了。