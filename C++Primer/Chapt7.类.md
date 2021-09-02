# 第七章：类

- [第七章：类](#第七章类)
  - [定义类](#定义类)
    - [this 指针](#this-指针)
  - [构造函数](#构造函数)
  - [访问控制和封装](#访问控制和封装)
  - [定义类型成员](#定义类型成员)
  - [可变数据成员](#可变数据成员)
  - [委托构造函数（C++ 11）](#委托构造函数c-11)
  - [默认构造函数的作用](#默认构造函数的作用)
  - [静态成员](#静态成员)



* 定义在类函数内部的函数是隐式的 `inline` 函数。   
* 构造函数没有返回类型，不能被声明为 `const` 。  
* 如果成员是 `const` ，引用，或者属于某种未默认提供构造函数的类类型，则必须通过构造函数初始值列表为这些成员提供初始值。 
* 为构造函数声明 `explicit` 可以阻止类的隐式类型转换。而且只支持直接初始化。
  ```c++
  explicit Sales_data(const std::string &s): bookNo(s) { }

  Sales_data item1("string"); // "string" 隐式转换成 string 类型
  Sales_data item2 = string("string"); // 错误，string -> Sales_data 不允许
  ``` 

## 定义类  

### this 指针

当调用类的成员函数时，其实是在替某个对象调用。成员函数可以通过一个名为 `this` 的额外隐式参数来访问调用它的对象。对于类成员函数，也可以不用 `this` 指针，而直接访问成员。可以认为编译器重写了调用：
```c++
Sales_data total;
total.isbn();
/* ----------------- */
// Sales_data::isbn(&total);
// std::string Sales_data::isbn(Sales_data *const this)
```

`this` 是个常量指针（**顶层常量**），不允许改变 `this` 指向的地址。这也意味着，不能把默认 `this` 绑定到一个常量对象上（**底层 `const` 转换时变成 非底层 `const`**）。下面的声明方式声明 `this` 同时是底层 `const` 指针：
```c++
// std::string Sales_data::isbn(void) const;
const Sales_data total;
total.isbn();
/* ----------------- */
// Sales_data::isbn(&total);
// std::string Sales_data::isbn(const Sales_data *const this);
```

常量对象不可以访问没有设置底层 `const` 的成员函数，非常量对象可以访问 底层 `const` 的成员函数。

## 构造函数  

* **合成的默认构造函数**是编译器在类没有声明任何构造函数时，自动生成的。
  * 如果存在类内的初始值，则用它来初始化成员。
  * 否则，默认初始化该成员。
  * 一旦存在定义其他构造函数，编译器就不会生成**合成的默认构造函数**。
* 可以通过 `= default` 要求编译器生成构造函数。
  ```c++
  class Sales_data
  {
      Sales_data() = default;   // 类内部默认 inline
      Sales_data(std::istream &);
  }
  ```
* 构造函数**初始化列表**，对于没有显式初始化的数据成员，将以合成默认构造函数相同的方式隐式初始化。初始化顺序与变量在类中的定义顺序有关，与**初始化列表顺序无关**。
  ```c++
  class Sales_data
  {
      Sales_data(const std::string &s): bookNo(s) { }
      Sales_data(const std::string &s, unsigned n, double p):
                 bookNo(s), units_sold(n), revenue(p*n) { }
  }
  ```
* 类外部定义构造函数。上面的列表初始化也可以在类外实现。
  ```c++
  Sales_data::Sales_data(std::istream &)
  {
      //
  }
  ```

## 访问控制和封装  

* `public` 整个程序可以访问。
* `private` 可以被类成员访问。
* `struct` 默认成员是 `public` ， `class` 默认是 `private` 。

友元函数，可以访问类非公有成员，不受类的访问控制级别约束，在类里面任意地方加上 `friend` 声明即可。也可以定义友元类，友元成员函数。

## 定义类型成员  

类型成员必须先定义，后使用，可以用 `typedef` ， `using` 。


## 可变数据成员  

可变数据成员在 const 成员函数和 const 对象中可以修改，可以通过声明中加入 `mutable` 关键字做到这一点。  

## 委托构造函数（C++ 11）  

委托构造函数可以使用所属类的其他构造函数执行自己的初始化过程。执行过程中，**先执行受委托的函数**的初始值列表和函数体，再返回委托者的函数体执行代码。  

```c++
class Sales_data;
void read(istream &is, Sales_data &data);
class Sales_data
{
    string bookNo;
    unsigned int units_sold;
    double revenue;
    friend void read(istream &is, Sales_data &data);

public:
    Sales_data(string s, unsigned cnt, double price):
        bookNo(s), units_sold(cnt), revenue(price) { }
    // 下面均是委托构造函数
    Sales_data(): Sales_data("", 0, 0) { }
    Sales_data(string s): Sales_data(s, 0, 0) { }
    Sales_data(istream &is): Sales_data()  { }
};

void read(istream &is, Sales_data &data)
{
    is >> data.bookNo >> data.units_sold >> data.revenue;
}

Sales_data(cin);
```

## 默认构造函数的作用  

当对象被默认初始化或值初始化时，将自动执行默认构造函数。

* 默认初始化  
  * 块作用域内不使用任何初始值定义一个非静态变量。  
  * 一个类本身含有类类型的成员且使用合成的默认初始化函数。
  * 类类型成员没有出现在构造函数的初始值列表中。
* 值初始化
  * 数组初始化时，提供的初始值数量小于数组的大小。
  * 不适用初始值定义一个局部静态变量。
  * 形如 `T()` 表达式显式请求值初始化。

## 静态成员  

* 类的静态成员存在于任何对象之外，对象中不包含静态成员有关的数据。  
* 静态成员函数不包含 `this` 指针。  
* 静态成员函数可以在类内、类外定义，类外定义时不能再加 `static`。  
* 静态成员变量一般不能再类内初始化，必须在类外定义并初始化静态成员变量。但是，如果可以为静态变量提供 const 来初始化 constexpr 类型的静态成员变量。  
* 静态成员变量可以是不完全类型（类前向声明），可以作为类成员函数的默认实参。

```c++
class Account
{
    static double interestRate;
    static constexpr int period = 30;
    static Account mem1;  // 不完全类型
public:
    static double rate()  { return interestRate; }
    static void rate(double);
    static double initRate();
}
double Account::interestRate = initRate();  // Account:: 剩余部分作用域位于类内
void Account::rate(double newRate)
{
    interestRate = newRate;
}
```