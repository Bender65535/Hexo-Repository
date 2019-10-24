---
title: Java的字节码与类创建过程
date: 2019-08-21 17:02:06
tags:
---

## Hello World的执行过程

```java
public class Hello{
    public static void main(String[] args){
        System.out.println("hello ");
    }
}
```

<!--more-->

- javac xxx.java
  - jdk将该文件输出为xxx.class文件,存入磁盘
- java xxx.class
  - 在内存中划出一块空间创建jvm
  - 校验磁盘中的xxx.class是否满足jvm规范
  - 类加载器将class文件加载到jvm中
  - 执行main方法,将main压入到线程栈

![JMM结构图](Java的字节码与类创建过程\1566373036510.png)

## 在执行new关键字时发生了什么

```java
public class Hello{
    public static void main(String[] args){
        Hello hello=new Hello();
    }
}
```

### 执行步骤:

- 当执行碰到new关键字时,那么main主线程便在自己的线程栈中声明了一个对象Hello hello;
- 在JVM的堆内存空间中申请一片内存地址,然后将Hello相关信息如:实例变量,实例方法等从方法区汇中加载到堆内存中.**执行顺序**:
  - 加载实例信息进入开辟的内存中
  - 执行构造方法就是<init>方法(clinit为类构造方法)
- 对象的引用指向堆内存中开辟的对象

## 对堆内存中开辟对象的结构进行讲解:

- 对象由对象的**头部信息**和实例信息组成
  - **头部信息**:
    - 填充值(使对象大小为2的n次方)
    - 持有指向方法区的指针(执行对象的静态方法要去方法区的方法表中查)
    - 描述信息
      - 持有当前对象的线程的id
      - 持有对象的锁线程的个数
      - 在gc中存活的生命周期数
      - 偏向锁的标志(当线程已经对此对象上锁后,执行完毕,如果下一次访问该对象的线程也是上一次的线程,那么不对此线程重新上锁)



### 类加载器的执行步骤:

+ 加载 : 将class文件加入到jvm
+ 验证 : 
  + 验证类信息
  + 验证元数据信息
  + 验证魔数
  + 验证当前虚拟机版本和class文件一不一样
  + 验证字节码有没违规操作
+ 准备 : 把**类变量**(static修饰的变量)初始化为**初始值**(jvm默认的值),final变量直接初始化为变量值
  - byte 0
  - short 0
  - int 0
  - long 0L
  - char "\u0000" (就是' ')
  - boolean false
  - float 0.0f
  - double 0.0d
  - 引用类型 null
+ 解析 : 把符号引用转为直接引用
+ 初始化 : 把我们定义的static变量或者static静态代码块按顺序组织成<clinit>构造器(也称作类构造器)来初始化变量(将你设置的值初始化给变量)
+ 使用 : 在堆内存中创建对象的时候执行顺序
  + 加载实例信息进入开辟的内存中
  + 执行构造方法就是<init>方法

### static方法无法访问非static变量的原因 : 

+ static修饰的方法是放入方法区中的,实例的变量是在堆内存,调用static方法时,从方法区中把数据调到线程栈中,没有产生实例,因此无法访问实例的变量(即非static修饰的变量)

