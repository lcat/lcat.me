---
layout: post
title: 踩坑日记 - canvas 透明点rgb
tags: [canvas, 透明, rgb]
---

最近一个项目，接口返回一张png原图，原图包括 `人脸图+mask图` 即 RGB + mask 。就是RGBA最后一个通道 Alpha 是 mask。

然后直接写代码：

```js
    // https://jsfiddle.net/0vh7xojy/3/
    var url = 'https://obs.mtlab.meitu.com/mtopen/EtRGxLI8ulQ43pGhuZfym9aYUD2PUbMP/MTU3NTI4MDgwMA==/f70f98c5-e863-47b8-6260-730ea4ca4c3c.png';
    var img = new Image();
    img.crossOrigin = 'Anonymous';
    img.onload = () => {
        var canvas = document.createElement('canvas');
        var ctx = canvas.getContext('2d');

        canvas.width = img.width;
        canvas.height = img.height;

        ctx.drawImage(img, 0, 0);

        var imgData = ctx.getImageData(0, 0, img.width, img.height);
        var face = new ImageData(img.width, img.height);
        var mask = new ImageData(img.width, img.height);
        var faceBase64;
        var maskBase64;
        
        console.log(imgData);

        for (let i = 0; i < imgData.data.length; i+=4) {
            face.data[i] = imgData.data[i];
            face.data[i + 1] = imgData.data[i + 1];
            face.data[i + 2] = imgData.data[i + 2];
            face.data[i + 3] = 255;

            // mask 可以自己设置颜色，这里是红色
            mask.data[i] = 255;
            mask.data[i + 1] = 0;
            mask.data[i + 2] = 0;
            mask.data[i + 3] = imgData.data[i + 3];
        }

        ctx.clearRect(0, 0, img.width, img.height);

        ctx.putImageData(face, 0, 0);

        faceBase64 = canvas.toDataURL('image/jpeg');

        document.getElementById('face').src = faceBase64;

        ctx.clearRect(0, 0, img.width, img.height);

        ctx.putImageData(mask, 0, 0);

        maskBase64 = canvas.toDataURL();

        document.getElementById('mask').src = maskBase64;
    }
    img.src = url;
```

然而跑起来后提取出来的 mask 图是正确的，人脸图跟原图一模一样，开始各种怀疑。

1. 简单看下原图的 imageData 发现 RGB 大部分是 0。怀疑原图是不是有问题
2. 客户端那边跑了一下人脸图是正常的，原图没问题
3. review 上面那段代码，没发现有错误
4. 回到第1点，不应该大部分 RGB 都是 0
5. 怀疑 是不是透明点的 RGB 都变成 0 了。
6. google
7. [stackoverflow How to get rgb from transparent pixel in js](https://stackoverflow.com/questions/39744072/how-to-get-rgb-from-transparent-pixel-in-js) 
结论：为了提高性能，画布底层缓冲区会对颜色进行预乘alpha，在写入缓冲区之前会乘以alpha值。canvas期待getImageData结果未预乘，这意味将从缓冲区取值除以alpha，来获取原始RGB值。这样带来问题：预乘会失去精度、完全透明(alpha = 0)的像素无法进行预乘除恢复(重点重点重点)