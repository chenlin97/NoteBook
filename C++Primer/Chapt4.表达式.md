# 第四章：表达式

- [第四章：表达式](#第四章表达式)
  - [隐式类型转换规则](#隐式类型转换规则)
  - [显式转换 - C++ 11](#显式转换---c-11)

## 隐式类型转换规则  

* 算术转换：运算符的对象将转换成最宽的类型。

* 无符号的运算对象：如果无符号类型不小于带符号类型，那么带符号类型的运算对象转换成无符号的。  

## 显式转换 - C++ 11  

C++ 的强制类型转换具有如下形式：  

```c++
cast-name<type>(expression)
```

cast-name 是 `static_cast`、`dynamic_cast`、`const_cast` 和 `reinterpret_cast` 中的一种。  

* static_cast：任何具有明确意义的类型转换，只要不包含底层 const，都可以使用 static_cast。

* const_cast：只能改变算对象的底层 const。

* reinterpret_cast：为运算对象的位模式提供较低层次的重新解释。  