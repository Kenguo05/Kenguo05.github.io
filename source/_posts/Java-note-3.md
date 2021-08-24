---
title: Java学习笔记-Java流程控制
date: 2021-08-24 12:59:00
tags: 
  - Java
  - 学习
categories:
  - 学习笔记
  - Java笔记
---

## Scanner 对象

Java5 在 `java.util.Scanner` 中提供了 `Scanner` 类，可以获取用户输入，使用之前要导入这个包

```java
import java.util.Scanner;
```

基本语法

```java
//创建一个名叫 s 的 Scanner 对象用于获取输入
Scanner s = new Scanner(System.in);
// 将接收到的输入赋给变量str
String str = s.next();
```

通过`Scanner`类的`next()`与`nextLine()`方法获取输入的字符串，读取前一般需要用`hasNext()`与`hasNextLine()`判断是否还有输入数据

+ `next()`
  1. 一定要读取到有效字符才可以结束输入
  2. 对输入有效字符之前遇到空白，`next()`方法会将其自动去掉
  3. 只有输入有效字符后才将其后面输入的空白作为分隔符或结束符
  4. **`next()`不能得到带有空格的字符串**

+ `nextLine()`
  1. 以`Enter`为结束符，也就是说`nextLine()`返回的是输入回车之前的字符
  2. 可以得到带有空格的字符串

> 当需要输入整数和小数时，可以使用`nextInt()`和`nextFloat()`等方法，其他数据类型依次类推。

## 顺序结构

最基本的结构，最简单的结构，语句从上到下依次执行

**是所有算法都会用到的结构**

## 选择结构

### if 单选择结构

语法

```java
if (布尔表达式){
	//执行语句
}
```

### if 双选择结构

语法

```java
if(布尔表达式){
    //执行语句
}else{
    //执行语句
}
```

### if 多选择结构

语法

```java
if (布尔表达式1){
    //执行语句
}else if (布尔表达式2){
    //执行语句
}else if (布尔表达式3){
    //执行语句
}else{
    //执行语句
}
```

### if 嵌套结构

语法

```java
if (布尔表达式1){
    // 执行语句
    if (布尔表达式2){
        // 执行语句
    }
}
```

### switch 选择结构

语法

```java
switch(expression){
    case value:
        //语句
        break; //可选
    case value:
        //语句
        break; //可选
    default: //可选
        // 语句
}
```

**自 JavaSE 7 开始支持字符串比较**

## 循环结构

### while 循环

结构

```java
while(布尔表达式) {
    // 执行语句
}
```

当布尔表达式为`true`是进入循环执行语句

**大多数情况需要循环停止下来，需要一个让表达式失效的方式来结束循环**

### do...while 循环

结构

```java
do {
    //执行语句
} while(布尔表达式)
```

de...while 循环至少执行一次

### for 循环

结构

```java
for(初始化; 布尔表达式; 更新) {
    // 执行语句
}
```

### 增强 for 循环

主要用于遍历数组，JavaSE 5 引入

结构

```java
for(声明语句 : 表达式){
    //执行语句
}
```

>声明语句：声明新的局部变量，该变量的类型必须和数组元素类型匹配。其作用域限定在循环语句块，其值与此时的数组元素的值相等
>
>表达式：表达式是要访问的数组名，或者是返回值为数组的方法

## break continue

### break

用于**强制**退出循环，或在`switch`语句中防止`case`穿透

### continue

用于终止当次循环，但仍会执行下一次循环

> Java 没有`goto`，但可以通过`label`标签和`continue`&`break`实现

