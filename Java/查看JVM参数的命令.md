控GC的工具分为2种：命令行工具和图形工具，图形工具可以进入jdk的bin目录下打开Jconcole.exe
下面先说命令行工具，在已经设置好java环境变量的情况下，可以在任意地方打开命令行窗口，使用一下命令：

## 1、jps

查询正在运行的JVM进程，常用的参数为：
-q:只输出LVMID，省略主类的名称
-m:输出虚拟机进程启动时传给主类main()函数的参数
-l:输出主类的全类名，如果进程执行的是Jar包，输出Jar路径
-v:输出虚拟机进程启动时JVM参数
命令格式:jps [option] [hostid]
一个简单的例子：

```
C:\Users\小健酱>jps
7536 Bootstrap
8144 Jps
3976
7656 JConsole
```

在上图中，有一个vid为7536的tomcat进程在提供web服务。图形化界面JConsole启动后，将自动搜索本机运行的jvm进程，不需要jps命令来查询指定

## 2、jstat（JVM统计监测工具）

jstat是一个观察java程序运行时程序的工具，可以实时显示本地或远程JVM进程中类装载、内存、垃圾收集、JIT编译等数据（如果要显示远程JVM信息，需要远程主机开启RMI支持）。
如果在服务启动时没有指定启动参数-verbose:gc，则可以用jstat实时查看gc情况。

命令格式:jstat [option vmid [interval[s|ms] [count]]]
参数解释：
Options — 选项，我们一般使用 -gcutil 查看gc情况
vmid — VM的进程号，即当前运行的java进程号
interval– 间隔时间，单位为秒或者毫秒
count — 打印次数，如果缺省则打印无数次

```
>jstat -gc 1532
>jstat -gc 1532 1000 5
>jstat -gcutil 1532 1000 5
```

jstat有如下选项：
-class:
监视类装载、卸载数量、总空间及类装载所耗费的时间
-gc:
监听Java堆状况，包括Eden区、两个Survivor区、老年代、永久代等的容量，以用空间、GC时间合计等信息
-gcutil:
监视内容与-gc基本相同，不同的是这里显示的是已占用的百分比，如S0为86.53，代表着S0区已使用了86.53%
-gccapacity:
监视内容与-gc基本相同，但输出主要关注java堆各个区域使用到的最大和最小空间
-gcutil:
监视内容与-gc基本相同，但输出主要关注已使用空间占总空间的百分比
-gccause:
与-gcutil功能一样，但是会额外输出导致上一次GC产生的原因
-gcnew:
监视新生代GC状况
-gcnewcapacity:
监视新生代各个区的大小信息
-gcold:
监视老年代GC情况
-gcoldcapacity:
监视老年代的容量信息
-gcpermcapacity:
输出永久代使用到最大和最小空间
-compiler:
输出JIT编译器编译过的方法、耗时等信息
-printcompilation:
输出已经被JIT编译的方法

一些术语的中文解释：

S0C：S0区容量（S1区相同，略）
S0U：S0区已使用
EC：E区容量
EU：E区已使用
OC：老年代容量
OU：老年代已使用
PC：Perm容量
PU：Perm区已使用
YGC：Young GC（Minor GC）次数
YGCT：Young GC总耗时
FGC：Full GC次数
FGCT：Full GC总耗时
GCT：GC总耗时

