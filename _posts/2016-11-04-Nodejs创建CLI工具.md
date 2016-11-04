---
layout: post
title: 使用Node.js创建CLI工具
tags: [nodejs, cli, node, yargs]
---

原文链接[Creating a CLI tool in Node.js](http://eridem.net/creating-a-cli-tool-in-node-js/)

## 开始之前

- 在Linux和Mac OSX上使用任何终端，在Windows上使用PowerShell。
- 你已安装Node.js 6+版本。
- 本教程使用的语法基于ES6。
- 你可以在这里获得本教程的完整源代码：[https：//github.com/eridem/cli-tutorial](https：//github.com/eridem/cli-tutorial)

## 初始结构

在文件系统中创建如下所示的结构：

```
+ my-cli-app
  + lib
    + commands
    + modules
    + scaffolding
  + test
    + lib
      + commands
      + modules
  - index.js 
```

然后，在根文件夹中初始化NPM：

```shell
$ npm init -y  # 会创建一个 package.json 文件
```

## 让你的应用程序全局化

打开你的`package.json`并定义将在可执行名称和起点文件：

```json
"bin": {
  "mycli": "index.js"
}
```

然后，使用 `#!/usr/bin/env node` 告诉npm `index.js` 是一个Node.js可执行文件：

```js
#!/usr/bin/env node
'use strict'

// The rest of the code will be here...
console.log("Hello world!")
```

## 调试程序

我们可以告诉NPM，你当前开发中的应用程序是一个全局应用程序，因此我们可以在文件系统中的任何位置进行测试：

```shell
$ npm link  # 根文件夹中
```

然后，你已经可以从计算机上的任何路径执行应用程序：

```shell
$ mycli     # Should print "Hello world" on your screen
```

## 命令和选项

CLI工具，通常有三个部分：

- CLI应用程序名称
- 一个命令作为主要任务执行
- 帮助命令执行的不同选项

```shell
$ myCliApp <command> <options>           # 结构
$ myCliApp say "Hello" --name "CLI"      # 示例
```

对于命令和选项，我使用[YARGS](https://npmjs.org/package/yargs)。 它使用起来相当简单，它为我们做了很多验证。

```shell
$ npm install --save yargs               # 根文件夹中
```

## 加载所有命令

修改`index.js`文件以使用[YARGS](https://npmjs.org/package/yargs)。 我们可以使用`commandDir`函数从`yargs`加载每个命令在这个文件夹（下面的例子）。

```js
#!/usr/bin/env node
'use strict'

const { join } = require('path')
const yargs = require('yargs')

yargs
  .commandDir(join(__dirname, 'lib', 'commands'))
  .demand(1)
  .help()
```

## 实现命令

在文件夹`lib/commands`中的一个文件中指定了一个命令。 它需要`导出`一些部分。 例如：

```js
'use strict'

exports.command = 'say <prefix>'
exports.desc = 'Prints: <prefix> name surname'
exports.builder = {
  name: {
    alias: 'n',
    describe: 'Pass the name',
    demand: true
  },
  surname: {
    alias: 's',
    describe: 'Pass the surname',
    demand: false
  }
}
exports.handler = function (argv) {
  const { prefix, name, surname } = argv
  const message = prefix + (name ? ' ' + name : '') + (surname ? ' ' + surname : '')
  console.log(message)
}
```

Where:

- `exports.command`: 命令名和参数
  - `say`: 命令名
  - `<prefix>`: 将用作强制性选项
- `exports.desc`: 描述将出现在帮助信息上
- `exports.builder`: 用于定义附加选项
  - 每个`item`是一个选项。
  - `demand: true` 必须的附加选项
  - `yargs`将为我们做所有的验证，所以我们不需要担心它
- `exports.hander` 函数，如果命令是将被调用
  - 在`yargs`对我们进行所有验证之后，参数`argv`将包含在CLI上传递的所有选项的值

## 脚手架命令

如果我们想像我们编写一个`bash`脚本那样进行操作，我们可以使用以下包：

```shell
$ npm install --save shelljs
```

这将使我们有可能使用命令像`mkdir`，`cp`，`touch`，`mv`...而且那些将在Linux和Windows无差别工作。

这对于脚手架工具非常有用，例如，我们在终端中工作时创建文件结构。 这里举个例子：

```shell
# Append the following structure into your project and add random data into the files
# 将以下结构附加到项目中，并将随机数据添加到文件中

+ my-cli-app
  + lib
    + scaffolding
      + create
        - module-file.js
        - another-module-file.json
    + commands
      - create.js
```

我们创建一个命令来复制`lib/commands/create.js`文件中的文件。

```js
'use strict'

const shell = require('shelljs')
const { join } = require('path')

exports.command = 'create <moduleName>'
exports.desc = 'Scaffolding command to create a new module'
exports.builder = { }
exports.handler = function (argv) {
  const { moduleName } = argv
  const folderSrc = join(__dirname, '../scaffolding/create')
  const folderDst = join(process.cwd(), moduleName)
  shell.cp('-Rf', folderSrc, folderDst)
}
```

它将在**当前目录**中创建一个名为`myModule`的新文件夹，以及`lib/scaffolding/create`中的内容。

## dirname vs cwd

当我们使用CLI工具时，我们需要考虑如何对文件进行引用：

- 我们需要在CLI项目中有一个文件吗？ `dirname`
- 我们想知道CLI在哪里执行吗？ `process.cwd()`

## dirname

想象一下，我们在路径`/Users/XXX/my-cli-app/`中有我们的项目，我们正在处理`lib/commands/create.js`文件：

```js
const thisFileFolderPath = __dirname
// === '/Users/XXX/my-cli-app/lib/commands'

const folderSrc = require('path').join(thisFileFolderPath, '../scaffolding/create') 
// === '/Users/XXX/my-cli-app/lib/scaffolding/create'
```

## cwd

`cwd`给我们调用CLI工具的路径。在之前的脚手架示例中，我们希望将文件复制到调用该工具的当前路径。想象一下，我们从`/Users/XXX/documents/another-project/src/

```js
const whereIWasCallPath = process.cwd()
// === /Users/XXX/documents/another-project/src

const folderToCreatePath = require('path').join(whereIWasCallPath, 'example-module')
// === /Users/XXX/documents/another-project/src/example-module
```

## 使用CWD作为可选参数

我的建议是通过我们的应用程序中的一个选项传递CWD，所以我们可以从任何路径调用我们的CLI工具，并将其设置为当前工作目录。 我们可以轻松地做到这一点，添加到我们的index.js文件：

```js
// ...
const { join, resolve } = require('path')
// ...

// Switch CWD if specified from options
const cwd = resolve(yargs.argv.cwd || process.cwd())
process.chdir(cwd);

// ...
yargs
  .help()
  .options({ cwd: { desc: 'Change the current working directory' } })
  .demand(1)
  .argv
// ...
```

## 依赖注入

本节的目标是：

- 我想保持清洁的代码，在`index.js`中添加所有依赖
- 我想要所有每个命令的依赖关系自动加载，所以命令`commands`专注于他们的业务，我们避免了大量的代码
- 我想加载外部依赖项和我自己的项目依赖项
- 我想做可测试的代码:-)

为了匹配这些标准，我们将创建一个如下的结构：

```
+ my-cli-app
  + lib
    + commands
    + modules
```

- 其中所有命令都有对外部包的引用，例如：`console`，`process`，`shell`，`path`，...
- 在所有命令都引用内部模块的地方，我们在`模块`内部。例如，稍后将实现的`日志`。
- 同时，所有模块都引用了外部包和它们自己的模块包。

在开始之前，让我们添加一些我们将使用的包：

```shell
$ node install --save require-dir camelcase chalk
```

## 调整我们的命令

首先，我们将所有的依赖注入到我们的命令中。我们将修改命令的结构看起来像这样：

```js
'use strict'

module.exports = function (dep) {
  let cmd = {}
  cmd.command = '...'
  cmd.desc = '...'
  cmd.builder = {}
  cmd.handler = function (argv) {}
  return cmd
}
```

例如，我们的`lib/commands/create.js`命令的`处理`函数将如下所示：

```js
cmd.handler = function (argv) {
  const { moduleName } = argv
  const { join, shell, process } = dep
  const folderSrc = join(__dirname, '../scaffolding/create')
  const folderDst = join(process.cwd(), moduleName)
  shell.cp('-Rf', folderSrc, folderDst)
}
```

我们可以看到，我们可以从dep变量中加载外部依赖关系，如`join`，`shell`，`process`和`__dirname`。没有人会阻止我们现在mock那些参考！ ;-)

## 外部引用

我们需要修改我们的`index.js`文件来加载所有的外部依赖和我们为命令创建的结构。 我们不能使用`yargs.commandDir`函数了，但我们会做非常类似：

```js
const requireDir = require('require-dir')
const { join, resolve } = require('path')
const shell = require('shelljs')
const yargs = require('yargs')
const colors = require('chalk')

// External dependencies to pass to the commands
let dep = { join, resolve, console, shell, colors, process, __dirname }

// Load commands from folder and pass dependencies
const commandsFn = requireDir(join(__dirname, 'lib', 'commands'))
const commands = Object.keys(commandsFn).map((i) => commandsFn[i](dep))

// Init CLI commands and options
commands.forEach(cmd => yargs.command(cmd.command, cmd.desc, cmd.builder, cmd.handler))
yargs
  .help()
  .demand(1)
  .argv
```

使用这种技巧，我们在一个对象中加载所有依赖项，并传递给每个命令。

## 内部引用

如`外部引用`章节，我们将从`modules`文件夹追加我们的新依赖项。

到目前为止，我们没有创建任何内容，所以让我们添加一个`logger`模块的示例名为`lib/modules/log.js`的新文件：

```js
'use strict'

module.exports = function (dep) {
  let result = {}

  result.debug = function (title, message) {
    const { console } = dep
    const { yellow, blue, gray } = dep.colors
    console.log(yellow('[MyCli]') + (title ? blue(' ' + title) : '') + (message ? gray(' ' + message) : ''))
  }

  return result
}
```

我们将在`lib/commands/say.js`中使用这个`logger`而不是`console`：

```js
cmd.handler = function (argv) {
  const { prefix, name, surname } = argv
  const { log } = dep
  const message = prefix + (name ? ' ' + name : '') + (surname ? ' ' + surname : '')
  log.debug(message)
}
```

现在，我们准备将我们的新模块注入到我们的所有命令中。注意`modules`同时注入其他模块的功能。 打开index.js文件并添加：

我们使用`camelcase`为了使我们的模块的名称生成为驼峰格式。例如：

```js
lib/modules/log.js           =    log
lib/modules/foo-module.js    =    fooModule
lib/modules/bar-module.js    =    barModule
```

## 创建测试

在`注入依赖`章节之后，我们应该准备好一切准备测试我们的应用程序。所有引用都传递给每个模块，这为我们提供了模拟它们的优势。

我们将使用`mocha`和`nyc`进行测试和覆盖。我们需要在全局安装：

```shell
$ npm install --global mocha nyc
```

我们修改`package.json`来运行测试：

```json
 "scripts": {
    "test": "nyc --cache mocha --timeout=8000 --check-leaks test/**/*.js"
  }
```

每次我们想运行测试，我们可以在项目目录上执行命令`npm test`项目。

解释如何使用`mocha`创建测试不在本教程之内，但是我可以展示我们对`say.js`做的一个测试：

```js
'use strict'
/* global describe, before, it */

require('chai').should()

describe('command/say.js', function () {
  describe('.handler', function () {
    // Mocks
    let _latestLogMessage
    const log = { debug: function (msg) { _latestLogMessage = msg } }

    // Target
    const _module = require('../../../lib/commands/say')
    let _target

    before(function () {
      _latestLogMessage = null
      _target = _module({ log })
    })
    it('should print prefix and name', function () {
      _target.handler({ prefix: 'Hello', name: 'CLI' })
      _latestLogMessage.should.equal('Hello CLI')
    })
    it('should print prefix, name and surname', function () {
      _target.handler({ prefix: 'Hello', name: 'CLI', surname: 'ILC' })
      _latestLogMessage.should.equal('Hello CLI ILC')
    })
  })
})
```

## 拆分模块和CLI

到目前为止，我们有一个完整的CLI工具。但我们想导出命令作为我们的包的模块。 它可能在不需要CLI的其他项目中非常有用。

最简单的方法是从我们的`index.js`导出包，并创建另一个名为`bin/cmd.js`的文件，以将其用作CLI起点。

```
+ bin
  - cmd.js
- index.js
```

我们可以从命令中利用我们模块的初始化：

```js
// index.js
'use strict'

const { join, resolve } = require('path')
const camelCase = require('camelcase')
const requireDir = require('require-dir')
const colors = require('chalk')
const shell = require('shelljs')

// External dependencies to pass to the commands
let dep = { join, resolve, console, colors, shell, process }

// Internal dependencies
const inDepFns = requireDir(join(  dirname, 'lib', 'modules'))
Object.keys(inDepFns).forEach(name => {
  dep[camelCase(name)] = inDepFns[name](dep)})

// Load commands from folder and pass dependencies
const commandsFn = requireDir(join(  dirname, 'lib', 'commands'))
const commands = Object.keys(commandsFn).map((i) => commandsFn[i](dep))

// Export commands and modules separatelly
const modules = commands.reduce((pre, cur) => { pre[cur.name] = cur.value; return pre }, {})
module.exports = { commands, modules }
```

正如我们所看到的，我们将导出`{commands，modules}`。CLI工具可以获取已创建的命令，任何其他包可以从模块获取我们的内部模块。

现在，我们将`yargs`和所需的CLI逻辑分成`bin/cmd.js`文件：

```js
#!/usr/bin/env node
'use strict'

const { join, resolve } = require('path')
const yargs = require('yargs')
const { homepage, version } = require(join(__dirname, '../package.json'))
const { commands } = require('../index.js')

// Switch CWD if specified from options
const cwd = resolve(yargs.argv.cwd || process.cwd())
process.chdir(cwd);

// Init CLI commands and options
commands.forEach(cmd => yargs.command(cmd.command, cmd.desc, cmd.builder, cmd.handler))
yargs
  .help()
  .options({ cwd: { desc: 'Change the current working directory' } })
  .demand(1)
  .epilog((homepage ? `| Documentation: ${homepage}\n` : '') + (version ? `| Version: ${version}` : ''))
  .argv
```

## 获取代码

你喜欢这个教程。 有关本教程和源代码的更新可以在下面找到：

[https://github.com/eridem/cli-tutorial](https://github.com/eridem/cli-tutorial)
