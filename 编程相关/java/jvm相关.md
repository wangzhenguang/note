
### jvm 参数列表

java -Xmx3550m -Xms3550m -Xmn2g -Xss128k -XX:NewRatio=4 -XX:SurvivorRatio=4 -XX:MaxPermSize=16m -XX:MaxTenuringThreshold=0

- -Xmmx3550m: 最大堆内存为 3550m
- -Xmms3550m : 初始堆内存为3550m，此值可以设置与-Xmx 相同，以避免每次垃圾回收完成后JVM重新分配内存。

- -Xmn2g :设置年轻代为2g
> 整个堆大大小 = 年轻代大小 + 年老代大小 + 持久代大小。 持久代一般固定大小为64m，所以增大年轻代后，将会减少年老代大小。sun公司推荐为整个堆 3/8.

- -Xss128k :设置每个线程的堆栈大小

- -XX:NewRatio=4 设置年轻代 与年老代的比值，设置成4，则年轻代与年老代所占比值为1：4 ，年轻代占整个堆栈的1/5。

- -XX:SurvivorRatio=4: 设置年轻代中Eden 区 与Survivor区的大小比值。设置为4，则两个Survivor区与一个Eden区的比值为2:4,一个Survivor区占整个年轻代的1/6

- -XX:MaxPermSize=16m :设置持久代大小
- -XX:MaxTenuringThreshold=15  :设置垃圾最大年龄。
> 如果设置为0的画，则年轻代对象不经过Survivor区，直接进入年老代。对于年老代多的应用，可以提高效率。 如果设置为一个较大的值，则年轻代对象会在Survivor区进行多次复制，这样可以增加对象在年轻代的存活时间，增加在年轻代被回收的概论。


  