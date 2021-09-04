# 第五章：语句  

- [第五章：语句](#第五章语句)
  - [switch 需要注意的点](#switch-需要注意的点)
  - [try 语句块与异常处理](#try-语句块与异常处理)
    - [throw表达式](#throw表达式)
    - [try 语句块](#try-语句块)
    - [标准异常](#标准异常)

## switch 需要注意的点

case 关键字和它对应的值一起被称为 case 标签。case 标签必须是**整型常量表达式**。

## try 语句块与异常处理  

* throw 表达式，异常检测部分使用 throw 表达式来表示它遇到了无法处理的问题。  

* try 语句块：以关键字 try 开始，并以一个或多个 catch 子句结束。try 语句抛出的异常通常会被某个 catch 子句处理。

* 一套异常类，用于在 throw 表达式和相关的 catch 子句之间传递异常的具体信息。  

### throw表达式  

throw 抛出异常将种植当前的函数，并把控制权转移给能处理该异常的代码。

```c++
#include <stdexcept>

void compare(int a, int b)
{
    if (a != b)
    {
        throw runtime_error("Different");
    }
    cout << a + b << endl;
}
```

### try 语句块  

如果寻找处理代码没有找到匹配的 catch 子句，则终止该函数，并在调用该函数的函数中继续寻找。以此类推，沿着程序执行路径逐层回退，直到找到适当的 catch 子句。

```c++
while(cin >> a >> b)
{
    try
    {
        compare(a, b);
    }
    catch (runtime_error err)
    {
        cout << err.what()
             << "\nTry again?" << endl;
    }
}
```

### 标准异常

C++ 标准异常类定义在 4 个头文件中：  

1. exception 头文件定最通用的异常类 exception，只报告异常发生，无额外信息。  

2. stdexcept 头文件。

3. new 头文件定义了 bad_alloc 异常类型。  

4. type_info 定义了 bad_cast 异常类型。
