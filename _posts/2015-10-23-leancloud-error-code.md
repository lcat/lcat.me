---
layout: post
title: leancloud(avoscloud) error code
tags: [avoscloud, leancloud, error_code]
---

在用[leancloud](https://leancloud.cn/)的云引擎，返回的信息是英文了。官网上找了下[错误代码对照表](https://cn.avoscloud.com/docs/error_code.html)，发现好像没有json格式下载。只好网页上抓取咯。

打开控制台，运行下面代码：

```js
var el = $('.sidebar-gruntfile-trigger.col-sm-9 h2');
var data = {};
el.each(function(key, item) {
  var $next = $(this).next();
  if ($next.prop('tagName') === 'UL') {
    data[$(this).text()] = {
      msg: $($next.find('li')[1]).text().replace('含义 - ', ''),
      msg_en: $($next.find('li')[0]).text().replace('信息 - ', '')
    }
  }
})
console.log(data); // 输出
copy(data); // 复制到粘贴板
```

下面是数据：

```json
{
  "1": {
    "msg": "服务器内部错误或者参数错误，一般是因为传入了错误的参数，或者没有在本文档里明确定义的运行时错误，都会以代码 1 指代。",
    "msg_en": "Internal server error. No information available."
  },
  "100": {
    "msg": "无法建立 TCP 连接到 LeanCloud 服务器，通常是因为网络故障，或者我们服务器故障引起的，我们的服务器状态可以查看 健康状态检查。",
    "msg_en": "The connection to the AVOS servers failed."
  },
  "101": {
    "msg": "查询的 Class 不存在，或者要关联的 Pointer 对象不存在。",
    "msg_en": "Object doesn't exist, or has an incorrect password."
  },
  "103": {
    "msg": "非法的 Class 名称，Class 名称大小写敏感，并且必须以英文字母开头，有效的字符仅限在英文字母、数字以及下划线。",
    "msg_en": "Missing or invalid classname. Classnames are case-sensitive. They must start with a letter, and a-zA-Z0-9_ are the only valid characters."
  },
  "104": {
    "msg": "缺少 objectId，通常是在查询的时候没有传入 objectId，或者 objectId 非法。objectId 只能为字母、数字组成的字符串。",
    "msg_en": "Missing object id."
  },
  "105": {
    "msg": "无效的 key 名称，也就是 Class 的列名无效，列名必须以英文字母开头，有效的字符仅限在英文字母、数字以及下划线。",
    "msg_en": "Invalid key name. Keys are case-sensitive. They must start with a letter, and a-zA-Z0-9_ are the only valid characters."
  },
  "106": {
    "msg": "无效的 Pointer 格式，Pointer必须为形如 {className: 'Post', objectId:'xxxxxx'} 的 JSON 对象。",
    "msg_en": "Malformed pointer. Pointers must be arrays of a classname and an object id."
  },
  "107": {
    "msg": "无效的 JSON 对象，解析 JSON 数据失败。",
    "msg_en": "Malformed json object. A json dictionary is expected."
  },
  "108": {
    "msg": "此 API 仅供内部使用。",
    "msg_en": "Tried to access a feature only available internally."
  },
  "111": {
    "msg": "想要存储的值不匹配列的类型，请检查你的数据管理平台中列定义的类型，查看存储的数据是否匹配这些类型。",
    "msg_en": "Field set to incorrect type."
  },
  "112": {
    "msg": "推送订阅的频道无效，频道名称必须不是空字符串，只能包含英文字母、数字以及下划线，并且只能以英文字母开头。",
    "msg_en": "Invalid channel name. A channel name is either an empty string (the broadcast channel) or contains only a-zA-Z0-9_ characters and starts with a letter."
  },
  "113": {
    "msg": "Class 中的某个字段设定成必须，保存的对象缺少该字段。",
    "msg_en": "Key is required."
  },
  "114": {
    "msg": "iOS 推送存储的 deviceToken 无效，如何存储 installation 请阅读 消息推送开发指南。",
    "msg_en": "Invalid device token."
  },
  "116": {
    "msg": "要存储的对象超过了大小限制，我们限制单个对象的最大大小在 16 M。",
    "msg_en": "The object is too large."
  },
  "117": {
    "msg": "更新的 Key 是只读属性，无法更新。",
    "msg_en": "The key is read only."
  },
  "119": {
    "msg": "该操作无法从客户端发起。通常可以通过在应用设置里开启对应选项就可以解决。",
    "msg_en": "That operation isn't allowed for clients."
  },
  "120": {
    "msg": "查询结果无法从缓存中找到，SDK 在使用从查询缓存的时候，如果发生缓存没有命中，返回此错误。",
    "msg_en": "The results were not found in the cache."
  },
  "121": {
    "msg": "JSON 对象中 key 的名称不能包含 $ 和 . 符号。",
    "msg_en": "Keys in NSDictionary values may not include '#39; or '.'."
  },
  "122": {
    "msg": "无效的文件名称，文件名称只能是英文字母、数字和下划线组成，并且名字长度限制在 1 到 36 之间。",
    "msg_en": "Invalid file name. A file name contains only a-zA-Z0-9_. characters and is between 1 and 36 characters."
  },
  "123": {
    "msg": "ACL 格式错误，如果您是使用 SDK 提供的 AVACL 类，理论上这不应该发生，正确的 ACL 格式请参考 REST API。",
    "msg_en": "Invalid ACL. An ACL with an invalid format was saved. This should not happen if you use AVACL."
  },
  "124": {
    "msg": "请求超时，超过一定时间（默认 10 秒）没有返回，通常是因为网络故障或者该操作太耗时引起的。",
    "msg_en": "The request timed out on the server. Typically this indicates the request is too expensive."
  },
  "125": {
    "msg": "电子邮箱地址无效。",
    "msg_en": "The email address was invalid."
  },
  "126": {
    "msg": "无效的用户 Id，可能用户不存在。",
    "msg_en": "Invalid user id."
  },
  "127": {
    "msg": "手机号码无效。",
    "msg_en": "The mobile phone number was invalid."
  },
  "137": {
    "msg": "违反 class 中的唯一性索引约束（unique），尝试存储重复的值。",
    "msg_en": "A unique field was given a value that is already taken."
  },
  "139": {
    "msg": "角色名称非法，角色名称只能以英文字母、数字或下划线组成。",
    "msg_en": "Role's name is invalid."
  },
  "140": {
    "msg": "超过应用的容量限额，请升级账户等级。",
    "msg_en": "Exceeded an application quota. Upgrade to resolve."
  },
  "141": {
    "msg": "云引擎脚本编译或者运行报错。",
    "msg_en": "Cloud Code script had an error."
  },
  "142": {
    "msg": "云引擎校验错误，通常是因为 beforeSave、beforeDelete 等函数返回 error。",
    "msg_en": "Cloud Code validation failed."
  },
  "145": {
    "msg": "本设备没有启用支付功能。",
    "msg_en": "Payment is disabled on this device."
  },
  "150": {
    "msg": "转换数据到图片失败。",
    "msg_en": "Fail to convert data to image."
  },
  "160": {
    "msg": "账户余额不足。",
    "msg_en": "Insufficient balance."
  },
  "200": {
    "msg": "没有提供用户名，或者用户名为空。",
    "msg_en": "Username is missing or empty"
  },
  "201": {
    "msg": "没有提供密码，或者密码为空。",
    "msg_en": "Password is missing or empty."
  },
  "202": {
    "msg": "用户名已经被占用。",
    "msg_en": "Username has already been taken."
  },
  "203": {
    "msg": "电子邮箱地址已经被占用。",
    "msg_en": "Email has already been taken."
  },
  "204": {
    "msg": "没有提供电子邮箱地址。",
    "msg_en": "The email is missing, and must be specified."
  },
  "205": {
    "msg": "找不到电子邮箱地址对应的用户。",
    "msg_en": "A user with the specified email was not found."
  },
  "206": {
    "msg": "没有提供 session，无法修改用户信息，这通常是因为没有登录的用户想修改信息。修改用户信息必须登录，除非在云引擎里，或者使用 master key 调用 REST API。",
    "msg_en": "The user cannot be altered by a client without the session."
  },
  "207": {
    "msg": "只能通过注册创建用户，不允许第三方登录。",
    "msg_en": "Users can only be created through sign up."
  },
  "208": {
    "msg": "第三方帐号已经绑定到一个用户，不可绑定到其他用户。",
    "msg_en": "An existing account already linked to another user."
  },
  "210": {
    "msg": "用户名和密码不匹配。",
    "msg_en": "The username and password mismatch."
  },
  "211": {
    "msg": "找不到用户。",
    "msg_en": "Could not find user."
  },
  "212": {
    "msg": "请提供手机号码。",
    "msg_en": "The mobile phone number is missing, and must be specified."
  },
  "213": {
    "msg": "手机号码对应的用户不存在。",
    "msg_en": "A user with the specified mobile phone number was not found."
  },
  "214": {
    "msg": "手机号码已经被注册。",
    "msg_en": "Mobile phone number has already been taken."
  },
  "215": {
    "msg": "未验证的手机号码。",
    "msg_en": "Mobile phone number isn't verified."
  },
  "216": {
    "msg": "未验证的邮箱地址。",
    "msg_en": "Email address isn't verified."
  },
  "250": {
    "msg": "连接的第三方账户没有返回用户唯一标示 id",
    "msg_en": "Linked id missing from request"
  },
  "251": {
    "msg": "无效的账户连接，一般是因为 access token 非法引起的。",
    "msg_en": "Invalid linked session或者Invalid Weibo session"
  },
  "300": {
    "msg": "CQL 语法错误。详情参考 CQL 语法详细指南",
    "msg_en": "CQL syntax error."
  },
  "301": {
    "msg": "新增对象失败，通常是数据格式问题。",
    "msg_en": "Fails to insert new document."
  },
  "302": {
    "msg": "无效的 GeoPoint 类型，请确保经度在 -180 到 180 之间，纬度在 -90 到 90 之间。",
    "msg_en": "Invalid GeoPoint values."
  },
  "401": {
    "msg": "未经授权的访问，没有提供 App id，或者 App id 和 App key 校验失败，请检查配置。",
    "msg_en": "Unauthorized."
  },
  "403": {
    "msg": "操作被禁止，因为 Class 权限限制。",
    "msg_en": "Forbidden to xxx by class permissions"
  },
  "502": {
    "msg": "服务器维护中。",
    "msg_en": "Server is in maintenance."
  },
  "503": {
    "msg": "超过流量访问限制，默认 API 并发 1000 访问每秒，通过数据管理平台每秒限制上传一个文件，并且每分钟最多上传 30 个文件，如需提升，请联系我们。",
    "msg_en": "Rate limit exceeded."
  },
  "600": {
    "msg": "无效的短信签名。短信签名是指附加在短信文本前后位置，使用中文括号【】括起来的文字，短信签名只能位于短信开头或者结束的位置，并且限制在 10（包含 10 个字符）个字符内。  默认发送的短信签名使用的是应用名称，应用名称可以在应用设置里修改。短信自定义模板可以在模板里自定义签名。+",
    "msg_en": "Invalid SMS signature."
  },
  "601": {
    "msg": " 发送短信过于频繁。我们限制验证类短信一分钟一条，每天每个号码限制在 10 条左右。我们强烈建议用户使用图形验证码或者倒数计时等方式来避免用户重复发送验证码，以及可能存在的短信验证码攻击。",
    "msg_en": "Can't send SMS too frequently."
  },
  "602": {
    "msg": " 发送短信或者语音验证码失败，这是短信提供商返回错误，如果确认手机号码没有问题，请联系我们处理。",
    "msg_en": "Fails to send message."
  },
  "603": {
    "msg": "无效的短信验证码，通常是不匹配或者过期。",
    "msg_en": "Invalid SMS code."
  },
  "604": {
    "msg": "找不到自定义的短信模板，请检查模板名称是否正确或者模板是否已经创建并审核通过。",
    "msg_en": "SMS template not found."
  },
  "605": {
    "msg": "短信模板未审核。",
    "msg_en": "SMS template not verified."
  },
  "606": {
    "msg": "渲染短信模板失败，通常是模板语法问题，我们的短信模板仅支持 handlerbars 模板语法。",
    "msg_en": "Fails to render SMS template."
  }
}
```

