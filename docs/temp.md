数据存储
Q：Android 中提供哪些数据持久存储的方法？
Q：Java 中的 I/O 流读写怎么做？
Q：SharePreferences 适用情形？使用中需要注意什么？
Q：了解 SQLite 中的事务处理吗？是如何做的？
Q：使用 SQLite 做批量操作有什么好的方法吗？
Q：如果现在要删除 SQLite 中表的一个字段如何做？
Q：使用 SQLite 时会有哪些优化操作?

IPC
Q：Android 中进程和线程的关系？区别？
Q：为何需要进行 IPC？多进程通信可能会出现什么问题？
Q：什么是序列化？Serializable 接口和 Parcelable 接口的区别？为何推荐使用后者？
Q：Android 中为何新增 Binder 来作为主要的 IPC 方式？
Q：使用 Binder 进行数据传输的具体过程？
Q：Binder 框架中 ServiceManager 的作用？
Q：Android 中有哪些基于 Binder 的 IPC 方式？简单对比下？
Q：是否了解 AIDL？原理是什么？如何优化多模块都使用 AIDL 的情况？

View
Q：MotionEvent 是什么？包含几种事件？什么条件下会产生？
Q：scrollTo() 和 scrollBy() 的区别？
Q：Scroller 中最重要的两个方法是什么？主要目的是？
Q：谈一谈 View 的事件分发机制？
Q：如何解决 View 的滑动冲突？
Q：谈一谈 View 的工作原理？
Q：MeasureSpec 是什么？有什么作用？
Q：自定义 View/ViewGroup 需要注意什么？
Q：onTouch()、onTouchEvent() 和 onClick() 关系？
Q：SurfaceView 和 View 的区别？
Q：invalidate() 和 postInvalidate() 的区别？

Drawable 等资源
Q：了解哪些 Drawable？适用场景？
Q：mipmap 系列中 xxxhdpi、xxhdpi、xhdpi、hdpi、mdpi 和 ldpi 存在怎样的关系？
Q：dp、dpi、px 的区别？
Q：res 目录和 assets 目录的区别？

Animation
Q：Android 中有哪几种类型的动画？
Q：帧动画在使用时需要注意什么？
Q：View 动画和属性动画的区别？
Q：View 动画为何不能真正改变 View 的位置？而属性动画为何可以？
Q：属性动画插值器和估值器的作用？

Window
Q：Activity、View、Window 三者之间的关系？
Q：Window 有哪几种类型？
Q：Activity 创建和 Dialog 创建过程的异同？

Handler
Q：谈谈消息机制 Hander？作用？有哪些要素？流程是怎样的？
Q：为什么系统不建议在子线程访问 UI？
Q：一个 Thread 可以有几个 Looper？几个 Handler？
Q：如何将一个 Thread 线程变成 Looper 线程？Looper 线程有哪些特点？
Q：可以在子线程直接 new 一个 Handler 吗？那该怎么做？
Q：Message 可以如何创建？哪种效果更好，为什么？
Q：这里的 ThreadLocal 有什么作用？
Q：主线程中 Looper 的轮询死循环为何没有阻塞主线程？
Q：使用 Handler 的 postDealy() 后消息队列会发生什么变化？

线程
Q：Android 中还了解哪些方便线程切换的类？
Q：AsyncTask 相比 Handler 有什么优点？不足呢？
Q：使用 AsyncTask 需要注意什么？
Q：AsyncTask 中使用的线程池大小？
Q：HandlerThread 有什么特点？
Q：快速实现子线程使用 Handler
Q：IntentService 的特点？
Q：为何不用 bindService 方式创建 IntentService？
Q：线程池的好处、原理、类型？
Q：ThreadPoolExecutor 的工作策略？
Q：什么是 ANR？什么情况会出现 ANR？如何避免？在不看代码的情况下如何快速定位出现 ANR 问题所在？

Bitmap
Q：加载图片的时候需要注意什么？
Q：LRU 算法的原理？
Q：Android 中缓存更新策略？

性能优化
Q：项目中如何做性能优化的？
Q：了解哪些性能优化的工具？
Q：布局上如何优化？列表呢？
Q：内存泄漏是什么？为什么会发生？常见哪些内存泄漏的例子？都是怎么解决的？
Q：内存泄漏和内存溢出的区别？
Q：什么情况会导致内存溢出？

谷歌新动态

Q：是否了解和使用过谷歌推出的新技术？
Q：有了解刚发布的 Androidx.0 的特性吗？
Q：Kotlin 对 Java 做了哪些优化？

3.2 Java
基础
Q：面向对象编程的四大特性及其含义？
Q：String、StringBuffer 和 StringBuilder 的区别？
Q：String a=""和 String a=new String("") 的的关系和异同？
Q：Object 的 equal() 和 == 的区别？
Q：装箱、拆箱什么含义？
Q：int 和 Integer 的区别？
Q：遇见过哪些运行时异常？异常处理机制知道哪些？
Q：什么是反射，有什么作用和应用？
Q：什么是内部类？有什么作用？静态内部类和非静态内部类的区别？
Q：final、finally、finalize() 分别表示什么含义？
Q：重写和重载的区别？
Q：抽象类和接口的异同？
Q：为什么匿名内部类中使用局部变量要用 final 修饰？
Q：Object 有哪些公用方法？

集合
Q：Java 集合框架中有哪些类？都有什么特点
Q：集合、数组、泛型的关系，并比较
Q：ArrayList 和 LinkList 的区别？
Q：ArrayList 和 Vector 的区别？
Q：HashSet 和 TreeSet 的区别？
Q：HashMap 和 Hashtable 的区别？
Q：HashMap 在 put、get 元素的过程？体现了什么数据结构？
Q：如何解决 Hash 冲突？
Q：如何保证 HashMap 线程安全？什么原理？
Q：HashMap 是有序的吗？如何实现有序？
Q：HashMap 是如何扩容的？如何避免扩容？
Q：hashcode() 的作用，与 equal() 有什么区别？

