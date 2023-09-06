# Shell

## 什么是shell？

Shell是一个用来提供用户交互接口、读取并执行命令的Unix程序，命令可以存在文件中并通过shell来执行。

#### **常见的shell**

* Bash：WSL和基于Linux的Docker的默认shell
* zsh：iTerm的默认shell
* ksh：AIX和Solaris的默认shell

### Bash登录设置文件

#### 交互式登录

当一个交互登录shell执行时，它会执行以下步骤

1. 对所有用户执行`/etc/profile`
2. 按顺序寻找`~/.bash_profile`, `~/.bash_login`, 和 `~/.profile`
3. 执行第一个找到的配置文件

为了保证和非交互式登录有同样的配置，`.bashrc` 也会被执行。

#### 非交互式登录

执行一下步骤：

* 对所有用户执行`/etc/bash.bashrc`
* 执行`~/.bashrc`

## 环境变量

通过`env`就可以查看你的shell的环境变量。

一些常见的环境变量有：

* PATH：shell查看哪个目录来找到运行的程序
* EDITOR：默认的编辑器
* HOME：你的home目录
* LANG：你使用的字符集

