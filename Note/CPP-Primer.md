# C++ Primer 读书笔记

## 第一章 开始

不同的编译器使用的c++文件后缀会不同，常见的包括.cc/.cxx/.cpp/.cp/.C。

g++ 编译选项 `-Wall` 表明显示所有的警告。

iostream 包含两个基础类型 `istream` 和 `ostream`，分别表示输入流和输出流。一个流就是一个字符序列，是从IO设备中读出或写入IO设备的。

在标准库中定义了4个IO对象，cin 为 istream 类型，cout 为 ostream 类型，cerr 和 clog 都为 ostream 类型，通常用cerr输入警告和错误消息，用clog输出程序运行时的一般性信息。

`std::endl` 的作用不仅仅是打印回车符，还能将与设备关联的缓冲区(buffer)中的内容刷到设备中。

`std::cin > i;` 如果在Mac OS/linux 的终端输入 ctrl+d 或在windows的控制台输入 ctrl+z，则返回的 ostream 的状态变为无效，在转换为 bool 类型时，其值为 false，因此可以使用 
```c++
for(cin >> value){
    // do something with value
}
```
的方式处理多个输入。
