---
layout: post
title: 分片上传base64
tags: [分片上传, base64, formdata]
---

日常踩坑，需求是上传高清图，所以用`qiniu-js`来做分片上传。因为图片源是`base64`格式，所以一开始直接找了个`dataURLtoFile`函数。

```js
function dataURLtoFile(dataurl, filename) {
    var arr = dataurl.split(','),
        mime = arr[0].match(/:(.*?);/)[1],
        bstr = atob(arr[1]),
        n = bstr.length,
        u8arr = new Uint8Array(n);
    while (n--) {
        u8arr[n] = bstr.charCodeAt(n);
    }
    return new File([u8arr], filename, {
        type: mime
    });
}
```

在 [caniuse](https://caniuse.com/#feat=fileapi) 上简单查了下，移动端可用，就直接提测，上线。

然而N天后，改另外一个bug后，测试突然反馈说`iPhone6 10.2`无法上传图片，抓包接口返回如下：
(是的，之前没有测试出来，直接上线了。。。🤦‍♂️🤦‍♂️🤦‍♂️)

```json
{
	"code": "EntityTooSmall",
	"message": "Your proposed upload is smaller than the minimum allowed object size.",
	"requestId": "6656097885744071680",
	"resource": "https://打码.com/"
}
```

第一反应是难道是`dataURLtoFile`有兼容问题，打印出来出来的`file`看起来都是正常的。进一步debug进去`qiniu-js`。qiniu也只是

```js
let formData = new FormData();
formData.append("file", this.file);
```

想在`formData`后面打印下`file`。又发现 10.2 不支持 `formData.get` 😪😪😪🤢🤢🤢

继续 google

发现也可以 [stackflow Convert Data URI to File then append to FormData](https://stackoverflow.com/questions/4998908/convert-data-uri-to-file-then-append-to-formdata/5100158) 

```js
// https://stackoverflow.com/questions/4998908/convert-data-uri-to-file-then-append-to-formdata/5100158
function dataURItoBlob(dataURI) {
    // convert base64/URLEncoded data component to raw binary data held in a string
    var byteString;
    if (dataURI.split(',')[0].indexOf('base64') >= 0)
        byteString = atob(dataURI.split(',')[1]);
    else
        byteString = unescape(dataURI.split(',')[1]);

    // separate out the mime component
    var mimeString = dataURI.split(',')[0].split(':')[1].split(';')[0];

    // write the bytes of the string to a typed array
    var ia = new Uint8Array(byteString.length);
    for (var i = 0; i < byteString.length; i++) {
        ia[i] = byteString.charCodeAt(i);
    }

    return new Blob([ia], {type:mimeString});
}
```


换上去，跑一下，竟然上传成功了。what happen，小孩子你是否有很多问号🤷‍♂️🤷‍♂️🤷‍♂️
有说知道为什么`dataURLtoFile`失败的大佬，麻烦告知