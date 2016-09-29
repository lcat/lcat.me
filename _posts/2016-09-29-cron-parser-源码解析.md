---
layout: post
title: cron-parser 源码解析
tags: [cron, parser, crontab]
---

最近想做个周期定时提醒小应用（类似Google日历里面的活动提醒），了解了一下，跟`linux`的 [crontab](https://zh.wikipedia.org/wiki/Cron) 很像。[github](https://github.com/search?l=JavaScript&q=cron&type=Repositories&utf8=%E2%9C%93) 上面搜了下很多js实现。然后就有了今天这个 [cron-parser](https://github.com/harrisiirak/cron-parser)

看 `README.md` 支持格式

```
*    *    *    *    *    *
┬    ┬    ┬    ┬    ┬    ┬
│    │    │    │    │    |
│    │    │    │    │    └ day of week (0 - 7) (0 or 7 is Sun)
│    │    │    │    └───── month (1 - 12)
│    │    │    └────────── day of month (1 - 31)
│    │    └─────────────── hour (0 - 23)
│    └──────────────────── minute (0 - 59)
└───────────────────────── second (0 - 59, optional)
```

首先打开 `package.json` 可以找到一些关键信息，比如入口文件是 `"main": "lib/parser.js",` `dependencies` 等。

```js
// 主入口，调用expression.js 里面的parse
CronParser.parseExpression = function parseExpression (expression, options, callback) {
  return CronExpression.parse(expression, options, callback);
};
```

看下 `expression.js` 里面的依赖， `./date.js` 是date的工具库， `is-nan.js` 是 Number.isNaN 的ES6 shim。

进去 `parse` 函数 

```js
CronExpression.predefined = {
  '@yearly': '0 0 1 1 *',
  '@monthly': '0 0 1 * *',
  '@weekly': '0 0 * * 0',
  '@daily': '0 0 * * *',
  '@hourly': '0 * * * *'
};
CronExpression.parse = function parse (expression, options, callback) {
  ...
  // 首先查找是不是常用的
  if (CronExpression.predefined[expression]) {
    expression = CronExpression.predefined[expression];
  }
  
  // expression field 的 constraints
  var fields = [];

  // 截断补全expression
  for (...) {
    // 解析field
    _parseField(...);
  }
  
  // 实例化
  return new CronExpression(fields, options);
}
```

进去 `_parseField` 函数

```js
CronExpression._parseField = function _parseField (field, value, constraints) {
  ...
  // 解析,  如 1,3,5
  parseSequence(...);

  // 解析/  如 */2
  parseRepeat(...);

  // 解析- 如 1-5
  parseRange(...);
}
```

比如 `0 8 * * 3` 经过 `parse` 函数里面for循环出来的 `fields` 为：

```json
[
  [0],
  [0],
  [8],
  [1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21,22,23,24,25,26,27,28,29,30,31],
  [1,2,3,4,5,6,7,8,9,10,11,12],
  [3]
]
```
上面 `parse` 函数返回一个实例化对象，现在要获取符合 `expression` 的时间，要再调用原型链上的 `next` 方法

```js
CronExpression.prototype.next = function next () {
  // 关键入口
  var schedule = this._findSchedule();
  
  // 支持 ES6 的迭代器
  // Try to return ES6 compatible iterator
  if (this._isIterator) {
    return {
      value: schedule,
      done: !this.hasNext()
    };
  }

  return schedule;
}
```

进去 `_findSchedule` 函数 

```js
CronExpression.prototype._findSchedule = function _findSchedule () {
  ...
  // 以 currentDate 基准
  // 追加天数，只到符合条件的day（DD）
  addDay();

  // 追加月份，只到符合条件的month（MM）
  addMonth();

  // 追加小时，只到符合条件的hour（HH）
  addHour();

  // 追加分钟数，只到符合条件的minute（mm）
  addMinute();

  // 追加秒数，只到符合条件的second（ss）
  addSecond();

  return currentDate;
}
```

附上一些常用配置

```js
function preview(describe, expression, options) {
  ...
}

preview('每周三早晨8点整', '0 8 * * 3');
preview('每月10号早晨8点整', '0 8 10 * *');
preview('工作日早晨8点整', '0 8 * * 1-5');
preview('每天早晨8点整', '0 8 * * *');
preview('每两天早晨8点整', '0 8 */2 * *');
```
