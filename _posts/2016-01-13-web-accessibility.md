---
layout: post
title: web 可访问性
tags: [web, accessibility, 可访问性, aria]
---

今天在看 [ant-design](http://ant.design/) 的代码时，发现下面代码

```html
<span ref="selection"
      key="selection"
      className={`${prefixCls}-selection ${prefixCls}-selection--${multiple ? 'multiple' : 'single'}`}
      role="combobox"
      aria-autocomplete="list"
      aria-haspopup="true"
      aria-expanded={state.open}
  {...extraSelectionProps}>
</span>
```

有没有发现很多 `aria-*="**"`. 

### 简介

Accessible Rich Internet Applications(aria)

> HTML 4 開始有了 tabindex，可以用於 a、area、button、input、object、select、textarea 這幾個元素上，tabindex的值是介於 1～32767 的正整數，導覽會從數字小的開始，直到數字最大的元素，有設定 tabindex 的元素會以設定的值排入導覽順序，不過如果網頁的內容有好的邏輯結構，tabindex 就可以不必設定，介面元件會自己依序排入 tab 鍵導覽的順序中。

> ARIA擴充了 tabindex 屬性，讓他可以出現在所有可見的元素中，除此之外，還允許使用負值讓元素不在 tab 鍵導覽的順序中出現，但還是可以用 script 程式取用它，不過由於負值的大小並不重要，所以通常是使用 -1。

### 属性

* role: 告诉读屏软件这个元素扮演什么角色，比如：

```html
<a href="http://lcat.me" role="link" class="">me.lcat</a>
<button type="button" role="button">按钮</button>
```

* aria-label: 告诉读屏软件这个元素是什么，比如：

```html 
// 比如一些字体ico
<button type="button" role="button" aria-label="close">x</button>
<input name="email" type="email" placeholder="请输入邮箱" aria-label="请输入邮箱">
```

当图片 `<img />`同时有 `alt` `aria-label` 和 `title` 时，读屏软件会读取 `aria-label`

### 参考链接

1. [前端的基礎修養：aria-label](http://lepture.com/zh/2015/fe-aria-label)
2. [不被重視的 HTML：Accessibility](http://lepture.com/zh/2014/talk-on-accessibility-jsdc2014)
3. [MDN ARIA](https://developer.mozilla.org/zh-CN/docs/Web/Accessibility/ARIA)
4. [w3c - ARIA in HTML](https://www.w3.org/TR/html-aria/)