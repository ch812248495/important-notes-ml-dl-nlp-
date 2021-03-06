# extern
## 基本解释
extern可以放在函数或者变量前面.用来标示变量或者函数是在其他的文件里面的,如果编译器遇到了,就要去相应的外部文件寻找定义.
如果在头文件中遇到`extern int g_Int`, 它的作用是声明函数或者全局变量作用范围的关键字.声明的函数和变量可以在本模块或者其他模块中使用. 这只是一个声明而不是定义,所以在编译阶段,当模块 B 包含了这个A 的头文件,虽然并没有定义,但是在编译阶段不会报错,在连接的时候可以从模块A的代码块中获取此函数.

- 声明而不定义
  C++允许让一个文件使用其他文件中定义的变量和函数,因此变量和函数的声明与定义是分离的, 在定义的时候并不需要` extern` 关键字.仅仅需要在头文件中声明的时候指出.
```C++
extern int i;  //声明i而非定义
int j;         //声明并定义i
```
- 共享 const对象
通常 const 对象仅仅对本文件有效,当多个文件中定义了同名的 const 对象,他们是不同的,如果需要多个文件共享 const 对象,则那个需要共享的 const 所在的文件需要在声明和定义中指出.
```cpp
//file1.cpp定义并初始化和一个常量，该常量能被其他文件访问
extern const int bufferSize = function();
//file1.h头文件
extern const int bufferSize; //与file1.cpp中定义的是同一个
```
不管是声明或者定义,都需要有 extern 关键字,此 const 对象并非在这个文件中独有,可以共享
- 模板的实例化
两个独立源文件提供了相同的模板和模板实例化参数之后,会带来双倍的开销,因此可以共享一个实例化通过`extern`函数.


## 问题
1. 在源文件中定义数组: `char a[6]`, 那么在另一个文件中进行以下声明 `extern char *a`是错误的.
    原因在于两者形式并不匹配.另外在*. c 中声明了一个全局变量,那么在*. h 中应该被声明,这样其他*. c 需要引用的时候,可以直接 include 这个头文件

2. 不要在 file1.h 中直接定义并声明外部变量,比如:
```cpp
extern char g[] = "123";
```
file1.cpp中包含这个头文件, file2.cpp 为了引用,也包含了这个头文件,这个变量将被定义两次. **变量可以被多次声明,但是不能被多次定义,在连接的时候,将会报错.**
请只在头文件中声明.

3. extern 和 static
`extern`: 变量已经在其他地方定义过了
`static`: 静态变量,存储在静态区而非栈上面.
static 是内部连接, 其定义和声明同时进行,作用域只是这个编译单元.一般放在源文件里而不是头文件中,和 extern 是冲突的.

4. extern 和 const
两者可以共存, 一起用的时候, 特性与 extern 相同.
