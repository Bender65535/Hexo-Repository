---
title: JVM常量池和内存原理
date: 2019-08-23 10:35:33
tags:
---

JVM内存模型:

![1566528627305](JVM常量池和内存原理\1566528627305.png)

<!--more-->

### JVM常量池中的表:

| 常量表类型                 | 标志值(占1byte) | 描述                           |
| -------------------------- | --------------- | ------------------------------ |
| CONSTANT_Utf8              | 1               | UTF-8的Unicode字符串           |
| CONSTANT_Integer           | 3               | int类型的字面值(-128~127)      |
| CONSTANT_Float             | 4               | float类型的字面值              |
| CONSTANT_Long              | 5               | long类型的字面值               |
| CONSTANT_Double            | 6               | double类型的字面值             |
| CONSTANT_Class             | 7               | 对一个类或接口的符号引用       |
| CONSTANT_String            | 8               | String类型字面值的引用         |
| CONSTANT_Fieldref          | 9               | 对一个字段的符号引用           |
| CONSTANT_Methodref         | 10              | 对一个类中方法的符号引用       |
| CONSTANT_InterfaceMetodref | 11              | 对一个接口中方法的符号引用     |
| CONSTANT_NameAndType       | 12              | 对一个字段或方法的部分符号引用 |

``` java
public class Demo{
    public static void main(String[] args){
        String string1="haha";
        String string2="haha";
        System.out.println(string1==string2);
    }
}
```

```
true
```

### 字符串比较过程:

+ 执行javac的时候,将string1编译为Unicode编码
+ class加载进入方法区
+ 将string1的值存入CONSTANT_Utf8表中
+ 将string1的tag存入CONSTANT_String表,表中的索引值指向CONSTANT_Utf8
+ string1的压入线程栈,其引用指向CONSTANT_String表中所对应的tag再指向CONSTANT_Utf8的值
+ string2也指向相同的tag再指向CONSTANT_Utf8的值



```java
public class Demo{
    public static void main(String[] args){
        String string1="haha";
        String string2=new String("haha");
        System.out.println(string1==string2);
    }
}
```

```
false
```

### 原因:

string2指向的是堆内存的地址,而堆内存中的指针才是指向常量池(注意:如果"haha"这个字面量在前面已经出现过了,那么只创建了一个对象,如果没有出现,就创建了两个对象)



```java
public class Demo{
    public static void main(String[] args){
        String string1="haha";
        String string2=new String("haha");
        System.out.println(string1==string2.intern());
    }
}
```

```
true
```

### 原因:

intern()方法返回的堆内存中的指针,也就是常量池字面值的地址.如果常量池里面没有这个字面量,那么先把这个字面值放到常量表里面之后返回常量表的地址



```java
public class Demo{
    public static void main(String[] args){
        String string1="haha";
        String string2="ha";
        String string3=string2+"ha"
        System.out.println(string1==string3);
    }
}
```

```
false
```

### 原因:

string3在运行时通过StringBuilder拼接后将拼接号的字符串放入常量池由于常量池已经有了"haha"所以不用再放,并且StringBuilder堆内存中的指针指向常量池"haha".string3为StringBuilder的地址,string1为常量池中CONSTANT_Utf8表的地址,故二者不相等

**注意**:如果string3=="ha"+"ha",则返回true.这是因为在编译时字符串已经拼接完毕string3=string2+"ha"之所以不会拼接是因为string2只有在执行时才变为真实地址









