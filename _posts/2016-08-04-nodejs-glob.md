---
layout: post
title: nodejs glob
tags: [nodejs, glob]
---

[上一篇](http://lcat.me/2016/01/11/webpack%E5%85%A5%E9%97%A8/)

最近在开发多语言，多语言是通过指令 #translation('common.index', '首页')服务端解析的时候翻译

指令两个参数，第一个参数是namespace，第二个参数是缺省时默认值。

然后我想统计整站（某些模块）使用的多语言成一个json。下面是用`nodejs`实现

```js
var fs = require("fs");
var glob = require('glob');

var translationArr = []

// 获取所有vm后缀的文件地址数组
glob("path/**/*.vm", {}, function (error, files) {
  if (error) {
    return console.log(error);
  }
  var filesLen = files.length;
  // 遍历所有地址
  files.map(function(item, filesIndex) {
    // 读取文件
    fs.readFile(item, function (err, data) {
      if (err) {
        return console.log(err);
      }
      // 转为string
      var vmPageStr = data.toString();
      var matchArr = vmPageStr.match(/\#translation[^\)]*\)/g);

      translationArr = translationArr.concat(matchArr)
      // 循环最后一个的时候执行解析
      if (filesIndex === filesLen - 1) {
        parseData();
      }
    }
})

function parseData() {
  // 去重
  // 正则匹配
  // ...
}

```

通过glob可以匹配所有或者某些模块，同理也可以统计出某个多语言namespace有哪几个页面在用。
