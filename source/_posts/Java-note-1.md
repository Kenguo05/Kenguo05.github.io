---
title: Java学习笔记-初识Java
update: 2021-08-24 01:07:08
tags: 
  - Java
  - 学习
categories:
  - 学习笔记
  - Java笔记
---

## Java 特性与优势

+ 简单性
+ 面向对象
+ 可移植性（**跨平台**）：Write once, run anywhere.
+ 高性能
+ 分布式
+ 动态性：反射机制
+ 多线程
+ 安全性
+ 健壮性

## Java 三大版本

+ `JavaSE`：标准版（桌面程序，控制台开发......）
+ `JavaME`：嵌入式开发（手机，小家电......）
+ `JavaEE`：企业级开发（web端，服务器开发......）

## JDK、JRE、JVM 初识

+ `JDK`：Java Development Kit
+ `JRE`：Java Runtime Environment
+ `JVM`：Java Virtual Machine

> `JRE` 扩充了一些开发工具后成为` JDK`
>
> **JVM** 是 Java 跨平台的核心

## Hello World 

`HelloWord` 的 `Java`实现

```java
public class HelloWorld{
	public static void main(String[] args){
		System.out.println("Hello, World!");
	}
}
```

在命令行窗口中运行命令

```sh
javac HelloWorld.java
# 在对应目录下运行cmd，将会生成一个.class文件
java HelloWorld
# 这里不用加后缀
```

运行成功后将打印

```sh
Hello, World!
```

