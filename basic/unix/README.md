# Unix

Unix 哲学强调构建简单、简短、清晰、模块化和可扩展的代码，与整体设计相比，有利于可组合性。

很多系统的组件都是文件的形式，包括设备（如I/O硬件），我们可以通过命令将数据写入到文件中，也可以通过命令将数据写到终端上，如

```sh
echo hello > /dev/tty
```
