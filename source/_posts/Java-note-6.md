---
title: Java学习笔记-面向对象
date: 2021-08-24 20:42:34
tags: 
  - Java
  - 学习
categories:
  - 学习笔记
  - Java笔记
---

## 面向对象

Object-Oriented Programming, OOP

**以类的形式组织代码，以对象的形式封装数据**

三大特性：

- **封装**
- **继承**
- **多态**

## 方法回顾与拓展

- 方法的定义
  + 修饰符
  + 返回值类型
  + 方法名
  + 参数表
  + 异常抛出

- 方法的调用

  + 静态方法：有`static`修饰符，将与类同时加载

  + 非静态方法：没有`static`修饰符，在类实例化后才加载

    > 即需要new一个对象使其实例化后才可以调用
  
  + 形参与实参
  
  + 值传递与引用传递
  
  + `this`关键字

## 类与对象的创建

* **使用`new`创建对象**
* 使用`new`创建对象的时候，除了分配内存空间之外，还会给创建好的对象进行默认初始化以及对类中构造器的调用

### 构造器

- 类中的构造器也称为构造方法，是在进行创建对象的时候必须要调用的。并且有以下两个特点：
  1. 必须与类名字相同
  2. 必须没有返回类型，也不能写`void`
- 构造器的作用
  - 使用`new`关键字，本质上实在调用构造器
  - 构造器用来初始化值
  - 当定义了有参构造器，无参构造器必须显式定义

eg：

在文件`Student.java`中

```java
pubilc class Student{
    String name;
    pubilc Student(){
        this.name = "Tom";
    } //这是一个无参构造器
    pubilc Student(String name){
        this.name = name;
    } //这是一个有参构造器
}
```

那么在文件`Application.java`中

```java
pubilc class Application{
    public static void main(String[] args){
        Student Tom = new Student();
        System.out.println(Tom.name); // 这里将会打印默认初始值Tom
        Student Alice = new Student("Alice");
        System.out.println(Alice.name); // 这里将会打印传入的参数Alice
        // 回忆关于方法的重载相关的知识
    }
}
```

## 创建对象的内存分析

仍然以上文的代码为例，其在内存中的存在形式如下图