NGCMN：年轻代(young)中初始化(最小)的大小 (字节)
NGCMX：年轻代(young)的最大容量 (字节)
NGC：年轻代(young)中当前的容量 (字节)
OGCMN：old代中初始化(最小)的大小 (字节)
OGCMX：old代的最大容量 (字节)
OGC：old代当前新生成的容量 (字节)
PGCMN：perm代中初始化(最小)的大小 (字节)
PGCMX：perm代的最大容量 (字节) www.2cto.com
PGC：perm代当前新生成的容量 (字节)
S0：年轻代中第一个survivor（幸存区）已使用的占当前容量百分比
S1：年轻代中第二个survivor（幸存区）已使用的占当前容量百分比
E：年轻代中Eden（伊甸园）已使用的占当前容量百分比
O：old代已使用的占当前容量百分比
P：perm代已使用的占当前容量百分比
S0CMX：年轻代中第一个survivor（幸存区）的最大容量 (字节)
S1CMX ：年轻代中第二个survivor（幸存区）的最大容量 (字节)
ECMX：年轻代中Eden（伊甸园）的最大容量 (字节)
DSS：当前需要survivor（幸存区）的容量 (字节)（Eden区已满）
TT： 持有次数限制
MTT ： 最大持有次数限制

实例使用：
C:\Users\小健酱>jstat -gc 7536

```
 S0C    S1C    S0U    S1U      EC       EU        OC         OU       MC     MU    CCSC   CCSU   YGC     YGCT    FGC    FGCT     GCT
512.0  512.0   0.0   288.0  33280.0  31036.6   118784.0   44997.3   44120.0 42762.9 4992.0 4670.8    409    1.506   2      0.129    1.636
```

## 3、jstack

用于生成当前JVM的所有线程快照，线程快照是虚拟机每一条线程正在执行的方法,目的是定位线程出现长时间停顿的原因。
-F:当正常输出的请求不被响应时，强制输出线程堆栈
-l:除堆栈外，显示关于锁的附加信息
-m:如果调用到本地方法的话，可以显示C/C++的堆栈
命令格式:jstack [option] vmid
命令行参数选项说明如下：

##### -l long listings，

会打印出额外的锁信息，在发生死锁时可以用jstack -l pid来观察锁持有情况，在输出会显示死锁以及发生死锁的若干线程、死锁线程持有的对象和等待的对象

## 4、jmap

jmap用来查看堆内存使用状况，一般结合jhat使用。
用于显示当前Java堆和永久代的详细信息（如当前使用的收集器，当前的空间使用率等）

-dump:生成java堆转储快照
-heap:显示java堆详细信息
-F:当虚拟机进程对-dump选项没有响应时，可使用这个选项强制生成dump快照(只在Linux/Solaris下有效)
-finalizerinfo:显示在F-Queue中等待Finalizer线程执行finalize方法的对象(只在Linux/Solaris下有效)
-histo:显示堆中对象统计信息
-permstat:以ClassLoader为统计口径显示永久代内存状态(只在Linux/Solaris下有效)

命令格式:jmap [option] vmid

其中前面3个参数最重要，如：

#### 查看堆详细信息：jmap -heap pid

```
C:\Users\小健酱>jmap -heap 8068
Attaching to process ID 8068, please wait...
Debugger attached successfully.
Server compiler detected.
JVM version is 25.51-b03

using thread-local object allocation.
Parallel GC with 8 thread(s)

Heap Configuration:
   MinHeapFreeRatio         = 0
   MaxHeapFreeRatio         = 100
   MaxHeapSize              = 4278190080 (4080.0MB)
   NewSize                  = 89128960 (85.0MB)
   MaxNewSize               = 1426063360 (1360.0MB)
   OldSize                  = 179306496 (171.0MB)
   NewRatio                 = 2
   SurvivorRatio            = 8
   MetaspaceSize            = 21807104 (20.796875MB)
   CompressedClassSpaceSize = 1073741824 (1024.0MB)
   MaxMetaspaceSize         = 17592186044415 MB
   G1HeapRegionSize         = 0 (0.0MB)

Heap Usage:
PS Young Generation
Eden Space:
   capacity = 147324928 (140.5MB)
   used     = 88394936 (84.29998016357422MB)
   free     = 58929992 (56.20001983642578MB)
   59.99998588154749% used
From Space:
   capacity = 524288 (0.5MB)
   used     = 32768 (0.03125MB)
   free     = 491520 (0.46875MB)
   6.25% used
To Space:
   capacity = 524288 (0.5MB)
   used     = 0 (0.0MB)
   free     = 524288 (0.5MB)
   0.0% used
PS Old Generation
   capacity = 179306496 (171.0MB)
   used     = 643200 (0.6134033203125MB)
   free     = 178663296 (170.3865966796875MB)
   0.35871539199561403% used

1544 interned Strings occupying 142360 bytes.
```

