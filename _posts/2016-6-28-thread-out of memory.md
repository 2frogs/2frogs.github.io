JAVA中，线程栈大小由Xss参数控制，JDK5后默认1M。当创建一个线程时，JVM只是创建一个Thread对象，同时创建一个操作系统线程。JVM只是对象占用空间，线程占用的内存是系统内存。

比如可用内存1.6G左右，将Xss设置成100M，Xmx设置成10M，创建165个线程后才报java.lang.OutOfMemoryError: unable to create new native thread

如果线程中声明一个10M的数组，则会报java.lang.OutOfMemoryError: Java heap space

由此可见创建的线程数量多少，更多取决于系统剩余内存。如果Xms分配越多内存，系统剩余内存越少，会导致能创建的线程越少。

前几天还遇到个奇怪的问题，ThreadDump报 Can't create GC Thread. Out of System resources. 可能也与此有关。

JVM调试常用参数如下：
-Xms100m -Xmx100m -Xss10m -Xmn30m
-XX:CompileThreshold=8000
-XX:PermSize=20M
-XX:MaxPermSize=20m
-XX:+HeapDumpOnOutOfMemoryError
-verbose:gc
-XX:+PrintGCDetails
-XX:+PrintGCDateStamps
-Xloggc:test_gc.log
-XX:+UseParNewGC
-XX:CMSInitiatingOccupancyFraction=70
-XX:+UseConcMarkSweepGC
-XX:+CMSScavengeBeforeRemark
-XX:CMSFullGCsBeforeCompaction=5

Xmx: 最大堆内存
Xms: JVM初始内存，可与Xmx相同避免垃圾回收完后JVM重新分配内存
Xmn：年轻代大小。Sun推荐为堆大小的3/8. 设置太大减少老年代大小，设置太小，年轻代刷新频繁，年龄增长较快，会更频繁触发Full GC
