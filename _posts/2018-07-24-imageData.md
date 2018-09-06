---
layout: post
title: canvas imageData 入门到放弃
tags: [imageData, canvas, inputImageData]
---

# 方法

### getImageData

> 获取一张图片的像素数据。返回图片宽度、高度、data。data数组中存储所有像素信息，每4个点组成一个像素点信息。[r1, g1, b1, a1, r2, g2, b2, a2, …]

### putImageData

> 把图像数据放回画布

### createImageData

> 创建 imageData 对象。ctx.createImageData(width, height)

# 应用

## 1️⃣ 抠图

> 抠图一般是上传原图到服务端，服务端生成mask图下发。前端拿mask图与原图融合抠图。原理就是把原图的每个像素点的透明度改成mask图对应点的R、G、B

[抠图demo](https://f2er.meitu.com/lx/canvasFilter/alpha.html)

![原图](/public/images/2018-07-24/1.jpg)
原图

![mask图](/public/images/2018-07-24/2.jpg)
mask图

```js
var size = img.width * img.height << 2;
for (var i = 0; i < size; i += 4) {
    imageData.data[i + 3] = maskData.data[i]
}
ctx.putImageData(imageData, 0, 0);
```

## 2️⃣ 反相

> rgb都是0-255. 反相就是取255与rgb的差值

[反相demo](https://f2er.meitu.com/lx/canvasFilter/demo01.html)

![反相图](/public/images/2018-07-24/3.jpg)
反相图

```js
var size = img.width * img.height << 2;
for (var i = 0; i < size; i += 4) {
    imageData.data[i + 0] = 255 - imageData.data[i + 0];
    imageData.data[i + 1] = 255 - imageData.data[i + 1];
    imageData.data[i + 2] = 255 - imageData.data[i + 2];
}
ctx.putImageData(imageData, 0, 0);
```


## 3️⃣ 灰度

[灰度demo](https://f2er.meitu.com/lx/canvasFilter/gray.html)

### 1.平均值法

> 每个点的RGB值都重置为RGB的平均值。 Gray = (R + G + B) / 3

```js
var size = img.width * img.height << 2;
for (var i = 0; i < size; i += 4) {
    var gray = (imageData.data[i + 0] + imageData.data[i + 1] + imageData.data[i + 2]) / 3;
    imageData.data[i + 0] = gray;
    imageData.data[i + 1] = gray;
    imageData.data[i + 2] = gray;
}
ctx.putImageData(imageData, 0, 0);
```

### 2.权重法

> 根据人眼敏感度调的rgb权重. Gray = 0.299R + 0.587G + 0.114B

```js
var size = img.width * img.height << 2;
for (var i = 0; i < size; i += 4) {
    var gray = imageData.data[i + 0] * 0.299 + imageData.data[i + 1] * 0.587 + imageData.data[i + 2] * 0.114;
    imageData.data[i + 0] = gray;
    imageData.data[i + 1] = gray;
    imageData.data[i + 2] = gray;
}
ctx.putImageData(imageData, 0, 0);
```

## 4️⃣ 调整亮度

> 每个像素点RGB分别加上亮度值(变亮)，减去亮度值(变暗)

[亮度demo](https://f2er.meitu.com/lx/canvasFilter/brightness.html)

```js
var brightness = 10;
var size = img.width * img.height << 2;
for (var i = 0; i < size; i += 4) {
    imageData.data[i + 0] += brightness;
    imageData.data[i + 1] += brightness;
    imageData.data[i + 2] += brightness;
}
ctx.putImageData(imageData, 0, 0);
```

## 5️⃣ 图像分割

> 像素灰度值与阈值比较，大于等于阈值的设为255，小于的设为0

[图像分割demo](https://f2er.meitu.com/lx/canvasFilter/threshold.html)

![图像分割](/public/images/2018-07-24/4.png)

图像分割

```js
var threshold = 150;
var size = img.width * img.height << 2;
for (var i = 0; i < size; i += 4) {
    var r = imageData.data[i + 0];
    var g = imageData.data[i + 1];
    var b = imageData.data[i + 2];
    var v = (0.2126 * r + 0.7152 * g + 0.0722 * b >= threshold) ? 255 : 0;

    imageData.data[i + 0] = v;
    imageData.data[i + 1] = v;
    imageData.data[i + 2] = v;
}
ctx.putImageData(imageData, 0, 0);
```



## 6️⃣ 马赛克

> 取周围半径范围的平均色

[马赛克demo](https://f2er.meitu.com/lx/canvasFilter/mosaic.html)

![马赛克](/public/images/2018-07-24/14.png)

马赛克


## 7️⃣ 模糊

[模糊demo](http://f2er.meitu.com/lx/canvasFilter/convolute.html)

### [概念] 卷积

> 二维原图像素矩阵，二维图像滤波矩阵(卷积核)，生成图

![卷积](/public/images/2018-07-24/5.png)

卷积

#### 周围模糊

```js
var matrix = [1/9, 1/9, 1/9,
              1/9, 1/9, 1/9,
              1/9, 1/9, 1/9];
```

![周围模糊](/public/images/2018-07-24/6.png)

#### 锐化

```js
var matrix = [ 0, -1,  0,
              -1,  5, -1,
               0, -1, 0];
```

![锐化](/public/images/2018-07-24/7.png)

#### 浮雕

> 3D阴影效果。将中心点一边的像素减去另外一遍的像素

```js
var matrix = [ 1,   1,  1,
              -1, 0.7, -1,
              -1,  -1, -1];
```

![浮雕](/public/images/2018-07-24/8.png)

#### 运动模糊

> 一个方向模糊，矩阵值大于1会过爆，所以求和结果要归一(除9)

```js
var matrix = [1,0,0,0,0,0,0,0,0,
              0,1,0,0,0,0,0,0,0,
              0,0,1,0,0,0,0,0,0,
              0,0,0,1,0,0,0,0,0,
              0,0,0,0,1,0,0,0,0,
              0,0,0,0,0,1,0,0,0,
              0,0,0,0,0,0,1,0,0,
              0,0,0,0,0,0,0,1,0,
              0,0,0,0,0,0,0,0,1];
```

![运动模糊](/public/images/2018-07-24/9.png)

#### notice

* 卷积核大小应该为奇数，才能有中心点
* 卷积核内所有元素之和为1时前后图亮度不变，大于1生成图变亮变，小于1变暗
* 边界像素点赋0或对称处理

## 8️⃣ 高斯模糊

> 均值模糊不平滑，主要在于距离中心点很远的点与距离中心点很近的所带的权重值相同，产生的模糊效果一样。高斯模糊是正态分布函数，越远的点权重越低。

[高斯模糊demo-二维公式](http://f2er.meitu.com/lx/canvasFilter/gaussBlur.html)

[高斯模糊demo-一维公式](http://f2er.meitu.com/lx/canvasFilter/gaussBlur2.html)

![一维正态](/public/images/2018-07-24/10.png)
一维正态

![二维正态](/public/images/2018-07-24/11.png)
二维正态

![正态](/public/images/2018-07-24/12.png)
一维正态公式

![正态](/public/images/2018-07-24/13.png)
二维正态公式


## 9️⃣ blend

>输入：基准图A(柔光、叠加、变亮等)、基准图B(固定)、素材图

[blend demo](http://f2er.meitu.com/lx/canvasFilter/blend.html)

## 🔟 lut3d

> 对于1D LUT，变动某个颜色输入值只会影响到该颜色的输出值，RBG的数据之间是互相独立的。

![lut1d](/public/images/2018-07-24/1d_lut.gif)

lut1d

![lut3d](/public/images/2018-07-24/3d_lut.gif)

lut3d

# 性能优化

1. 回到上面的高斯模糊demo。1是直接应用二维正态分布公式，2是xy分别应用一维正态分布公式。

复杂度：

> 二维: O(width×height×(2×radius)^2)
>
> 一维: O(width×height×2×radius×2)

二维比一维多了radius倍，一维时间减少明显。

2. 有些算法需要很多遍历，会卡死UI。可以把算法部分放到 `js worker` 里面处理，保证UI进程

```js
    // main.js
    if (window.Worker) {
        var myWorker = new Worker('worker.js');

        myWorker.onmessage = event => {
            console.log(event.data);
            ...
        }
        myWorker.postMessage([...]);
    }
    else {
        // 不兼容 Worker 处理
    }

    // worker.js
    import SuperAlphaMix from './alphaMix';
    onmessage = function(e) {
        var result = new SuperAlphaMix(e.data);
        postMessage(result);
    }
```

# 坑

微信颜色变暗.解决办法: imageData都是在原有数据上操作

[https://bugs.chromium.org/p/chromium/issues/detail?id=713632](https://bugs.chromium.org/p/chromium/issues/detail?id=713632)

[demo](f2er.meitu.com/lx/learn/imgdata.html)
