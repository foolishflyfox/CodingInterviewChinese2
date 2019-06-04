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

## 第二章 变量和基本类型

在 c++ 进行变量运算时，只是进行位运算，并不考虑变量类型，例如下面的例子：
```cpp
unsigned int a1 = 10;
int a2 = 10;
int b = -42;
cout << hex << a1+b << endl << a2+b << endl;
```
输出的两行是完全相同的，都是 `ffffffe0`，不过 unsigned int 和 int 相加的结果会被认为是 unsigned int 类型。

变量之间的类型转换规则：
- 相同大小的有符号与无符号类型转换，其内存中的表示不变，只是解释方式发生变化；
- 小数与整数的转换
    - 小数与有符号整数：将小数类型小数点后面的部分去掉
    - 小数与无符号整数：小数 -> 有符号整数 -> 无符号整数

小数可以用指数表示法，如 1.23e5, 1.23E5，默认的浮点数字面量的类型为double。

字符中的转义序列：
1. \a: 表示报警响铃符，在macOS 和 linux下测试都有效；
1. \v: 纵向制表符；
1. \b: 退格符，相当于键盘上的back；
1. \r: 回车符；

初始化不是赋值，初始化的含义是创建变量时赋予其一个初始值，而赋值的含义是把对象的当前值擦除，而以一个新值来替代。

初始化的方式：
```c++
int units_sold = 0;
int units_sold = {0};
int units_sold{0}; // c++11开始被全面应用
int units_sold(0);
```

建议初始化每一个内置类型的变量，虽然并非必须这么做，但如果我们不能确保初始化后程序安全，那么这么做不失为一种简单可靠的方法。

任何包含了显示初始化的声明即成为定义。我们能给由extern关键字标记的变量赋一个初始值，但这么做也就抵消了extern的作用，extern 语句如果包含了初始值，如`extern double pi=3.1416`，则不再是一个声明，而是一个定义。

引用(reference)为对象起了另一个名字，引用必须进行初始化：`int& refVal = ival;`

因为在声明语句中，指针的类型实际上被用于指定它所指向对象的类型，所以二者必须匹配。如果指针指向了一个其他类型的对象，该对象的操作将发生错误。

nullptr是c++11中新引入的特殊类型字面量，可以被转化为任意其他的指针类型。

经常有观点会误认为在定义语句中，类型修饰符(*或&)作用于本次定义的全局变量，造成这种错误看法的原因有很多，其中之一是我们可以把空格卸载类型修饰符和变量名中间。建议类型声明时，修饰符和基本类型分开，例如`int *p1, *p2;`

面对一条比较复杂的指针或引用的声明语句时，从左到右阅读有助于弄清楚它的真实含义。

如果想在多个文件之间共享const对象，必须在变量的定义和声明前都添加 `extern` 关键字。

引用必须指向一个变量，而常量引用不一定，例如：
```c++
int &v1 = 10; //错误
const int &v2 = 10; //正确
```

const 指针是指不能改变指向的指针类型，因此必须进行初始化，例如 `int* const cp;`。

指针本身是不是常量以及指针所指的是不是一个常量是两个相互独立的问题，用名词顶层const(top-level const)表示指针本身是一个常量，用底层const(low-level const)表示指针所指向的对象是一个常量。

在 C++11 中规定了新的类型别名的方法，使用**别名声明**(alias declaration)类定义类型的别名，例如 `using SI = Sales_item;`。

需要注意的一点：
```c++
typedef char *pstring; // pstring 表示 char* 是基本类型
const pstring cstr = 0; // 等价于 char* const cstr = 0;
```

**auto类型说明符**：编程时常常需要将表达式的值赋给变量，这就要求在声明变量的时候清楚地知道表达式的类型。然而要做到这一点并非那么容易，有时甚至根本做不到。为了解决这个问题，c++11中引入了**auto**说明符，用它就能让编译器替我们去分析表达式所属的类型。和原来那些只对应一种特定类型的说明符(如double)不同，auto让编译器通过初值来推算变量的类型。显然，auto定义的变量必须有初始值：
```c++
auto item = val1 + val2;
```
编译器根据val1和val2的相加结果来推断item的类型，如果val1和val2是Sales_item的对象，则item的类型就是Sales_item。

使用auto也能在一条语句中声明多个变量。因为一条声明语句只能有一个基本数据类型，所以该语句中的所有变量的初始基本数据类型必须都一样。
```c++
auto i=0, *p=&i;
auto sz=0, pi=3.14; //错误，sz和pi的类型不一致
```
auto一般会保留底层const而忽略顶层const。

## 第三章 字符串、向量和数组

头文件中不应该包含using声明。

string 对象的初始化方式：
```c++
string s1; // 默认初始化，s1是一个空串
string s2(s1); // 等价于 string s2 = s1;
string s3("value"); //用字面量值进行初始化，等价于string s3 = "value";
string s4(n, 'c'); //用n个'c'进行初始化
```

string 的操作：`getline(is, s); // 从is中读取一行赋给s，返回is`。

因为某些历史原因，也为了和C兼容，所以C++语言中的字符串字面量并不是标准库string的对象。

对字符的处理函数需要包含 `cctype` 头文件：
- `isalnum(c)`: 判断c是否为数字0-9或字母；
- `isdigit(c)`: 判断c是否为数字0-9；
- `isalpha(c)`: 判断c是否为字母；
- `iscntrl(c)`: 判断c是否为控制字符，如\v,\b,\n,\a等；
- `islower(c)`: 判断c是否为小写字母；
- `isupper(c)`: 判断c是否为大写字母；
- `isprint(c)`: 判断c是否可打印；
- `ispunct(c)`: 判断c是是否为标点；
- `isspace(c)`: 判断c是否为空白符；
- `isxdigit(c)`: 判断c是否为十六进制数字；
- `tolower()/toupper()`

c++11 中遍历容器的方法：
```c++
for (auto v: container){
    // do something with v
}
```
向 `vector` 容器后追加元素，使用 `push_back` 函数，删除最后的元素，使用`pop_back` 函数。

获取 vector 的迭代器可以用 `begin/cbegin` 和 `end/cend`。获取方向的迭代器可以用 `rbegin/crbegin`和`rend/crend`，都是从begin到end递增。

**但凡是使用了迭代器的循环体，都不要向迭代器所属的容器添加元素。**

两个迭代器可以相加减，类型是名为 difference_type 的带符号整形。

数组与vector不同，其大小是固定的，不能随意向数组中添加元素。因为数组的大小固定，因此对某些特殊的应用来说程序的运行时性能较好，但损失了一些灵活性。

对于大多数应用来说，使用标准库string要比使用C风格字符串更安全，更高效。

将string类型转变为const char* 类型以与c语言兼容，可以使用string的c_str方法。但我们无法保证c_str函数返回的数组一直有效，事实上，如果后续的操作改变了s的值，返回的指针可能就失效了。

使用数组对vector进行初始化: `vector<int> vs(a+1, a+4)` 表示用a数组的第2~4元素对vs进行初始化，`vector<int> vs(begin(a), end(a))` 表示用a所有元素初始化vs。

**现代的C++程序应该尽量使用vector和迭代器，避免使用内置数组和指针。尽量使用string，避免c语言风格字符串。**
