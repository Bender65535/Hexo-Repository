---
title: JVM的堆内存模型
date: 2019-09-09 22:30:12
tags: java , jvm
---

## JDK1.7的堆内存模型:

<img src="JVM的堆内存模型\1567920726148.png" alt="1568039480585" style="zoom:80%;" />

<!--more-->

+ Young 年轻区(代)

  Young区被划分为三部分**,Eden区**和两个**大小严格相同**的**Survivor区**,其中Survivor区间中,某一时刻只有其中一个是被使用的,另一个**留作垃圾收集时辅助对象用**,在Eden区间变满的时候,GC就会将存活的对象移动到空闲的Survivor区间中,根据jvm的策略,在经过几次垃圾收集后,任然存活于Survivor的对象将被移动到Tenured区间.

+ Tenured 年老区

  Tenured去主要保存生命周期长的对象,一般是一些老的对象,当一些对象在Young复制转移一定的次数以后,对象就会被转移到Tenured区,一般如果系统中用了**application级别的缓冲**,缓冲中的对象往往会被转移到这一区间,**老年区的对象也有可能会被回收**

+ Perm 永久区

  Perm代主要保存class.method.filed对象,这部份的空间一般不会溢出,除非一次性加载了很多的类,不过在涉及**热部署**的应用服务器的时候,有时候会遇到**java.lang.OutOfMemoryError : PermGen space**这样的错误,造成这个错误的很大原因就有可能是每次都重新部署,但是重新部署后,类的class没有被卸载掉,这样就造成了大量的class对象保存在了perm中,这种情况下,一般**重新启动应用服务器**可以解决

+ Virtual区

  最大内存和初始内存的差值,就是Virtual区

## JDK1.8的堆内存模型

<img src="JVM的堆内存模型\1568043123417.png" alt="1568043123417" style="zoom: 67%;" />

由上图可以看出,jdk1.8的内存模型是由2部分组成,年轻代+老年代

年轻代 : Eden + 2*Survivor

年老代 : OldGen

在jdk1.8中变化最大的Perm区,用Metaspace(元数据空间)进行了替换

需要特别说明的是 : Metaspace所占用的内存空间不是在虚拟机内部,而是在本地内存空间汇总,这也是1.7的永久代最大的区别所在.

<img src="JVM的堆内存模型\1568043436785.png" alt="1568043436785" style="zoom: 50%;" />

CCS: 压缩指针(只有被开启才使用)

CodeCache: 存放class

### 为什么要废弃1.7中的永久区?

现实使用中,忧郁永久代内存经常不够用或发生泄露,爆出异常java.lang.OutOfMemoryError:PermGen.基于此,将永久才废弃,而该用元空间,改为了使用本地内存空间

