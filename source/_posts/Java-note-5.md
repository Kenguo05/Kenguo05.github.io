---
title: Java学习笔记-数组
date: 2021-08-24 18:09:34
tags: 
  - Java
  - 学习
categories:
  - 学习笔记
  - Java笔记
description: 没啥好说的，Java 中数组与 C 语言中大差不差，但 Java 中提供了 for - each 循环遍历数组，而且还有强大的 Arrays 类提供数组的各种方法
cover: https://cdn.jsdelivr.net/gh/Kenguo05/blog-img@main/img/blog-cover/Java.jpg
---

## 数组的声明创建

+ 声明

  ```java
  dataType[] arrayRefVar; //首选
  dataType arrayRefVar[]; //也行
  ```

+ 创建

  ```java
  arrayRefVar = new dataType[arraySize];
  ```

+ 也可以

  ```java
  dataType[] arrayRefVar = new dataType[arraySize];
  ```

> 数组下标从0开始

+ 获取数组长度`arrays.length`

  > arrays 为数组名

## 数组初始化

### 静态初始化

```java
dataType[] arrayRefVar = {data1, data2,data3...};
```

### 动态初始化

```java
dataType[] arrayRefVar = new dataType[arraySize];
arrayRefVar[0] = data1;
arrayRefVar[1] = data2;
```

### 默认初始化

+ 数组是引用类型，其元素相当于类的实例变量，因此数组一经分配，其元素也被按照实例变量同样的方式被隐式初始化

## 数组使用

+ for-each 循环

  ```java
  for (int array : arrays){
      System.out.println(array);
  }
  ```

+ 数组作为参数

  ```java
  pubilc static void printArray(int[] arrays){
      for (int array : arrays){
      	System.out.println(array);
  	}
  }
  ```

+ 数组作为返回值

  ```java
  pubilc static int[] reverse(int[] arrays){
      int[] result = new int[arrays.length];
      for (int i = 0, j = result.length - 1; i < arrays.length; i++, j--){
          result[j] = arrays[i];
      }
      return result;
  }
  ```

## 多维数组

可以看作数组的数组

以二维数组为例

```java
int[][] arrays = {{1,2},{2,3},{3,4},{4,5}};
```

获取数组长度只获取当前维度的长度

```java
System.out.println(arrays.length);
// 打印结果为4
System.out.println(arrays[0].length);
// 打印结果为2
```

结构

![arrays](https://cdn.jsdelivr.net/gh/Kenguo05/blog-img@main/img/blog-emoji/arrays.jpg)

>  三维数组或更高维的类似

## Arrays 类

[Java SE 8 官方API文档](https://docs.oracle.com/javase/8/docs/api/)

