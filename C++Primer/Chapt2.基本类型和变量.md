# 第二章 基本类型和变量  

## 类型转换  

隐式转换规则：

* 非 Bool 类型 -> Bool 类型，0 -> False，其他 -> True。  

* Bool 类型 -> 非 Bool 类型，False -> 0，True -> 1。  

* 浮点数 -> 整数，去掉小数部分。  

* 整数 -> 浮点数，小数部分取 0，精度可能发生损失。  

* 无符号数赋值，初始值超过类型表示最大值时，结果是初始值对无符号数表示数值最大值取模后的结果。  

* 有符号数赋值，超过表示范围时，结果是未定义的。  

* 有符号数 + 无符号数 -> 无符号数。  

## 字符常量  

字符常量可以用转义序列表示。  

* `'\'` 后面跟 1，2，3 个八进制数字，超过 3 个只取前三个与 `'\'` 构成转义。  

* `'\x'` 后面跟一个或多个 16 进制数字。  

## 赋值与初始化  

C++ 中初始化和赋值是两个不同的概念，初始化的含义是创建变量时赋予其一个初始值，而赋值的含义是把对象的当前值擦除，而以一个新值来代替。  

* 列表初始化：`int units_sold{0};` 用花括号来初始化变量。当用于内置变量时，如果列表初始化的初始值存在丢失风险，编译器会报错（如 `int units_sold{1.1}`）。  

* 默认初始化：定义变量时没有指定初始值。对于内置类型，函数体外定义的变量被初始化为 0，函数体内的变量**不被初始化**。  

* 类内初始值：创建对象时，类内初始值将用于数据成员，可以用花括号或等号右边初始化，不能放在圆括号里面。 

## 声明与定义  

* `extern int i;`：声明。  

* `int j;`：定义。  

* `extern double pi = 3.14`：定义（编译器警告，不报错）。  

## 引用和指针

* `int *a, *b;`  

* `int *a = nullptr;`  

* `int *&pa = a;`  

## const 限定符  

* 默认情况下，const 对象被设定为**仅在文件内有效**。多个文件出现同名 const 变量时，等同于在不同文件中分别定了独立的变量。  

* 为了解决上述问题，实现多个文件使用同一个 const 变量，在所有文件中，都加上 `extern` 关键字，一个文件用于声明和定义（编译器不警告和报错），其他文件都表示声明。  

* **Const 与指针**：  
    * 常量的指针：  
        ```c++
        const double pi = 3.14;
        double *ptr = &pi; // 错误，ptr是一个普通指针
        const double *cptr = &pi; // 正确
        ```

    * const 指针：
        ```c++  
        int errNumb = 0;
        int *const curErr = &errNumb; // curErr 将一直指向 errNumb
        ```

* 顶层与底层 const  

    * 顶层 const 表示指针本身是个常量。  

    * 底层 const 表示指针所指向的对象是一个常量。  

## constexpr 变量  

声明为 const 的变量不一定在编译阶段就可以获取变量的值，如：`const int sz = size()` 这样的定义，sz 虽然本身是一个常量，但是它的具体值必须在运行时才可以获取。  

C++ 11 允许把变量声明为 `constexpr` 类型，由编译器来验证变量是否是常量表达式。声明为 `constdexpr` 的变量必须在编译阶段确定值。同时，C++ 11 也扩充了 `constexpr` 函数，这种函数必须可以在编译阶段确定结果，可以用来初始化 `constexpr` 变量。  

```c++
const int *p = nullptr; // 底层 const
constexpr int * q = nullptr; // 顶层 const
// int *constexpr q = bullptr; // 语句错误
constexpr const int *ptr = nullptr;  // const 底层，constexpr 顶层
```

## 类型别名  

关键字 `typedef` ，C++ 11 新标准添加了 `using` 关键字作为别名声明。

```c++
typedef double wages;
typedef wages base, *p; // p 是 double* 的同义词
using myDouble = double;
using myDoublePtr = double*;

const p cstr = nullptr; // cstr 是一个指向 double 的常量指针
```

## auto 类型说明符（C++ 11）  

`auto` 类型说明符，可以让编译器自动分析表达式所属的类型。  

* `auto` 定义的变量必须有初始值。  

* 声明多个变量时，必须使用相同的基本类型。  

* 当初始值是引用类型时，真正参与初始化的时应用对象的值。
    ```c++
    int i = 0, &r = i;
    auto a = r;     // a 是一个整数（int）
    ```

* `auto` 一般会忽略掉顶层 `const`，同时底层 `const` 将会保留。
    ```c++
    const int ci = i, &cr = ci;
    auto b = ci;    // 整数（int），ci 的顶层 const 被忽略
    auto c = &ci;   // 指向整数常量的指针（取底层 const )
    const auto f = ci;  // 整数常量
    ```

## decltype 类型提示符（C++ 11）    

类似 `auto` 的自动化推导类型，但是可以不使用表达式的初始化变量，而是指定一种变量类型。  

```c++
decltype(f()) sum = x;  // sum 的类型就是函数 f 的返回类型
```

* 如果 `decltype` 使用的表达式不是一个变量，则返回表达式结果对应的类型。  
    ```c++
    int i = 42, *p = &i;
    decltype(*p) c = i;     // c 是 int& 型
    ```

* 对于 `decltype` 使用的表达式，变量与变量加上一个括号是不同的。变量加上括号，表示一个表达式，可以作为赋值语句左值的特殊表达式，会得到引用类型。  
    ```c++
    int i = 0;
    decltype((i)) d;    // 错误，d 是引用类型，必须初始化
    decltype(i) e;      // 正确，e 是一个（未初始化的）int
    ```

## 自定义数据结构 - struct  

C++ `struct` 和 C 语言中不同，增加了数据元素初始化，可以直接使用类名定义变量。  

```c++
struct Sales_data
{
    std::string bookNo;
    unsigned int units_sold = 0;    // 等号右边
    double revenue{0.0};    // 花括号列表初始化，不能用圆括号
};
Sales_data accum, trans;
```




