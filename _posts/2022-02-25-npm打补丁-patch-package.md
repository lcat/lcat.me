---
layout: post
title: npm打补丁patch-package
tags: [npm, patch-package]
---

日常开发中肯定会用到第三方组件，如果第三方组件有bug，或者不支持你急需的某个特性。如果给第三方组件提MR，有些甚至得个把月才能合入发版。如果项目比较紧急，这时候可以用 `patch-package` 给第三方组件打上补丁。

举个例子🌰

[postcss-px-to-viewport](https://github.com/evrone/postcss-px-to-viewport) 最开始不支持 `/* px-to-viewport-ignore */` 来忽略 `px` 转 `vw`, 项目经常有那种一像素边框，不能转vm，只能用px。只能通过 `selectorBlackList`，这种写法比较繁琐、特遇到伪元素等也特别麻烦。给作者提 [PR](https://github.com/evrone/postcss-px-to-viewport/pull/27) 也没那么及时得到合入。

接下来用 `patch-package` 来给这个工具打上补丁，支持我们需要的特性。

第一步 `npm install postcss-px-to-viewport --save-dev` 安装工具，然后直接找到 `node_modules/postcss-px-to-viewport` 里面修改。

```js
// index.js L84

var nodes = decl.parent.nodes;
var indexes = decl.parent.indexes['1'];
var next = nodes[indexes + 1];
// next declaration is comment and comment text is no or px
if (next && next.type === 'comment' && (next.text === 'no' || next.text === 'px')) {
  // remove comment
  nodes.splice(indexes + 1, 1);
  return;
}
```

加完可以运行下项目代码验证下是否支持特性了，测试通过后就可以开始打补丁。

回到项目根目录，执行 `npx patch-package postcss-px-to-viewport`, 会在根目录生成 `patches/postcss-px-to-viewport+1.1.0.patch` 文件。打开这个文件是 `git diff`记录描述。

```patch
diff --git a/node_modules/postcss-px-to-viewport/index.js b/node_modules/postcss-px-to-viewport/index.js
index db48afc..3676007 100755
--- a/node_modules/postcss-px-to-viewport/index.js
+++ b/node_modules/postcss-px-to-viewport/index.js
@@ -84,6 +84,16 @@ module.exports = postcss.plugin('postcss-px-to-viewport', function (options) {
           size = opts.viewportWidth;
         }
 
+        var nodes = decl.parent.nodes;
+        var indexes = decl.parent.indexes['1'];
+        var next = nodes[indexes + 1];
+        // next declaration is comment and comment text is no or px
+        if (next && next.type === 'comment' && (next.text === 'no' || next.text === 'px')) {
+          // remove comment
+          nodes.splice(indexes + 1, 1);
+          return;
+        }
+
         var value = decl.value.replace(pxRegex, createPxReplace(opts, unit, size));
         
         if (declarationExists(decl.parent, decl.prop, value)) return;
```

第二步 在 `package.json` script 里面增加

```json
{
    "postinstall": "patch-package"
}
```

第三步 `git` 提交 `patches` 文件夹

大功告成，别人拉取代码下来后，执行 `npm install` 会自动应用补丁。