并发
Q：开启一个线程的方法有哪些？销毁一个线程的方法呢？
Q：同步和非同步、阻塞和非阻塞的概念
Q：Thread 的 join() 有什么作用？
Q：线程的有哪些状态？
Q：什么是线程安全？保障线程安全有哪些手段？
Q：ReentrantLock 和 synchronized 的区别?
Q：synchronized 和 volatile 的区别？
Q：synchronized 同步代码块还有同步方法本质上锁住的是谁？为什么？
Q：sleep() 和 wait() 的区别？

Java 新动态
Q：是否了解 Java1.x 的特性吗？
Q：谈谈对面向过程编程、面向对象编程还有面向切面编程的理解

3.3 计算机网络
基础
Q：五层协议的体系结构分别是什么？每一层都有哪些协议？
Q：为何有 MAC 地址还要 IP 地址？

TCP
Q：TCP 和 UDP 的区别？
Q：拥塞控制和流量控制都是什么，两者的区别？
Q：谈谈 TCP 为什么要三次握手？为什么要四次挥手？
Q：播放视频用 TCP 还是 UDP？为什么？

HTTP
Q：了解哪些响应状态码？
Q：get 和 post 的区别？
Q：Http1.0、Http1.1、Http2.0 的区别？
Q：HTTP 和 TCP 的区别?
Q：HTTP 和 HTTPS 的区别?
Q：HTTP 和 Socket 的区别?
Q：在地址栏打入 http://www.baidu.com 会发生什么？

3.4 JVM
Q：JVM 内存是如何划分的？
Q：谈谈垃圾回收机制？为什么引用计数器判定对象是否回收不可行？知道哪些垃圾回收算法？
Q：Java 中引用有几种类型？在 Android 中常用于什么情景？
Q：类加载的全过程是怎样的？什么是双亲委派模型？
Q：工作内存和主内存的关系？在 Java 内存模型有哪些可以保证并发过程的原子性、可见性和有序性的措施？
Q：JVM、Dalvik、ART 的区别？
Q：Java 中堆和栈的区别？

3.5 操作系统
Q：操作系统中进程和线程的区别？
Q：死锁的产生和避免?

3.5 数据结构 & 算法
Q：怎么理解数据结构？
Q：什么是斐波那契数列？
Q：迭代和递归的特点，并比较优缺点
Q：了解哪些查找算法，时间复杂度都是多少？
Q：了解哪些排序算法，并比较一下，以及适用场景
Q：快排的基本思路是什么？最差的时间复杂度是多少？如何优化？
Q：AVL 树插入或删除一个节点的过程是怎样的？
Q：什么是红黑树？
Q：100 盏灯问题
Q：老鼠和毒药问题，加个条件，必须要求第二天出结果
Q：海量数据问题
Q：（手写算法）二分查找
Q：（手写算法）反转链表
Q：（手写算法）用两个栈实现队列
Q：（手写算法）多线程轮流打印问题
Q：（手写算法）如何判断一个链有环 / 两条链交叉
Q：（手写算法）快速从一组无序数中找到第 k 大的数 / 前 k 个大的数
Q：（手写算法）最长（不）重复子串

3.6 设计模式
Q：谈谈 MVC、MVP 和 MVVM，好在哪里，不好在哪里？
Q：如何理解生产者消费者模型？
Q：是否能从 Android 中举几个例子说说用到了什么设计模式？
Q：装饰模式和代理模式有哪些区别？
Q：实现单例模式有几种方法？懒汉式中双层锁的目的是什么？两次判空的目的又是什么？
Q：谈谈了解的设计模式原则？

3.7 数据库
Q：数据库中的事务了解吗？事务的四大特性？
Q：如何理解数据库的范式？

3.8 hr 问题
Q：请简单的自我介绍一下
Q：谈谈项目经历，为什么会做，怎么做的，遇到的难点？
Q：谈谈实习经历，做了什么，收获有哪些？
Q：谈谈学习 Android 的经历，有哪些学习方法和技巧？
Q：是否会考研？/ 为何不保研？
Q：成绩怎么样？奖学金情况?
Q：学过哪些课程？那门课印象最深刻 / 最有意义 / 学的最好 / 最不喜欢？为什么？
Q：近 x 年的职业规划？
Q：为什么想来我们公司？/ 为何不转正留在 xx?
Q：对公司 / 部门是否有了解？
Q：为何会选择做技术？/ 对女生做开发的看法？
Q：学习生活中遇到什么挫折，如何解决的？
Q：还投过那些公司，进展如何？如何 xx 和 xx 都给你发 offer 会如何选择？
Q：家是哪里的？是独生子女吗？从小的家庭环境如何？
Q：平常有哪些兴趣爱好？大学参加了哪些校园活动？
Q：有男 / 女朋友吗？未来有什么规划？
Q：评价一下自己的优缺点？/ 用 x 个词形容你自己。/ 别人都是怎样评价你的？
Q：觉得自己博客写的最好的文章是什么？为什么？
Q：觉得自己的优势是什么？
Q：如何看待加班？
Q：意向工作城市是哪？/ 是否会考虑在 xx 发展?
Q：对于薪酬有什么想法？
Q：有什么问题想要问我？

3.9 项目相关、实习相关技术问题（略）
Q：使用那些版本控制工具？Git 和 SVN 的区别？
Q：了解 Git 工具吗？用过哪些命令？解决冲突时 git merge 和 git rebase 的区别？

点击此处见 Android 学习笔记清单：
https://www.jianshu.com/p/c44d7a106302