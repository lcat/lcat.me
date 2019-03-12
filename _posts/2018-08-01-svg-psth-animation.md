---
layout: post
title: svg path animation
tags: [svg, path, stroke, dashoffset, dasharray]
---

![动效](/public/images/2018-08-01/gif.gif)

某天[微博](https://weibo.com/3647843001/GscEB0rmO?type=comment#_rnd1552295420975)上不小心浏览到某知名UI博主一个登录动效流，看了下然后马上去看评论。果然基本都是说 `程序已死` `想打人系列` . 那会自己也思考了小会，这应该很难实现吧？

过了几天，又不小心在某个网站看到 `prototypr`的一篇教程，就是写看到的登录动效教程。马上mark..



看完教程，整个是用 `svg path` 做的动画。

![路径](/public/images/2018-08-01/path.png)

### path 的一些属性

* `d` 路径命令集合
* `stroke` 路径颜色
* `stroke-width` 路径宽度
* `stroke-dasharray` <length>和<percentage>数列。指定短划线和缺口的长度。
* `stroke-dashoffset` 路径的偏移值

### 原理

就是利用 `stroke-dasharray` 和 `stroke-dashoffset` 变更显示 path 的某一段，再配合 `transition` 就会又下划线(边框)有动画效果。demo 中 `stroke` 是引用了 `linearGradient`

```css
svg path {
    stroke-dashoffset: 0;
    stroke-dasharray: 0, 1386;
    transition: all 700ms; /*重点*/
}
svg path.email {
    stroke-dashoffset: 0;
    stroke-dasharray: 242, 1386;
}
svg path.password {
    stroke-dashoffset: -336;
    stroke-dasharray: 240, 1386;
}
svg path.submit {
    stroke-dashoffset: -730;
    stroke-dasharray: 530, 1386;
}
```

[demo 地址](/public/html/svg-path-animation.html)

### 参考

* [How to create the snake highlight animation with anime-js](https://blog.prototypr.io/how-to-create-the-snake-highlight-animation-with-anime-js-bf9c6cb66434)
* [线条之美，玩转SVG线条动画](http://www.alloyteam.com/2017/02/the-beauty-of-the-lines-break-lines-svg-animation/)