生成dump文件： jmap -dump:file=./test.prof 309

#### 查看当前堆中对象统计信息： jmap -histo 8068：

用jmap把进程内存使用情况dump到文件中，再用jhat分析查看
jmap进行dump命令格式如下：

#### jmap -dump:format=b,file=dumpFileName pid

```
C:\Users\小健酱>jmap -dump:format=b,file=H:\heap.hprof 10732
Dumping heap to H:\heap.hprof ...
Heap dump file created
```

## 5、jhat

用于分析使用jmap生成的dump文件，是JDK自带的工具，前文中jmap输出堆文件heap.hporf为例子：

```
C:\Users\小健酱>jmap -dump:format=b,file=c:\heap.hprof 10732
Dumping heap to C:\heap.hprof ...
Permission denied

C:\Users\小健酱>jmap -dump:format=b,file=H:\heap.hprof 10732
Dumping heap to H:\heap.hprof ...
Heap dump file created

C:\Users\小健酱>jhat H:\heap.hprof
Reading from H:\heap.hprof...
Dump file created Sun Jul 31 19:20:35 CST 2016
Snapshot read, resolving...
Resolving 2740999 objects...
Chasing references, expect 548 dots....................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................
Eliminating duplicate references....................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................................
Snapshot resolved.
Started HTTP server on port 7000
Server is ready.
```

