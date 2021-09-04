# 第六章：函数

- [第六章：函数](#第六章函数)
  - [可变形参](#可变形参)
  - [函数返回值](#函数返回值)
  - [辅助调试的预处理](#辅助调试的预处理)
  - [重载函数的匹配规则](#重载函数的匹配规则)
  - [函数指针](#函数指针)

## 可变形参  

除了与 C 语言兼容的 `...` 可变参数形式，C++ 11 还提供了两种新的方法：标准库的 `initializer_list` 和可变参数模板。  

* `initializer_list` 列表中的类型必须一样，对象中的元素永远是常量值。

## 函数返回值

* 函数的返回值可以是引用类型。返回引用的函数得到左值，其他返回类型得到右值，意味着可以像使用其他左值一样使用返回引用的函数的调用。

    ```c++
    char &get_val(string &str, string::size_type ix)
    {
        return str[ix];
    }

    string s("hello world");
    get_val(s, 0) = '1';
    ```

* 返回数组指针的函数
  
    ```c++
    int arr[10];
    int (*parr)[10] = &arr; // 维度必须一致

    // 可以声明如下形式的函数，用来返回数组指针
    // Type (*function(parameter_list)) [dimension];
    int (*func(int i)) [10];
    ```

    C++ 11 新标准支持使用**尾置返回类型**，任何函数的定义都可以使用尾置返回，这种对于返回形式复杂的函数很有用，返回类型需要用 `auto` 说明符。

    ```c++
    auto add(int i, int j) -> int;
    auto func(int i) -> int(*)[10]; // 这样定义和上面例子完全一样
    ```

    还可以使用 `decltype` 

    ```c++
    int arr[10] = {0};
    
    decltype(arr) *func(int i);     // 这也与上面的例子一样
    ```

## 辅助调试的预处理

* `assert` 预处理宏： `assert` 宏在 `cassert` 头文件中，使用一个表达式作为他的条件。首先对表达式求值，如果表达式为假，则输出信息，并退出程序；为真，`assert` 什么也不做。

* `NDEBUG` 预处理变量：`assert` 依赖这个变量。如果定义了 `NDEBUG`，`assert` 什么也不做。

预处理器还定义了四个对程序调试很有用的变量：

1. `__FILE__` ：存放文件名的字符串字面值。
2. `__LINE__` ：存放当前行好的整形字面值。
3. `__TIME__` ：存放文件编译时间的字符串字面值。
4. `__DATA__` ：存放文件编译日期的字符串字面值。

## 重载函数的匹配规则

* 首先，确定**候选函数**。候选函数与被调用函数同名，且其声明在调用点可见。
* 其次，选出**可行函数**。可行函数是与本次调用提供的实参数量相等，且每个实参类型与对应的形参类型相同（或可以转换成形参类型）。
* 寻找**最佳匹配**：
  * 该函数的每个实参的匹配都不劣于其他可行函数需要的匹配。
  * 至少有一个参数的匹配由于其他可行函数提供的匹配。
  
  为了确定最佳匹配，类型转换的匹配等级如下：
  1. 精确匹配
     * 实参与形参类型相同
     * 实参数组类型或函数类型转换成对应的指针类型
     * 像实参添加顶层 `const` 或者从实参中删除顶层 `const`
  2. 通过 `const` 转换实现的匹配
  3. 通过类型提升实现的匹配
  4. 通过算数类型转换实现的匹配
  5. 通过类类型转换实现的匹配


## 函数指针  

函数指针可以不用 取地址符 ，使用时也可以不用 解引用。

```c++
bool compare(int a, int b);
bool (*pf)(int a, int b);

pf = compare;
pf = &compare;  // 等价上式
bool b1 = pf(1, 2);
bool b2 = (*pf)(1, 2);  // 等价上式
```  

指向重载函数的函数指针，必须与对应重载函数中的一个精确匹配。  

同样的，函数指针可以作为函数参数形参，也可以作为返回值：

```c++
// --- 作为参数 ---
void useBigger(const string &, const string &, bool pf(const string &, const string &));
void useBigger(const string &, const string &, bool (*pf)(const string &, const string &));
// 这两个声明完全等价

// 比较方便的方式是使用类型别名
bool lengthCompare(const string &, const string &);
typedef bool(*FuncP)(const string &, const string &);
typedef decltype(lengthCompare) *FuncP2;

// --- 作为返回值 ---
// 直接声明
bool (*func(int))(const string &, const string &);
// 尾置声明
auto func(int) -> bool (*)(const string &, const string &);
// decltype 声明
decltype(lengthCompare) *fun(int);
```

