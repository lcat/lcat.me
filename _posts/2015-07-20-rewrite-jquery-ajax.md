---
layout: post
title: 重写 jquery ajax
tags: [jquery, ajax]
---

项目需要，在每一个ajax 请求前后做一些公共动作。然后就开始重写（封装）ajax

```js
(function($) {
  
  $.ajaxSetup({
    cache: false //关闭AJAX缓存
  })

  var _ajax = $.ajax;

  $.ajax = function(opt) {

    var fn = {
      success: opt.success ? opt.success : function(data, textStatus) {},
      error: opt.error ? opt.error : function(XMLHttpRequest, textStatus, errorThrown) {},
      beforeSend: opt.beforeSend ? opt.beforeSend : function(XMLHttpRequest, settings) {}
    };

    opt.dataType = opt.dataType || 'json';
    opt.type = opt.type || 'POST';

    var _opt = $.extend(opt, {
      success: function(data, textStatus) {
        // custom 
        // ...
        fn.success(data, textStatus);
      },
      error: function(XMLHttpRequest, textStatus, errorThrown) {
        // custom 
        // ...
        fn.error(XMLHttpRequest, textStatus, errorThrown);
      },
      beforeSend: function(XMLHttpRequest, settings) {
        // custom 
        // ...
        fn.beforeSend(XMLHttpRequest, settings);
      }
    });
    // 返回ajax  可链式调用
    return _ajax(_opt); 
  }
})(jQuery); 
```

在引入jquery 的下面引入这个js 就可以了，没有长期测试，不知道有没有什么坑...