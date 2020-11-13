# 一   自己整理

##  1.  ArrayList 实现原理 
底层由数组实现，默认创建容量大小为10的数组。随着ArrayList中元素的增加，数组容量大小也会发生变化。当ArrayList中存放的元素个数大于其数组容量大小后，ArrayList就会对数组进行扩容，扩充后的容量数原容量的1.5倍，同时将数组中的内容复制到新的数组中，而这种复制操作是很消耗资源的，所以要尽量避免这种操作，如果在创建ArrayList 的时候可以预见数组的大小，那么ArrayList正好提供了一个可以初始化数组容量大小的构造函数，建议创建的时候指定其容量大小。

##  2.   LinkArrayList实现原理

## 3.   HashMap实现原理
HashMap map = new HashMap();
底层创建了默认长度为 16 的 Entry 数组向 HashMap 中添加entry1(key， value)，需要首先计算 entry1 中 key 的哈希值(根据 key 所在类的 hashCode()计算得到)，此哈希值经过处理以后，得到在底层 Entry[ ]数组中要存储的位置 i，如果位置 i 上没有元素，则 entry1直接添加成功。如果位置 i 上已经存在 entry2(或还有链表存在的 entry3， entry4)，这种情况称之为Hash碰撞，则需要通过循环的方法，依次比较 entry1 中 key 和其他 entry的 key 是否 equals。如果返回值为 true，则使用 entry1 的 value 去替换 equals 为 true的 entry 的 value。如果遍历一遍以后，发现所有的 equals 返回都为 false，则 entry1 仍可添加成功。 entry1 指向原有的 entry 元素。
默认情况下，如果添加元素的长度 >= 默认长度* 负载因子(默认值为0.75)，且新要添加的数组位置不为 null 的情况下，就进行扩容。默认扩容为原有长度的 2 倍。将原有的数据复制到新的数组中。

![](https://upload-images.jianshu.io/upload_images/25046096-437bb53514f521f1.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

jdk 8.0中的改进 :
1.  HashMap map = new HashMap();//默认情况下，先不创建长度为 16 的数组。
2.  当首次调用 map.put()时，再创建长度为 16 的数组
3. 当还是链表存储时，新加的 entry 放在链表尾部，而Java7中则是放到链表头部（“七上八下”）。
4.  当数组指定索引位置的链表长度>8 时，且 map 中的数组的长度> 64 时，此索引位置上的所有 entry 使用红黑树进行存储。

##  4.   LinkedHashMap实现原理
继承于 HashMap，底层由 哈希表 和 双向链表 来 保存元素。采用的哈希算法和HashMap相同，但重新定义了数组中的Entry元素， Entry 除了保存当前对象的引用外，还保存了上一个元素 before 和下一个元素 after 的引用，从而在哈希表的基础上构成了双向链表。
定义了排序模式：accessOrder(boolean型变量)。当为false时：插入顺序；当为true时：访问顺序。默认为false。  若为访问顺序，则读取元素的时候，将最新访问的元素添加到双向链表的表头，并从原来的位置删除。由于链表的添加、删除是常量级的，故并不会带来性能损失。

![](https://upload-images.jianshu.io/upload_images/25046096-c5ea24cef40b24e4.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 5.   ConcurrentHashMap 实现原理
因为HashMap在高并发的场景下会出现链表环，一旦形成环形数据结构，Entry的next节点永远不为空，就会产生死循环。而HashTable效率又很低下，所以就出现了 ConcurrentHashMap。

JDK7 中的实现原理：
数组 + Segment + 分段锁  （其中 Segment 继承自ReentrantLock）
将数据分成一段一段的存储，然后给每一段数据配一把锁，当一个线程占用锁访问其中一个段数据的时候，其他段的数据也能被其他线程访问，能够实现真正的并发访问。

![](https://upload-images.jianshu.io/upload_images/25046096-c99454813b7d5fb5.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

定位一个元素，需要两次哈希操作，第一次定位到Segment，第二次定位到元素所在链表的头部。
坏处：哈希过程要比普通的 HashMap 长。
优点：提高了并发能力。

JDK8 中的实现原理：
抛弃Segment，改用 CAS + synchronized 控制并发操作。与Java8中的HashMap类似，采用 数组+链表+红黑树 来存储数据。
通过 Node 实现，保存key，value及key的hash值的数据结构。其中value和next都用volatile 修饰，保证并发的可见性。Node是ConcurrentHashMap存储结构的基本单元，继承于HashMap中的Entry

## 6.    HashMap和HashTable的区别

HashMap: ① Map 的主要实现类
                 ②线程不安全的
                 ③可以存储 null 的 key 和 value
                 ④ 继承自 AbstractMap 类，然后实现Map接口。
                 ⑤ 默认容量为 16，要求底层数组容量一定是 2 的整数次幂，扩容时，将容量扩充为原容量的 2 倍。
                 ⑥ 哈希值是根据key的hashcode，然后重新计算得到。根据哈希值求对应位置索引时，采用与运算。
              
HashTable: ① Map 的古老实现类
                  ② 线程安全的
                  ③ 不可以存储 null 的 key 和 value
                  ④ 继承自 Dictionary 类，然后实现Map接口。
                  ⑤ 默认容量是 11 ，不要求底层数组容量一定是 2 的整数次幂，扩容时，将容量扩充为原容量的 2 倍 +1。
                  ⑥ 哈希值就取 key 的hashcode，根据哈希值求对应位置索引时，采用 取模运算 。
注：HashMap 和 HashTable 实现了Serializable接口，因此它支持序列化，实现了Cloneable接口，能被克隆。
它们对key的hash算法和hash值到内存索引的映射算法不同。

##  7.   HashMap 和  ConcurrentHashMap的区别

