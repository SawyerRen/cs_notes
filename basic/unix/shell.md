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

## 输入输出流

shell提供了三种I/O的流。

* `STDOUT`: (标准输出) 通常显示在终端上
* `STDIN`: (标准输入) 数据进入到Shell中
* `STDERR`: (标准错误) 防止输出的噪音，一般被写入到别的地方去

### 输入和输出的重定向

* 重定向输出

```
$ ls -l > listing # Creates new file, overwriting if it exists, containing output
$ ls  -l >> listing # Creates new file, appending if it exists, containing output
```

* 重定向输入

```
$ cat < index.md
```

* 重定向错误

```
$ ls -l 2> errorfile
$ ls -l 2>> errorfile
```

## 复杂命令

shell允许多个命令的组合，一般有以下几种机制：

#### 子shell

创建一个新的shell，这个shell会在命令执行完后消失，这个shell可以通过括号()来创建，组合多个命令可以更好的控制命令的执行顺序。

#### 管道组合

&#x20;执行一个命令并且将输出作为下一个命令的输入，命令之间通过管道符| 来分隔

#### 顺序组合

命令都按照顺序执行

```
$ sleep 10; echo "Done"
```

#### 平行组合

所有的命令同时执行，除了最后一个，别的命令都在后台执行。

```
$ gvim text1 & gvim text2 & vim text3
```

#### 并行组合

只有前一个命令执行成功，才会执行下一个命令

```
$ mkdir NEW_DIR && cd NEW_DIR
```

#### 异或组合

只有前一个命令执行不成功，才会执行下一个命令

```
$ ./myprog aFile || vi myprog # myprog works...or else we go back to editing it ;)
```