![objects](https://cdn.jsdelivr.net/gh/Kenguo05/blog-img@main/img/blog-emoji/objects.jpg)

## 封装

- **高内聚，低耦合**
  - 高内聚：类的内部数据操作细节自己完成，不允许外界干涉
  - 低耦合：仅暴露少量方法给外部使用
- 封装
  - 通常，应禁止直接访问一个对象中数据的实际表示，而应通过操作接口来访问，这称为信息隐藏
- 属性私有，通过get/set对属性进行操作

eg

```java
pubilc class Student{
    // 使用 private 关键字将数据封装，这些字段将无法从外部访问
    private String name;
    private int id;
    private char gender;
    // 通过一些 public 方法实现对相应字段的操作
    public String getName(){
        return this.name;
    }
    public void setName(String name){
        this.name = name;
    }
    // ...
}
```

封装的意义在于：

+ 确保程序的安全，可以规避不合法的数据，提高健壮性

```java
pubilc class Student{
    private String name;
    private int age;
    private int id;
    private char gender;
    public void setAge(int age){
        if(age>120||age<0){ //判断数据是否合法
            this.age = 3; //处理非法数据，保障系统安全
        }else{
            this.age = age; //合法数据
        }
    }
}
```

+ 隐藏代码的实现细节
+ 统一接口
+ 增加系统的可维护性

## 继承

+ 本质是对某一批类的抽象

+ 使用`extends`关键字，意为“扩展“，子类为父类的扩展

+ Java 中只有单继承，没有多继承

  > 一个子类只能有一个父类，一个父类可以有多个子类

比如现在定义一个 Person 类

```java
public class Person{
    public void say(){
        System.out.println("Hello");
    }
}
```

再定义一个 Student 类继承它

```java
public class Student extends Person{
    // 这个类中将继承父类中所有 public 的属性和方法
}
```

那么在主方法中可以

```java
public class Application{
    pubilc static void main(String[] args){
        Student student = new Student();
        student.say(); //这是合法的
    }
}
```

> 实际上，所有的类都可以通过继承关系追溯到一个 Object 类，因此即便一个类不定义任何方法都可以找到一些默认自带的方法比如 `hashCode`方法

## super 关键字

### super 关键字的用法

`super`关键字用于调用父类的数属性和方法

```java
super.name; // 调用父类的 name 属性
super.print(); //调用父类的 print 方法
super(); //调用父类的构造方法
```

> `super`关键字无法调用被`private`修饰的属性或方法

在子类的无参构造器中，默认调用父类无参构造器，且先于子类无参构造器执行

```java
public class Student extends Person{
    public Student{
        super(); // 该语句隐式存在，若显式写出，须写在子类构造器第一行
        // 可以通过 this 关键字调用本类的构造器，但不能与 super 同时显式存在
        System.out.println("子类无参构造执行");
    }
}
```

```java
public class Person{
    public Person{
        System.out.println("父类无参构造执行");
    }
}
```

当在`main`方法中`new`一个`Student`对象时，将会打印

```sh
父类无参构造执行
子类无参构造执行
```

> 当父类只有有参构造时，子类若使用无参构造将会报错，但可以在子类的无参构造中的`super`语句中传入参数。
>
> ```java
> // 父类
> public class Person{
>     public Person(String name){
>         System.out.println(name);
>     }
> }
> // 子类
> class Student extends Person{
>     public Student(){
>         super("name"); // 若不写此语句，程序将报错
>     }
> }
> ```

### super 关键字对比 this 关键字

+ 代表的对象不同
  - `this`：代表调用者本身这个对象
  - `super`：代表父类对象的引用
+ 使用前提
  - `this`：没有继承关系也可以使用
  - `super`：必须存在继承关系
+ 构造方法
  - `this()`：本类的构造
  - `super()`：父类的构造

## 方法重写

重写：必须要有继承关系，子类可以重写父类的方法

1. 方法名必须相同
2. 参数列表必须相同
3. 修饰符：范围可以扩大但不能缩小
4. 抛出异常：范围可以缩小但不能扩大

重写的意义在于：父类的功能未必满足子类的需求，或超出了子类的需求

```java
public class Application{
    public static void main(String[] args){
        // 若为静态方法，则方法的调用只和左边定义的数据类型有关
        // 若为非静态方法，则发生了方法的重写
        B b = new B();
        b.test(); // 打印 B=>test
        A a = new B(); //父类的引用指向了子类
        a.test(); //由于方法重写，打印 B=>test
    }
}
```

```java
public class A{
    public void test(){
        System.out.println("A=>test");
    }
}
```

```java
public class B extends A{
    public void test(){
        System.out.println("B=>test");
    }
}
```

## 多态

即同一种方法可以根据发送对象的不同而采用多种不同的行为方式

比如说，

```java
public class Application{
    public static void main(String args[]){
        Pet pet = new Pet();
        Pet dog = new Dog(); // 父类的引用指向子类
        Pet cat = new Cat();
        pet.shout(); //将会打印 shout
        dog.shout(); //将会打印 wof
        cat.shout(); //将会打印 nya
    }
}

class Pet{
    public void shout(){
        System.out.println("shout!");
    }
}

class Cat extends Pet{
    @Override          // 重写了 Pet 中的 shout 方法
    public void shout(){
        System.out.println("nya!");
    }
}

class Dog extends Pet{
    @Override
    public void shout(){
        System.out.println("wof!");
    }
}
```

多态存在的条件

- 存在继承关系

- 方法重写

  > 一些方法不能被重写
  >
  > - static 方法
  >
  > - final 常量
  >
  >   >被 final 修饰的变量初始化后便不能更改
  >   >
  >   >被 final 修饰的方法不能重写
  >   >
  >   >被 final 修饰的类不能被继承
  >
  > - private 方法

- 父类的引用指向子类

多态的意义：提高了程序的灵活性与可扩展性，比如上例中如果需要添加其他的子类，只需要将新增的类继承自`Pet`，再将`shout`方法重写就可以，不需要对原本有的代码进行改动

## instanceof 和类型转换

### instanceof

instanceof是Java中的二元运算符，左边是对象，右边是类；当对象是右边类或子类所创建对象时，返回true；否则，返回false

看以下代码

```java
public class Application{
    public static void main(String[] args){
        //考虑类的继承结构
        //Object > String
        //Object > Person > Teacher
        //Object > Person > Student
        Object object = new Student();
        System.out.println(object instanceof Student); // true
        System.out.println(object instanceof Person); // true
        System.out.println(object instanceof Object); // true
        System.out.println(object instanceof Teacher); //false
        System.out.println(object instanceof String); //false
        Person person = new Student();
        System.out.println(person instanceof Student); // true
        System.out.println(person instanceof Person); // true
        System.out.println(person instanceof Object); // true
        System.out.println(person instanceof Teacher); // false
        //System.out.println(person instanceof String); // 编译报错
        Student student = new Student();
        System.out.println(student instanceof Student); // true
        System.out.println(student instanceof Person); // true
        System.out.println(student instanceof Object); // true
        //System.out.println(student instanceof Teacher); // 编译报错
        //System.out.println(student instanceof String); // 编译报错
    }
}

class Person{
    
}

class Student extends Person{
    
}

class Teacher extends Person{
    
}
```

对于语句`X instanceof Y`，是否编译通过取决于`X`引用类型与`Y`类是否可比，若可比，则编译通过，否则编译报错；而语句的值取决于`X`所指向的实际类型是否是`Y`类或其子类，若是，则值为`true`，否则值为`false`

### 类型转换

与基础类型的转换类似，父类型处于高位，子类型处于低位，如果父类型想调用子类型的方法，可以使用强制转换

```java
public class Application{
    public static void main(String[] args){
        Person obj = new Student();
        // obj.run(); //会报错
        ((Student) obj).run();
        // 或者
        // Student student = (Student)obj;
        // student.run();
    }
}

class Person{
    // run方法是Student拥有的方法，父类中没有这个方法
}

class Student extends Person{
    public void run(){
        System.out.println("run");
    }
}
```

而子类转换为父类，可以自动转换

```java
public class Application{
    public static void main(String[] args){
        Student student = new Student();
        student.run(); //可以直接调用自己的方法
        Person person = student; //无需强制转换
    }
}

class Person{
    // run方法是Student拥有的方法，父类中没有这个方法
}

class Student extends Person{
    public void run(){
        System.out.println("run");
    }
}
```

## static 关键字

### 静态变量

当给变量加上`static`修饰词时，变量成为静态变量，静态变量与类同时加载，即使没有将类实例化成对象，也可以通过类来访问这个变量，因为此时静态变量已经存在于内存中了

```java
public class Student{
    private static int age;
    private int score;
    public static void main(String[] args){
        System.out.println(Student.age); // 可以直接访问
        // System.out.println(Student.score); //不能直接访问
    }
}
```

### 静态方法

当给方法加上`static`修饰词时，变量成为静态方法，静态方法与类同时加载，其他方法可以直接调用静态方法，而非静态方法需要先将类实例化成对象才能调用

```java
public class Student{
    private static int age;
    private int score;
    public static void go(){
        
    }
    public void run(){
        go(); // 其他方法可以直接调用静态方法
    }
    public static void main(String[] args){
        Student.go(); //可以直接调用
        // 如果在同一个类中，还可以
        go();
    }
}
```

### 静态代码块

在类中可以加入用`{}`包裹的代码块，这种代码块先于构造器执行

```java
public class Person{
    {
        // 匿名代码块
    }
    static {
        // 静态代码块
    }
        
}
```

其中静态代码块最先执行且执行一次，然后执行匿名代码块，最后执行构造器

可以利用这种特性对数据进行初始化

### 静态导入包

通过静态导入包可以在当前类中直接使用导入包中的方法或属性

```java
import static pkg1.pkg2.pkg3.classname.methodname;
import static pkg1.pkg2.pkg3.classname.fieldname;
```

## 抽象类

通过`abstract`修饰过的类称为抽象类

+ 抽象类不能`new`，只能通过子类实现它
+ 抽象类中可以写普通方法
+ 抽象方法只能存在于抽象类中
+ 抽象方法的实现只能通过子类重写

```java
public abstract class Action{
    public abstract void do(); // 这是一个抽象方法
}

class A{
    @Override
    public abstract void do(){
        // 方法体
    }
}
```

## 接口

**接口的本质是契约**

定义接口的关键字是`interface`

定义一个接口

```java
public interface UserService {
    void add(String name); //接口中的方法只有方法名，且默认为 public abstract
    void delete(String name);
    void update(String name);
    void query(String name);
}
```

接口需要有实现类，通过关键字`implements`继承

```java
public class UserServiceImpl implements UserService {
    public void add(String name){
        // 在实现类中重写接口中的方法
    }
    public void delete(String name){
        
    }
    public void update(String name){
        
    }
    public void query(String name){
        
    }
}
```

接口是多继承的，一个类可以实现多个接口

```java
public interface TimeService {
    void timer();
}
```

```java
// 继承了多个接口
public class UserServiceImpl implements UserService,TimeService {
    public void add(String name){
		
    }
    public void delete(String name){
        
    }
    public void update(String name){
        
    }
    public void query(String name){
        
    }
    public void timer(){
        
    }
}
```

接口中也可以定义属性

所有属性默认为`public static final`

接口的特点：

- 约束
- 定义一些方法，让不同的人实现
- 接口不能被实例化，接口中没有构造方法
- `implements`可以实现多个接口

## 内部类

内部类就是在一个类的内部再定义一个类

- 成员内部类
- 静态内部类
- 局部内部类
- 匿名内部类

### 成员内部类

```java
public class Outer{
    private int id = 10;
    public void out(){
        
    }
    public class Inner{
        public void in(){
            
        }
        public int getId(){
            return id;
        }
    }
}
```

可以通过外部类实例化内部类

```java
public class Application{
    public static void main(String[] args){
        Outer outer = new Outer();
        Outer.Inner inner = outer.new Inner();
        // 通过内部类获取外部类的私有属性/方法
        int id = inner.getId;
    }
}
```

### 静态内部类

用关键字`static`修饰的内部类，只能获取外部类的静态变量/方法

### 匿名内部类

没有名字的初始化类，不用将实例保存到变量中

```java
public class Test {
    public static void main(String[] args) {
        new Apple().eat(); //这是一个匿名内部类
        UserService userservice = new UserService(){ // 将返回一个UserService对象
          public void hello(){
              
          }  
        }; // 这也是一个匿名内部类，可以用来实现接口
    }
}
class Apple{
    public void eat(){
        
    }
}
interface UserService{
    void hello();
}
```



### 局部内部类

定义在方法中的类，类似于局部变量
