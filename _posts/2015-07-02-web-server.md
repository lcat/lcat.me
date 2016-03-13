---
layout: post
title: 快速搭建web服务器
tags: [web, server]
---

之前搭建本地服务器都是xampp等等，安装麻烦切庞大。如今nodejs神奇的几行代码就可以搭一个服务器了。为了更简单，我们直接用开源库[http-server](https://www.npmjs.com/package/http-server)

修改package.json如下：

```js
{
  "devDependencies": {
    "http-server": "~2.8.1"
  },
  "script": {
    "start": "node node_modules/http-server/bin/http-server -o -p 80"
  }
}
// -o 浏览器打开;-p 指定端口
```

然后执行 

> $ cnpm install

> $ npm start

一切顺利的话浏览器会打开127.0.0.1  

完~