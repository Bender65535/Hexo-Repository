---
title: JVM常见参数及命令
date: 2019-09-09 20:26:44
tags: java , jvm
---

# JVM的运行参数

## 三种参数类型

+ 标准参数
  + -help
  + -version
  + -server
  + -client
  + ...
+ -X参数(非标准参数)
  + -Xint
  + -Xcomp
+ **-XX参数**(使用率极高)
  + -XX:newSize
  + -XX:+UseSerialGC

<!--more-->

### 标准参数:

#### -server与-client参数:

+ 它们的区别是Server VM 的初始值空间会大一些,默认使用的是并行垃圾回收器,启动慢运行快
+ Client VM相对来讲会保守一些,初始堆空间会小一些,使用串行的垃圾回收器,他的目标是为了让JVM的启动速度更快,但运行速度会比Server模式慢些
+ JVM在启动的时候会根据阴间和操作系统自动选择使用Server还是Client类型的JVM
+ 32位操作系统
  + 如果是Windows系统,不论硬件配置如何,都默认使用Client类型的JVM
  + 如果是其他操作系统上,机器配置有2GB以上的内存同时有2个以上CPU的话默认使用server模式,否则使用client模式
+ 64位操作系统
  + 只有server类型,不支持client类型

### -X参数:

#### -Xint,-Xcomp,-Xmixed

+ 在解释模式(interpreted mode)下,-Xint标记会强制JVM执行所有的字节码,当然这会降低运行速度,通常低10被或更多
+ **-Xcomp**参数与-Xint正好相反,JVM在第一次使用时会把所有的字节码编译成本地代码,从而带来最大程度的优化
  + 然而,很多应用在使用-Xcomp也会有一些性能损失,当然这比使用-Xint损失的少,原因是-Xcomp没有让JVM启用IT编译器的全部功能.JIT编译器可以对是否需要编译做判断,如果所有代码都进行编译的话,对于一些只执行一次的代码就没有意义了
+ -Xmixed是混合模式,将解释模式与编译模式进行混合使用,有jvm自己决定,这是jvm默认的模式,也是推荐使用的模式

### -XX参数:

-XX也是非标准参数,**主要用于jvm的调优**和debug操作

-XX参数的使用有2中方式,一种是boolean类型,一种是非boolean类型:

+ boolean类型:
  + 格式:**-XX:[+-]<name>**表示启用或禁用<name>属性
  + 如:**-XX:+DisableExplicitGC**表示禁用手动调用gc操作,也就是说调用System.gc()无效(-表示不启用这个参数)
+ 非boolean类型
  + 格式:-XX<name>=<value>表示<name>属性的值为<value>
  + 如:**-XX:NewRatio=1** 表示新生代和老年带的比值



### -Xms与-Xmx参数 ( 属于-XX参数 )

-Xms与-Xmx分别是设置jvm的堆内存的初始大小和最大大小

-Xmx2048m : 等价于**-XX:MaxHeapSize**,设置JVM最大堆内存为2048M

-Xms512m : 等价于**-XX:InitialHeapSize**,设置JVM初始堆内存为512M

适当的调整jvm内存大小,可以充分利用服务器资源,让程序跑的更快



### 查看JVM的运行参数

有些时候我们需要查看jvm的运行参数,这个需求可能会存在2中情况:

第一,运行java命令打印出运行参数;

第二,查看正在运行的java进程参数;

####  运行java命令时打印参数

运行java命令时打印参数,需要添加**-XX:+PrintFlagsFinal**参数即可 ( 参数中"="表示默认参数,":="表示值已经被修改,启用该参数 )

#### 查看正在运行的JVM参数

如果想要查看正在运行的JVM就需要借助**jinfo**命令查看.

首先,启动一个tomcat用于测试,来观察下运行的jvm参数.

例如:jinfo -flags 5212   (注:5212为线程id,可用jps查看)



### 通过jstat命令进行查看堆内存使用情况

**jstat**命令可以查看堆内存各部分的使用量,以及加载类的数量,命令格式如下:

jstat [ -命令选项 ] [ vmid ] [ 间隔时间/毫秒 ] [ 查询次数 ]

例如 : jstat -class 6219  (6219使用jps查看)

说明:

- Compiled:编译数量
- Failed : 失败数量
- Invalid : 不可用数量
- Time : 时间
- FailedType : 失败类型
- FailedMethod : 失败的方法

### 垃圾回收统计

jstat -gc 6219

说明:

- SOC:第一个 Survivor区的大小(KB)
- S1C:第二个 Survivor区的大小(KB)
- S0U:第一个 Survivor区的使用大小(KB)
- S1U:第二个 Survivor区的使用大小(KB)
- EC:Eden区的大小(KB)
- EU:Eden区的使用大小(KB)
- OC:old区大小
- OU:old区大小(KB)
- MC:方法区大小(KB)
- MU:方法区使用大小(KB)
- CCSC:压缩类空间大小(KB)
- CCSU:压缩类空间使用大小(KB)
- YGC:年轻代垃圾回收次数
- YGCT:年轻代垃圾回收消耗时间
- FGC:老年代垃圾回收次数
- FGCT:老年代垃圾回收消耗时间
- GCT:垃圾回收消耗时间

### jmap分析内存溢出

堆内配置信息和使用情况 : jmap -heap 6219

查看内存中对象数量及大小 : 

+ 查看所有对象 : jmap -histo <pid> | more
+ 查看活跃对象 : jmap -histo : live <pid> | more

### 将内存使用情况dump到文件中

有些时候我们需要将jvm当前内存中的情况dump到文件中,然后对它进行分析,jmap也是支持dump到文件中的

用法: jmap -dump:format=b,file=dumpFileName <pid>

示例:jmap -dump:format=b,file=/tmp/dump.dat 6219

### 通过jhat队dump文件进行分析

在上衣小节中,我们将jvm的内存dump到文件中,这个文件是一个二进制的文件,不方便查看,这时我们可以借助于jhat工具进行查看

用法: jhat -port <port> <file>

示例: jhat -port 9999 /tmp/dump.dat

除了jhat,还能下载MAT工具进行分析

### jstack的使用

有些时候我们需要查看下jvm中的线程执行情况,比如,发现服务器的CPU的负载突然增高了,出现了死锁,死循环等,我们该如何分析呢?

由于程序是正常运行的,没有任何的输出,从日志方面也看不出什么问题,所以就需要看下jvm的内部线程的执行情况,然后在进行分析查找出原因

这个时候就需要借助jstack命令了,jstack的作用是将正在运行的jvm的**线程情况**进行快照,并且打印出来

用法:jstack <pid>

