---
layout: post
title: angular interpolate interr
tags: [angular, interpolate, interr]
---

今天开发上遇到我给超链接加一个attribute data-src="//path"

```html
  <a href="" data-src="http://path.jpg">test</a>
```
但是报错了

```js
Error: [$interpolate:interr] Can't interpolate: {{x.exampleImageUrl }}
Error: [$sce:insecurl] Blocked loading resource from url not allowed by $sceDelegate policy.  URL: http://imgp.zteup.com/upload/sys/qualification/c15f6488a179419e90ef15a6fe991769.png
http://errors.angularjs.org/1.2.22/$sce/insecurl?p0=http%3A%2F%2Fimgp.zteup.com%2Fupload%2Fsys%2Fqualification%2Fc15f6488a179419e90ef15a6fe991769.png
http://errors.angularjs.org/1.2.22/$interpolate/interr?p0=%7B%7Bx.exampleIm…Fupload%252Fsys%252Fqualification%252Fc15f6488a179419e90ef15a6fe991769.png
```

一翻搜索后，原来是 angular 为了防指 url 注入，所以我们需要加一个过滤器.

```js
  angular.module('app')
  .filter('trustUrl', function ($sce) {
    return function(url) {
      return $sce.trustAsResourceUrl(url);
    };
  });
```

然后html那边对应修改

```html
  <a href="" data-src="{{src | trustUrl}}">test</a>
```

[plnkr demo](http://plnkr.co/edit/tYq22VjwB10WmytQO9Pb?p=preview)
