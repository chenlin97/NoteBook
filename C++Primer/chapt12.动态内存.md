## 第十二章：动态内存  

---
- [第十二章：动态内存](#第十二章动态内存)
- [智能指针](#智能指针)
  - [shared_ptr](#shared_ptr)
  - [直接管理内存](#直接管理内存)
  - [new 与 shared_ptr 结合](#new-与-shared_ptr-结合)
  - [unique_ptr](#unique_ptr)
  - [weak_ptr](#weak_ptr)
- [动态数组](#动态数组)
  - [new 和 delete 数组](#new-和-delete-数组)
  - [智能指针与动态数组](#智能指针与动态数组)
  - [allocator 类](#allocator-类)

---

## 智能指针  

智能指针的头文件定义在 `memory` 中。  

### shared_ptr  

* `shared_ptr` 允许多个指针指向同一个对象。 shared_ptr 类似 vector ，也是模板类，创建指针时需要指明类型信息。  
* 默认初始化的智能指针中保存着一个空指针。指针使用方式与普通指针一样，可以直接解引用。  
* 使用 `make_shared` 标准库函数动态分配对象，并返回 shared_ptr 对象。  
* 拷贝 `shared_ptr` 对象会让**引用计数**递增， `shared_ptr` 赋新值或销毁时，引用计数递减。引用计数到 0 时，自动释放 `shared_ptr` 指向的动态分配对象。  
* `return shaored_ptr` 对象指针会先拷贝（引用计数递增），再销毁原 `shared_ptr` 对象指针（引用计数递减）。  
* 对于存在在容器中的 `shared_ptr` ，不用时最好使用 `erase` 销毁。  

  ```c++
  shared_ptr<int> p1 = make_shared<int>(42);  // 括号内是初始化参数
  ```

### 直接管理内存  

`new` 和 `delete` 是直接内存管理的一对操作符。

* new 分配对象的内存，返回指针。默认情况下，分配的对象使用默认初始化（内置类型对象的值将会是未定义），类会使用默认构造函数。也可以使用直接初始化，支持**构造方式**（圆括号），**列表初始化**（花括号方式）。  
* 使用 delete 释放动态内存。首先销毁给定指针指向的对象，然后释放对应的内存。  

### new 与 shared_ptr 结合  

* 接受指针参数的智能指针构造函数是 `explicit` 的，不能隐式转换。  
* 可以用 `reset()` 成员函数使智能指针指向一个新的对象。  
* shared_ptr 创建时，可选传递一个指向删除器函数的参数。删除器函数的传入参数必须是对应类型的指针。  

### unique_ptr  

* `unique_ptr` 拥有指向的对象，某个时刻只能有一个 `unique_ptr` 指向给定对象。当 `unique_ptr` 被销毁时，指向的对象也相应被销毁。  
* `unique_ptr` 也可以接受可选的释放内存参数指针对象，但是需要指定这个对象的类型（与 `shared_ptr` 不一样）。因此，重载删除器会影响到 `unique_ptr` 的类型。  
* 可以使用 `release()` 成员函数转移指向对象的所有权。  
* 返回 `unique_ptr` ，会执行一种特殊的拷贝，使得可以返局部 `unique_ptr` 局部对象。  
  ```c++
  void end_connection(connection *p) { disconnect(*p); }
  connection c1 = connect(&d1); 
  shared_ptr<connection> p1(&c1, end_connection);

  connection c2 = connect(&d2); 
  unique_ptr<connection, decltype(end_connection)*> p2(&c2, end_connection);

  p2.reset(p1.release());   // 释放 p2 指向的对象， p1 对象的所有权转移到 p2 .
  ```

### weak_ptr  

`weak_ptr` 不控制指向对象的生存周期，指向由一个 shared_ptr 管理的对象。  

## 动态数组  

### new 和 delete 数组  

* 可以分配空数组，返回的指针类似尾后指针。  
* 分配数组得到的指针是元素类型指针，不能得知数组长度，不能使用 begin() 和 end() 。同理，也不能用范围 for 语句。
```c++
int *pia = new int[10]; // 默认初始化，int 对应没有初始化。
int *pib = new int[10](); // 值初始化，0。
int *pic = new int[10]{1, 2, 3, 4}; // 前四个提供初始值，后面使用值初始化。
delete [] pia, pib, pic;

using arrT = int[10];
pia = new arrT;
delete [] pia;
```

### 智能指针与动态数组  

* `unique_ptr` 存在可以管理数组的版本，再声明对象类型时跟上一对 `[]` 即可。  
* `unique_ptr` 可以使用下标运算符，但是不能用点和箭头运算符（没有意义）。  
* `shared_ptr` 不直接支持管理数组。如果希望使用 `shared_ptr` 管理数组，必须自己定义删除器。  

```c++
unique_ptr<int[]> up(new int[10]);
up.release(); // 自动调用 delete[] 销毁其头指针

shared_ptr<int> sp(new int[10], [](int *p) { delete[] p; })
for (size_t i = 0; i != 10; ++i)
{
    *(sp.get() + i) = i;  // 使用 get() 获取内置指针
                          // 智能指针不支持算术运算
}

sp.reset(); // 使用自定义的 lambda 释放数组。
```

### allocator 类  

* `new` 把内存分配和对象构造组合在了一起。 `delete` 把对象析构和内存释放组合在了一起。 `allocator` 类可以把内存分配与对象构造分离。  
* 拷贝和填充 allocator 分配的对象 `uninitialized_copy(b,e,b2)` ， `uninitialized_copy_n(b,n,b2)` ， `uninitialized_fill(b,e,t)` ， `uninitialized_fill_n(b,n,t)` 。这四个函数都需要调用者确保目的地址有足够的空间可以完成拷贝和填充。  

```c++
allocator<T> a;
a.allocate(n);  // 分配 n 个未初始化的 T 类型对象，返回 T* 类型指针。
a.deallocate(p, n); // 释放 allocate 分配的 n 个对象（必须是全部对象）内存，调用之前需要先对每个对象 destroy 。
a.construct(p, args); // p 指向 T 类型一块原始内存，args 传给 T 的构造参数。  
a.destroy(p); // p 是 T* 类型指针，对 p 指向的对象执行析构函数。  

allocator<string> alloc;  // 一个可以分配 string 的 allocator 对象
auto const p = alloc.allocate(n); // 分配 n 个未初始化的 string
```

