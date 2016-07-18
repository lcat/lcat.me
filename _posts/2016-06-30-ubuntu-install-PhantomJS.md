---
layout: post
title: ubuntu 安装 PhantomJS
tags: [ubuntu, PhantomJS]
---

[http://phantomjs.org/download.html](官网下载)最新 .tar.bz2

这里下载的是`phantomjs-2.1.1-linux-x86_64`

> cd path/to/
> sudo tar xvjf $phantomjs-2.1.1-linux-x86_64.tar.bz2
> sudo mv phantomjs-2.1.1-linux-x86_64 /usr/local/share
> sudo ln -sf /usr/local/share/phantomjs-2.1.1-linux-x86_64/bin/phantomjs /usr/local/bin

测试下是否已安装

> phantomjs -v // 2.1.1