jhat在分析完后以HTTP服务器展示其分析结果，在浏览器中访问[http://localhost:7000，结果如图：](http://localhost:7000%EF%BC%8C%E7%BB%93%E6%9E%9C%E5%A6%82%E5%9B%BE%EF%BC%9A/)

![d](http://daiguahub.com/img/m.jpg)

不过jhat没有mat好用，推荐使用mat（Eclipse插件： <http://www.eclipse.org/mat> ），mat速度更快，而且是图形界面。

## 6、jinfo查看和设置某个参数的值

jinfo可以用来查看正在运行的java程序的扩展参数，甚至支持在运行时修改部分参数
jinfo命令格式：
jinfo [option] 
其中option可以为以下几种信息：
-flag :打印指定参数信息
-flag [+|-]:设置指定参数的布尔值
-flag =:设置指定参数的值
pid虚拟机进程id 可以通过 jps命令查看
例如直接使用 jinfo -flags pid 查看vm的设置参数（只显示部分，没有显示所有参数）:

```
C:\Users\小健酱>jinfo -flags 7536
Attaching to process ID 7536, please wait...
Debugger attached successfully.
Server compiler detected.
JVM version is 25.51-b03
Non-default VM flags: -XX:CICompilerCount=4 -XX:InitialHeapSize=268435456 -XX:MaxHeapSize=4278190080 -XX:MaxNewSize=1426063360 -XX:MinHeapDeltaBytes=524288 -XX:NewSize=89128960 -XX:OldSize=179306496 -XX:+UseCompressedClassPointers -XX:+UseCompressedOops -XX:+UseFastUnorderedTimeStamps -XX:-UseLargePagesIndividualAllocation -XX:+UseParallelGC
Command line:  -Dcatalina.base=C:\Users\小健酱\workspace\.metadata\.plugins\org.eclipse.wst.server.core\tmp1 -Dcatalina.home=F:\Tomcat\apache-tomcat-8.0.36 -Dwtp.deploy=C:\Users\小健酱\workspace\.metadata\.plugins\org.eclipse.wst.server.core\tmp1\wtpwebapps -Djava.endorsed.dirs=F:\Tomcat\apache-tomcat-8.0.36\endorsed -Dfile.encoding=GBK
```

再比如查询NewSize参数的值：

```
C:\Users\小健酱>jinfo -flag NewSize 7536
-XX:NewSize=89128960
```

查询新生代晋升到老年代的最大年龄：

```
C:\Users\小健酱>jinfo -flag MaxTenuringThreshold 8068
-XX:MaxTenuringThreshold=15
```

jinfo也可以运行时修改参数，但是这个修改能力是极其有限的，下面是修改PrintGCDetails的实例，可以在程序运行时，关闭或者打开这个开关

```
C:\Users\小健酱>jinfo -flag PrintGCDetails 8068
-XX:-PrintGCDetails

C:\Users\小健酱>jinfo -flag +PrintGCDetails 8068

C:\Users\小健酱>jinfo -flag PrintGCDetails 8068
-XX:+PrintGCDetails
```

## 7、java -X 命令查看JVM的配置说明

运行后如下结果，这些就是配置JVM参数的秘密武器

```
C:\Users\小健酱>java -X
    -Xmixed           混合模式执行 (默认)
    -Xint             仅解释模式执行
    -Xbootclasspath:<用 ; 分隔的目录和 zip/jar 文件>
                      设置搜索路径以引导类和资源
    -Xbootclasspath/a:<用 ; 分隔的目录和 zip/jar 文件>
                      附加在引导类路径末尾
    -Xbootclasspath/p:<用 ; 分隔的目录和 zip/jar 文件>
                      置于引导类路径之前
    -Xdiag            显示附加诊断消息
    -Xnoclassgc       禁用类垃圾收集
    -Xincgc           启用增量垃圾收集
    -Xloggc:<file>    将 GC 状态记录在文件中 (带时间戳)
    -Xbatch           禁用后台编译
    -Xms<size>        设置初始 Java 堆大小
    -Xmx<size>        设置最大 Java 堆大小
    -Xss<size>        设置 Java 线程堆栈大小
    -Xprof            输出 cpu 配置文件数据
    -Xfuture          启用最严格的检查, 预期将来的默认值
    -Xrs              减少 Java/VM 对操作系统信号的使用 (请参阅文档)
    -Xcheck:jni       对 JNI 函数执行其他检查
    -Xshare:off       不尝试使用共享类数据
    -Xshare:auto      在可能的情况下使用共享类数据 (默认)
    -Xshare:on        要求使用共享类数据, 否则将失败。
    -XshowSettings    显示所有设置并继续
    -XshowSettings:all
                      显示所有设置并继续
    -XshowSettings:vm 显示所有与 vm 相关的设置并继续
    -XshowSettings:properties
                      显示所有属性设置并继续
    -XshowSettings:locale
                      显示所有与区域设置相关的设置并继续
```

怎么用这这些参数呢？其实所有的命令行都是这么一用，下面我就给出一个最简单的HelloWorl的例子来演示这个参数的用法，非常的简单。

```
HelloWorld.java
-----------------------------------------------
public class  HelloWorld
{
 public static void main(String[] args)
 {
  System.out.println("Hello World!");
 }
}
```

编译并运行：

```
D:\j2sdk15\bin>javac HelloWorld.java
D:\j2sdk15\bin>java -Xms256M -Xmx512M HelloWorld    
Hello World!
```

那么如何在大型系统或者应用中配置JVM参数呢？比如你配置IDE工具的参数，常见的有IDEA、Eclipse，这个是在一个配置文件中指定即可。如果你要在J2EE环境中配置这些参数，那么你需要在J2EE应用服务器或者Servlet容器相关启动参数设置处指定，其启动文件中来配置，Tomcat是在catalina.bat中配置，weblogic和websphere是在其他地方。

## 8、-XX:+PrintFlagsFinal参数

可以获取到所有可设置参数及值(手动设置之后的值)，这个参数只能使用在Jdk6 update 21以上版本(包括该版本)。-XX:+PrintFlagsFinal参数的使用 与上面-XX:+PrintFlagsInitial 参数使用相同 java -XX:+PrintFlagsFinal