# 第三章：字符串、向量和数组

## cctype(ctype.h) 头文件  

```c++
//#include <cctype>
isalpha(c);     // 字母
isalnum(c);     // 字母 + 数字
isdigit(c);     // 数字
islower(c);     // 小写字母
ispunct(c);     // 标点符号
isspace(c);     // 空白符（空格、制表、回车等）
isupper(c);     // 大写字母
isxdigit(c);    // 十六进制数字
tolower(c);     // 如果是大写，则转小写，否则原样输出
toupper(c);     // 如果是小写，则转大写，否则原样输出
```

## 范围 for 语句（C++ 11)  

C++ 11 新增了范围 for (range for) 语句的支持，用以遍历给定序列的每个元素，并对序列中的每个值执行操作。  
```c++
for (declaration : expression)
    statement

string str("songe thing");

for(auto c : str)   // 迭代时拷贝
{
    cout << c << endl;
}
for(auto &c : str)  // 迭代时引用，用于改变 str 元素
{
    c = toupper(c);
}
cout << str << endl;
```

## vecotr 初始化

需要注意的情况：  

* 列表初始化，只能把初始值都放在花括号里进行初始化。如果花括号里面提供的值不满足列表初始化，则会尝试用这样的值来实现构造初始化。  
    ```c++
    vector<string> v1{"a", "an", "the"};
    vector<string> v2(10);
    vector<string> v3{10};  // 显然不满足列表初始化，与上面等效
    ```

* 值初始化，对象需要支持**默认初始化**或提供**初始元素值**  
    ```c++
    vector<string> v1(10);  // string 类默认初始化置空
    ```

## 迭代器  

所有标准库容器都可以使用迭代器（`iterator`），这些类型都拥有名为 `begin` 和 `end` 的成员函数。其中 `begin` 负责返回指向第一个元素的迭代器，`end` 负责返回指向容器“为元素的下一个位置”。如果容器为空，则 `begin` 和 `end` 返回的是同一个迭代器。

```c++
*iter           返回迭代器 iter 所指元素的引用
iter->men       解引用 iter 并获取该元素的名为 mem 的成员
++iter          令 iter 指示容器中的下一个元素
--iter          令 iter 指示容器中的上一个元素
iter1 == iter2
iter1 != iter2  如果两个迭代器指示的是同一个成员或者是同一个容器的尾后成员，则相等。 
```

迭代器包含 `iterator` 和 `const_iterator` 两种，前一个可读可修改，后一个可读不可修改。默认对象不是 `const` 时，`begin` 和 `end` 返回 `iterator` ，否则返回 `const_iterator`。C++ 11 扩展了 `cbegin` 和 `cend` ，无论对象是否是常量，都返回 `const_iterator`。

## 数组  

```c++
int iap[] = {0, 2, 4, 6, 8};
int *p = iap + 2;
int k = p[-2];      // k = 0;

/* -------------------------------- */
// 迭代数组元素
for(auto it = begin(iap); it != end(iap); ++it)
{
    //
}

int ia[10][20] = {0};
for (auto &row : ia)        // 引用类型，row 是大小为 4 的数组
{
    for (auto col : row)    // col 是指针
    {
        cout << col << endl;
    }
}

```






