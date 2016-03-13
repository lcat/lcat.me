---
layout: post
title: nodejs child process
tags: [nodejs, child_process]
---

## # child_process.exec(command[, options], callback)

* command {String} 要执行的命令，空格分割
* options {Object}
  - cwd {String} 子进程的当前工作目录
  - env {Object} 环境变量
  - encoding {String} (默认: 'utf8')
  - shell {String} 运行命令的 shell (默认: '/bin/sh' UNIX, 'cmd.exe' Windows, 该 shell 必须接收 UNIX上的 -c 开关 ，或者 Windows上的/s /c 开关 。Windows 上，命令解析必须兼容 cmd.exe。)
  - timeout {Number} (默认: 0)
  - maxBuffer {Number} (默认: 200*1024)
  - killSignal {String} (默认: 'SIGTERM')
  - uid {Number} 设置进程里的用户标识。
  - gid {Number} 设置进程里的群组标识。
* callback {Function} 进程终止的时候调用
  - error {Error}
  - stdout {Buffer}
  - stderr {Buffer}
* 返回: {ChildProcess object}

```js
var child_process = require('child_process');
child_process.exec('ls', function (err, stdout, stderr){
  if (err) {
    console.log("child processes failed with error code: " + err.code);
  }
  console.log(stdout);
});
```

```js
// 传递参数demo
// parent.js
var exec = require('child_process').exec;
var env = { specialNumber = 13 }; 
exec('node child.js', 
  { env: evn }, 
  function (err, stdout, stderr) {
      if (err) {
          throw err;
      }
      console.log(stdout);
  }
);

// child.js
var specialNumber = process.env.specialNumber;

console.log(typeof(specialNumber));
// -> "string"
console.log(typeof(parseInt(specialNumber, 10)));
// -> "number"
```

`child_process.exec`方法会从子进程返回一个完整的**buffer**。这个buffer默认大小限制为200k，超过200k程序会崩溃。当然可以设置maxBuffer，不过不建议。

## # child_process.spawn(command[, args][, options])

* command {String} 要运行的命令
* args {Array} 字符串参数表
* options {Object}
  - cwd {String} 子进程的工作目录
  - env {Object} 环境
  - stdio {Array|String} 子进程的 stdio 配置。
  - customFds {Array} Deprecated 作为子进程 stdio 使用的 文件标示符。(已废弃)
  - detached {Boolean} 子进程将会变成一个进程组的领导者。
  - uid {Number} 设置用户进程的ID。
  - gid {Number} 设置进程组的ID。
* 返回: {ChildProcess object}

```js
var spawn = require('child_process').spawn;
var ls = spawn('ls', ['-lh', '/usr']);

ls.stdout.on('data', function (data) {
  console.log('stdout: ' + data);
});

ls.stderr.on('data', function (data) {
  console.log('stderr: ' + data);
});

ls.on('close', function (code) {
  console.log('child process exited with code ' + code);
});
```

#### options.stdio

stdio 可能是以下几个参数之一:

* 'pipe' - ['pipe', 'pipe', 'pipe'], 默认值
* 'ignore' - ['ignore', 'ignore', 'ignore']
* 'inherit' - [process.stdin, process.stdout, process.stderr] 或 [0,1,2]

1. 'pipe' - 创建在父进程和子进程间的 pipe。管道的父进程端以 child_process 的属性形式暴露给父进程，例如 ChildProcess.stdio[fd] 。为 fds 0 - 2 创建的管道也可以通过 ChildProcess.stdin, ChildProcess.stdout 和 ChildProcess.stderr 来独立的访问。

2. 'ignore' - 在子进程里不要设置这个文件标识，注意，Node 总会为其 spawn 的进程打开 fd 0-2。如果任何一个被 ignored，node 将会打开 /dev/null 并赋给子进程的 fd。



```js
var spawn = require('child_process').spawn;

// Child will use parent's stdios
spawn('prg', [], { stdio: 'inherit' });

// Spawn child sharing only stderr
spawn('prg', [], { stdio: ['pipe', 'pipe', process.stderr] });

// Open an extra fd=4, to interact with programs present a
// startd-style interface.
spawn('prg', [], { stdio: ['pipe', null, null, null, 'pipe'] });
```

## # child_process.fork(modulePath[, args][, options])

* modulePath {String} 子进程里运行的模块
* args {Array} 参数列表
* options {Object}
  - cwd {String} 子进程的工作目录
  - env {Object} 环境
  - execPath {String} 执行文件路径
  - execArgv {Array} 执行参数 (默认: process.execArgv)
  - silent {Boolean} 如果是 true ，子进程将会用父进程的 stdin, stdout, and stderr ，否则，将会继承自父进程, 更多细节，参见 spawn() 的 stdio 参数里的 "pipe" 和 "inherit" 选项(默认 false)
  - uid {Number} 设置进程里的用户标识。
  - gid {Number} 设置进程里的群组标识。
* 返回: ChildProcess 对象

这是 `spawn()` 的特殊例子，用于派生 Node 进程。除了拥有子进程的所有方法，它的返回对象还拥有内置通讯通道。

## # kill child process

```js
var spawn = require('child_process').spawn;

// Make the child sleep for 3000 miliseconds
var child = spawn('sleep', ['3000']);

// Kill the child midway through its sleep.
setTimeout(function () {
  child.kill();
}, 1500);
```

可以发送指定范围信号杀死子进程(不能为`SIGKILL` 和 `SIGSTOP`)

```js
child.kill('SIGUSR2);

process.on('SIGUSER2', function () {
  console.log("What doesn't kill me makes me stronger.");
});
```