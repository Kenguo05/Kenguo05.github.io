---
title: Java学习笔记-Java基础
date: 2021-08-24 02:23:48
tags: 
  - Java
  - 学习
categories:
  - 学习笔记
  - Java笔记
description: 学习 Java 基本的语法知识，比如注释、关键字、运算符blablablabla......有其他语言基础学起来果然轻松不少
cover: https://cdn.jsdelivr.net/gh/Kenguo05/blog-img@main/img/blog-cover/Java.jpg
---

## 注释

+ 单行注释

  ```java
  // 单行注释
  ```

+ 多行注释

  ```java
  /*
  多行注释
  多行注释
  */
  ```

+ 文档注释（JavaDoc）

  ```java
  /**
   *@Description
   *@Author
   */
  ```

## 标识符与关键字

### 关键字

Java 语法中自带的单词，如 public，int，package等

### 标识符

类名、变量名以及方法名都被称为标识符，标识符不能以关键字命名

+ 标识符以大小写字母、美元符号（$）、下划线（_）开头
+ 首字符后可以使用大小写字母、美元符号、下划线、数字
+ 标识符大小写敏感
+ 不建议使用中文与拼音命名

## 数据类型

### 基本类型

+ 数值类型

  + 整数类型

    + byte：占1个字节
    + short：占2个字节
    + int：占4个字节
    + long：占8个字节

    > 二进制 `0b`，八进制`0`，十六进制`0x`

  + 浮点类型

    + float：占4个字节
    + double：占8个字节

    > 浮点数有误差，最好不要使用浮点数进行比较操作

  + 字符类型

    + char：占2个字节

    > unicode编码 占2字节
    >
    > 转义字符：\t \n 等

+ boolean类型：占一位，只有 `true`和`false`两种取值

### 引用类型

+ 类
+ 接口
+ 数组

## 类型转换

byte, short, char -> int -> long -> float -> double

> 一般从小到大转换

### 强制类型转换

eg：

```java
int i = 10;
byte b = (byte)i;
```

高转低用强制转换，注意内存溢出与精度问题

### 自动类型转换

eg：

```java
char c = 'a';
int i = c+1;
```

低到高用自动转换

> 在计算比较大的数字时注意溢出的问题

## 变量、常量、作用域

### 变量

最基本的存储单元，包括变量名、变量类型和作用域

+ 每个变量都有类型，可以是基本类型，也可以是引用类型
+ 变量名为合法的标识符

### 常量

初始化后不再改变，一般用`final`+`类型`+`常量名`定义

```java
final double PI = 3.14
```

常量名一般用大写字符

### 作用域

+ 类变量：作用域更大
+ 实例变量：从属于对象，有默认值
+ 局部变量：作用于方法内，需要初始化

> **变量命名规范**
>
> + 见名知意
> + 类变量：首字母小写和驼峰原则：monthSalary
> + 局部变量：首字母小写和驼峰原则
> + 常量：大写字母和下划线
> + 类名：首字母大写和驼峰原则：HelloWorld
> + 方法名：首字母小写和驼峰原则：setName

## 运算符

+ 算术运算符：+ , - , * , / , % , ++ , --

+ 赋值运算符：=

+ 关系运算符：> , < , >= , <= , == , != , instaceof

  > instanceof是Java中的二元运算符，左边是对象，右边是类；当对象是右边类或子类所创建对象时，返回true；否则，返回false

+ 逻辑运算符：&&，||， !

+ 位运算符：&，|，^，~，>>，<<，>>>

+ 条件运算符：? : 

+ 扩展赋值运算符：+=，-=，*=，/=

## 包机制

用于更好的组织类

语法

```java
package pkg1[.pkg2[.pkg3...]];
// 三个包存在包含关系
```

一般用域名倒置作为包名

当使用某个包的成员时，需要导入该包

```java
import package1[.package2...].(classname|*);
// *表示导入包中所有的类
```

## JavaDoc

+ javadoc命令用于生成自己的API文档
+ 参数
  + @author 作者
  + @version 版本
  + @since jdk版本
  + @param 参数
  + @return 返回值情况
  + @throws 异常抛出情况