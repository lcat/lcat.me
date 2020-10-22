---
layout: post
title: IntersectionObserver API
tags: [IntersectionObserver, intersection, 统计]
---


最近有个项目需要统计一些卡片、商品曝光。卡片是上下滑动，商品是横向滑动，统计需求是用户停止滚动时漏出50%就当做曝光统计。

首先想到的是监听 `scroll` 事件，再获取目标元素的位置是否在可视范围来判断。但是 `scroll` 事件密集，而且需要停止滚动才算，还需要封装 `scrollEnd` 事件。

两个交叉滚动容器，再加滚动停止，感觉很是蛋疼，一不小心可能就影响到性能。

突然想起来之前有看到一个新的 [`IntersectionObserver API`](https://developer.mozilla.org/zh-CN/docs/Web/API/Intersection_Observer_API)

> Intersection Observer API提供了一种异步检测目标元素与祖先元素或 viewport 相交情况变化的方法。

新 API 首先要翻下兼容性怎样 [can i use intersection](https://caniuse.com/?search=Intersection) 。感觉兼容性还行，IE全挂。但是在 MDN 底下看到了 [polyfill](https://github.com/w3c/IntersectionObserver/tree/master/polyfill)，又可以愉快玩耍。

有了 `Intersection Observer API` 就不要关注是几个滚动容器了，只需要设置一个回调函数，每当目标元素与指定元素发生交集的时候就会执行。

```js

var myObserver = new IntersectionObserver((entries, observer) => {
    entries.forEach(entry => {
        if (entry.isIntersecting) {
            // 这边可以统计或者一些其他逻辑
            // 如果只需要曝光一次，可以解绑
            // myObserver.unobserve(entry.target)
        }
    })
}, {
    root: null,
    rootMargin: '0px',
    threshold: 0.5, // 曝光50%
})

myObserver.observe(targetElement);

// 可以停止观察
// myObserver.disconnect();
```

简单几行代码，把上面的需求搞定，这个 api 相对还是挺强大的，有兴趣的可以多翻翻 MDN

































