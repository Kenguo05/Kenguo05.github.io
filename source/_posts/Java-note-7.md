---
title: Java学习笔记-异常
date: 2021-08-25 19:31:34
tags: 
  - Java
  - 学习
categories:
  - 学习笔记
  - Java笔记
description: 学会预测、发现、捕获、处理异常，保证程序运行的安全性
cover: https://cdn.jsdelivr.net/gh/Kenguo05/blog-img@main/img/blog-cover/Java.jpg
---

## Exception 和 Error

- 检查性异常：比如用户错误或问题引起的异常，这是程序员无法预见的。例如要打开一个不存在的文件时，一个异常就发生了，这些异常在编译时不能被简单的忽略
- 运行时异常：运行时异常是可能被程序员避免的异常。与检查性异常相反，运行时异常可以在编译时被忽略
- 错误：错误不是异常，而是脱离程序员控制的问题。错误在编译中通常被忽略。例如，当栈溢出时，一个错误就发生了，它们是编译也检查不到的

### Error

- Error 类对象由 Java 虚拟机生成并抛出，大多数错误与代码编写这所执行的操作无关
- Java 虚拟机运行错误（VirtualMachineError），当 JVM 不再有继续执行操作所需的内存资源时，将出现 **OutOfMenmoryError**。这些异常发生时，JVM 一般会选择终止线程
- 在 JVM 试图执行应用时，如类定义错误（NoClassDefFoundError）、链接错误（LinkageError）。这些错误时不可查的，因为它们在应用程序的控制和处理能力之外，而且绝大多数时程序运行时不允许出现的状况

### Exception

- 在 Exception 分支中有一个重要的子类 RuntimeException（运行时异常）
  - ArrayIndexOutOfBoundsException
  - NullPointerException
  - ArithmeticException
  - MissingResourceExecption
  - ClassNotFoundException
- 这些异常是不检查异常，程序中可以选择捕获处理，也可以不处理
- 这些异常一般是由程序逻辑错误引起的，程序应该从逻辑角度尽可能避免这类异常发生
- Error 和 Exception 的区别
  - Error 通常是灾难性的致命错误，是程序无法控制和处理的，当出现时，JVM 一般会选择终止线程
  - Exception 通常情况下是可以被程序处理的，并且在程序中应该尽可能的处理

## 异常处理机制

异常处理关键字：`try`, `catch`, `finally`, `throw`, `throws`

### 捕获异常

```java
try{
    // try 监控区域
}catch(想要捕获的异常){
    // 捕获异常后的执行语句
}finally{
    // 无论是否捕获到异常都会执行
}
```

如果要捕获多个异常，要按照异常包含关系从小到大捕获，否则会报错

比如

```java
try{
    // try 监控区域
}catch(Exception e){
    // 捕获异常后的执行语句
}catch(Throwable t){
    // 这段语句不能与上一段捕获语句交换位置
}finally{
    // 无论是否捕获到异常都会执行
}
```

### 抛出异常

假设在一个方法中无法处理异常，可以将其抛出

```java
public class Test {
    public static void main(String[] args) {
        try{
            new Test().test(1,0); //方法将异常抛出于此
        }catch(ArithmeticException e){ //捕获到异常
            //处理异常
        }
    }
    
    public void test(int a, int b) throws ArithmeticException{ // 可能出现的异常
        if(b==0){ // 判断可能发生异常
            throw new ArithmeticException(); //主动将异常抛出
        }
    }
}
```

## 自定义异常

以下代码自定义了一个异常

```java
public class MyException extends Exception{ // 自定义异常需要继承自 Exception 或更高的父类
    private int detail;

    public MyException(int detail) { //构造方法，对 detail 赋值
        this.detail = detail;
    }

    @Override
    public String toString() { //来自父类Object的方法，将类的信息转换为字符串
        return "MyException{" +  //这里相当于把这个异常信息转换成了字符串便于打印
                "detail=" + detail +
                '}';
    }
}

```

下面是测试代码

```java
public class Test {
    public static void main(String[] args) {
        try {
            new Test().test(11); //异常抛出于此
        } catch (MyException e) { //捕获异常信息 e，也就是 toString 的返回值
            System.out.println(e); //打印异常信息
        }
    }

    static void test(int a) throws MyException {
        System.out.println("传入的参数为：" + a);
        if (a > 10){ //这里将判断是否异常
            throw new MyException(a); //异常则 new 一个异常类，将其初始化为a，抛出到上层
        }
        System.out.println("OK");
    }
}
```

## 实际应用

- 处理运行异常时，采用逻辑去合理规避同时辅助`try`+`catch`处理
- 多重`catch`块后面，可以加一个`catch(Exception e)`来处理可能会被遗漏的异常
- 对于不确定的代码，也可以加上`try`+`catch`，处理潜在的异常
- 尽量去处理异常，切记只是简单的调用`printStackTrace()`去打印输出
- 具体如何处理异常，需要根据不同的业务需求和异常类型去确定
- 尽量添加`finally`语句块去释放占用资源