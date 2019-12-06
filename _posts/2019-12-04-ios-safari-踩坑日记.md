---
layout: post
title: 踩坑日记 - ios safari
tags: [踩坑, ios, safari]
---

记录下最近一个项目踩坑泪屎

### ios 10 及以上 meta 禁用缩放无效

原本通过 `meta` 标签设置 `user-scalable=no` 就可以禁用缩放，可是突然在 `safari` 上发现又能缩放了，查了一圈资料，原来是 ios 10 及以上的新特性。

> To improve accessibility on websites in Safari, users can now pinch-to-zoom even when a website sets user-scalable=no in the viewport.

然而，需求比较特殊不能缩放，所以只能通过 javascript 禁用。

```js
document.addEventListener('touchstart', function (event) {
    // 触点大于1的直接 preventDefault
    if (event.touches.length > 1) {
        event.preventDefault();
    }
});
var lastTouchEnd = 0;
document.addEventListener('touchend', function (event) {
    var now = (new Date()).getTime();
    if (now - lastTouchEnd <= 300) {
        event.preventDefault();
    }
    lastTouchEnd = now;
}, false);
document.addEventListener('gesturestart', function (event) {
    event.preventDefault();
})
```

### 屏蔽重力长按

safari 重力长按图片会不小心当前窗口打开图片，这有点扯。

1. 常规 css 屏蔽

```css
img {
    -webkit-touch-callout: none;
    -webkit-user-select: none;
    user-select: none;
}
```

2. 一些图片需求是要长按保存的，不能用上面👆的方法，只能通过 javascript 禁用

```js
window.addEventListener('touchforcechange', function(event) {
    var force = event.changedTouches[0].force;
    var target = event.changedTouches[0].target;
    // 值可以自己调，试了一遍过去感觉 0.15 挺合适
    if (force > 0.15) {
        event.preventDefault();
    }
})
```

### ios 13 

上面的 `touchforcechange` 失效了，重力长按还会带来一个问题，RTC 莫名被停了，打印了下 video 也是正常播放，mediaStream.active 也是 true。各种状态看起来都是正常，然后尝试直接重新初始化 getUserMedia。然后有些 ios 会卡死，吐血🤮

这时候只好打开竞品，看了下，他们 ios 13 图片不用长按保存(完美避开上面问题)，直接下载，查了下 [caniuse Download attribute](https://caniuse.com/#search=download)。 ios 13 开始支持了。建议判断下是否支持 `Download attribute` 支持的话就直接下载。

```js
// 判断是否支持 Download attribute
function isSupportDownload() {
    let a = document.createElement('a');
    if (typeof a.download != "undefined") {
        return true;
    }
    return false;
}
```

### 页面初始化获取的 `window.innerHeight` 或者 `document.documentElement.clientHeight` 错误

最开始是测试反馈几台机子(iPhone 7plus 12.3.1)概率性进来变形了，后面发现 `safari` 地址栏编辑后进来，获取的高度会是错误的（怀疑是键盘影响到），正常刷新是不会。一开始测试万能的 setTimeout 到 800ms，还被我重现了一两次，完全不能接受，后面只能改逻辑，将错就错，界面逻辑不出错就行。不知道哪位大佬知道是什么原因，或者有什么完美解决办法。

[demo 地址](https://f2er.meitu.com/lx/tmp/height.html)

[复现视频](https://makeup-magic.zone1.meitudata.com/webar/RPReplay_Final1574651440.MP4)