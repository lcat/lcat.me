---
layout: post
title: windows github gitcafe ssh key
tags: [github, gitcafe, sshkey]
---

## github ssh key

1. 下载安装**[git](https://git-scm.com/downloads)**
2. 打开Git Bash
3. 执行 `ssh-keygen -t rsa -C "邮箱"` 
会在~/.ssh 下生成 私钥`id_rsa`及公钥`id_rsa.pub`
4. 执行 `clip < ~/.ssh/id_rsa.pub` 复制公钥内容,
复制完，登录github，打开settings页面，找到`SSH keys` 

```
osx 下复制为 `pbcopy > ~/.ssh/id_rsa.pub`
```

5. 点击 `Add SSH keys` 把复制内容粘贴到key里面，保存。
6. 执行 `ssh -T git@github.com` 测试是否配置成功。

## gitcafe ssh key

1. 执行 `ssh-keygen -t rsa -C "邮箱" -f ~/.ssh/gitcafe` 
会在~/.ssh 下生成 私钥`gitcafe`及公钥`gitcafe.pub`
2. `~/.ssh/config` 中指定证书名称，如果没有 config 文件的话就新建一个 (Linux 平台的话需使用该命令 `chmod 644 ~/.ssh/config` 来改变 config 文件权限。
3. 编辑 config 如下：

```
Host gitcafe.com www.gitcafe.com
  IdentityFile ~/.ssh/gitcafe
```

4. 执行 `clip < ~/.ssh/gitcafe.pub` 复制公钥内容,
复制完，登录gitcafe，打开账户设置页面，找到`SSH公钥管理` 
5. 点击 `添加新的公钥` 添加公钥，保存。
6. 执行 `ssh -T git@gitcafe.com -i ~/.ssh/gitcafe` 测试是否配置成功。

