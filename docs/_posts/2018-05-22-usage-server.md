---
layout: post
title:  服务端使用文档
categories: 教程
author: kongkongye
---

* content
{:toc}

主要给`服主`查阅,介绍如何在服务端安装与使用此菜单MOD.




## 安装

## 服务器使用

## 代理服务器(Proxy Server)
处于OSI模型的会话层,起到防火墙的作用.大多用来连接国际互联网与局域网.

### 主要功能
1. 突破自身IP限制,访问国外站点,教育网等
2. 访问一些单位或团体的内部资源
3. 提高访问速度,可能有缓冲
4. 隐藏真实IP

### 分类
1. http(s)代理
2. socks代理(即sockets代理)
3. vpn代理: 在公用网络上搭建专用网络的技术,通过加密与认证来保证安全.
4. 反向代理
  1. 加密与ssl加速
  2. 负载均衡
  3. 缓存静态内容
  4. 压缩,减速上传
  5. 安全,外网发布
5. ftp代理
6. rtsp代理
7. pop3代理

### 软件
1. squid server

## 内存泄露
指内存被占着,但不再使用.

## 判断JVM运行在Client还是Server模式
使用`java -version`即可查看

## 开启jmx
要开启java VM的jmx功能,需要添加以下启动参数:

```
-Dcom.sun.management.jmxremote.port=8888
```

jmx在客户端连接上后,rmi会选择另一个随机端口让客户端连接,如果服务器上有防火墙,则可能导致连接不上,
解决办法就是固定rmi的端口,添加以下参数(端口推荐与上面的相同,但如果ssl开启,则无法设置相同):

```
-Dcom.sun.management.jmxremote.rmi.port=8888
```

此外,如果服务端与客户端不在一个网段下,可能还需要指定服务端绑定的域名,否则访问不了:

```
-Djava.rmi.server.hostname=1.2.3.4
```

如果需要关闭客户端登录认证,可以加上以下参数(不安全):

```
-Dcom.sun.management.jmxremote.authenticate=false
-Dcom.sun.management.jmxremote.ssl=false
```

todo

## java中有哪些常用的包?
* `java.lang`: 提供基础类,是默认导入的包.重要的有`Runnable`,`Object`,`Math`,`String`,`StringBuffer`,`System`,`Thread`,`Throwable`等
* `java.util`: 提供集合框架,事件模型,日期时间,国际化和各种工具类
* `java.io`: 通过文件系统,数据流和序列化提供系统输入与输出
* `java.nio`: 异步io
* `java.net`: 提供网络相关类
* `java.sql`
* `java.awt`: 提供了创建界面与图像的类
* `javax.swing`: 提供一组`轻量级`组件,以使在不同工作平台上工作方式相同
* `java.text`: 提供了与自然语言无关的方式来处理文本,日期,数字和消息的类和接口

## java中常用的集合类
* `Collection`
  * `Set`: 不能包含重复元素
      * `HashSet`
      * `TreeSet`
  * `List`: 有序列表
      * `ArrayList`: 内部使用`数组`存储,适合查询操作多,增删操作少的情况
      * `LinkedList`: 内部使用双向`链表`存储,适合增删操作多,查询操作少的情况
      * `Vector`: 内部使用数组存储,线程安全
          * `Stack`
  * `Queue`
* `Map`: 不能包含重复的键,但可以包含重复的值
  * `HashMap`: 内部使用哈希表实现,线程不安全,允许键或值为null
      * `LinkedHashMap`: 有序,但遍历比HashMap慢
  * `Hashtable`: 线程安全,但也因此效率低于HashMap,不允许键或值为null
  * `ConcurrentHashMap`: 线程安全,并且锁分离.内部使用段(Segment)来表示这些不同的部分,每个段其实就是一个小的hashtable,它们有自己的锁.只要多个修改操作发生在不同的段上,它们就可以并发进行.
  * `TreeMap`: 内部使用红黑树实现,不允许键为null

## transient关键字作用
实现了`Serializable`接口的类在被序列化时,有此关键字的属性不会被序列化,如敏感的密码字段

## ThreadLocal(线程局部变量)
本质上就是每个线程都维护了一个map,map的key就是threadlocal,值就是我们set的那个值

## 哈希表(散列表)
数组的特点是寻址容易,插入和删除困难;链表的特点是寻址困难,插入和删除容易.
而哈希表就是这两者优点的结合,寻址容易,插入和删除也容易.

一个简单的实现可以描述为: 哈希表通过`散列函数`将key转换成一个整数值,再对数组长度进行取余,结果当作数组的下标,再将value存入数组中那个下标的位置.
通过转换,任意长度的输入会变换成固定长度的输出,该输出就是`散列值`.

比较常用的实现方法为`拉链法`(可以看做链表的数组).

## 时间复杂度

## epoll

## spring特点
* `IOC`/`DI`
    * `IOC`(Inversion of Control, 控制反转): 是面向对象编程中的一个设计原则,意在降低代码耦合度
    * `DI`(Dependency Injection, 依赖注入): 这是IOC的常用方式(其它的比如还有`依赖查找`方式)
        * 基于接口
        * 基于set方法
        * 基于构造函数
        * 基于注解
* `AOP`(面向切面)

## spring请求处理流程
1. 找到`HandlerMapping`,得到`HandlerExecutionChain`(就是handler+若干HandlerInterceptor)

    简单的说,就是根据请求找到对应的处理器(handler)

    具体逻辑为: 遍历所有注册的HandlerMapping,直到返回HandlerExecutionChain

    HandlerMapping的具体实现会根据请求来查找处理器,找到就返回.
    Spring默认会注册以下两个HandlerMapping:

    1. `BeanNameUrlHandlerMapping`: bean名与url匹配映射
    2. `RequestMappingHandlerMapping`: 根据有`@Controller`注解的类内的`@RequestMapping`来匹配映射

2. 找到`HandlerAdapter`,得到`ModelAndView`

    简单的说,就是根据处理器(handler)找到对应的适配器(HandlerAdapter),然后进行处理得到结果(ModelAndView)

    具体逻辑为:

    1. 遍历所有注册的HandlerAdapter直到找到能处理此handler的(通过调用HandlerAdapter的`boolean supports(handler)`方法)
    2. 然后进行处理得到ModelAndView(通过调用HandlerAdapter的`ModelAndView handle(request, response, handler)`方法)

    比如`RequestMappingHandlerAdapter`可以处理`HandlerMethod`这个handler(内部会调用相应方法),并产生ModelAndView输出

    PS: 为什么不直接调用handler?因为handler是Object类型的,具体实现可以是任意的类型,因此没法直接调用.
    而需要一个能处理它的HandlerAdapter来调用,大部分HandlerAdapter通过handler的类型来判断是否能够处理.

3. 解析`ModelAndView`,得到`View`并渲染返回

    具体逻辑为:

    1. 如果ModelAndView内包含`viewName`(即内部view字段的类型为String),那么就会遍历所有注册的`viewResolver`来解析出view
    2. 如果ModelAndView内直接包含view,则直接解析view

    PS: View内带有渲染的方法

## classloader(类加载器)
### java默认提供的classloader
1. `Bootstrap ClassLoader(启动类加载器)`: 加载核心类库,如rt.jar,resources.jar,charsets.jar等

    它不是java类,不继承自ClassLoader,底层由c++编写,嵌入到jvm内核中

2. `Extension ClassLoader(扩展类加载器)`: 加载扩展类库,默认加载`$JAVA_HOME/jre/lib/ext`目录下所有jar
3. `App ClassLoader(系统类加载器)`: 加载应用classpath目录下所有class和jar文件
4. `自定义类加载器`: 必须继承自ClassLoader

### 加载原理
ClassLoader采用`双亲委托`模型,使用自上而下的顺序来加载,这样方式的好处有:

1. 避免`重复加载`,即父加载器已经加载了,子加载器就不需要重新加载一次.
2. `安全考虑`,比如你没法实现个自定义的String类代替默认的

### 如何判断两个类是否相同
1. 由同一类加载器加载
2. 类名相同

## 创建对象方式
1. `new`
2. `反射`: Constructor的newInstance()方法
3. `clone()`: 类必须实现`Cloneable`接口,否则会抛`CloneNotSupportedException`异常.

    clone方法会直接复制字段内容,进行`浅拷贝`.
    并且构造函数(及其它的实例语句)不会被调用.

    Object的clone()方法默认是protected的,约定为子类覆盖此接口,并将方法修饰符改为public

4. `序列化`: 调用ObjectInputStream的readObject()方法

## 创建线程方式
1. 继承`Thread`: 使用`this`即可获得当前线程
2. 实现`Runnable`: 优点是线程类只是实现了Runnable接口,还能继承其它的类,更加灵活.访问当前线程使用`Thread.currentThread()`方法

## 序列化
如果想把对象的属性(不包括方法)保存到文件或数据库,或者通过网络/RMI进行传输,那么就需要序列化.

要序列化的类必须实现`Serializable`接口,并且所有属性必须是可序列化,
或者给属性加`transient`关键字表示是短暂的,这个属性就不会被序列化.

父类实现序列化后,子类也会自动实现自动化.

可以通过`ObjectOutputStream`进行序列化,通过`ObjectInputStream`进行反序列化.

#### 是否所有对象都能序列化?
并非所有的对象都可以序列化,原因比如:

1. 安全方面原因,比如一个对象有private的属性,但序列化后,这些属性值也会暴露
2. 资源分配方面原因,如socker,thread类,如果可以序列化传输,但资源也无法重新分配

## AOP/代理
### 分类
1. `静态代理`: 在编译阶段生成AOP代理类,也称为编译时增强,如`AspectJ`
2. `动态代理`: 在运行时借助`JDK动态代理`,`CGLIB`等在内存中临时生成AOP动态代理类,也称为运行时增强

### Spring AOP 底层实现
* 如果类实现了`InvocationHandler`接口,则使用`JDK动态代理`,为你生成代理对象.
* 如果类没有实现上述接口,则使用`CGLIB动态代理`,生成代理对象.

## 锁分类

### 公平锁/非公平锁
公平锁是指线程按照申请锁的顺序来获取锁.

非公平锁的吞吐量更大.

`ReentrantLock`可以通过构造函数指定是否是公平锁,默认是非公平锁.

`synchronized`是非公平锁,并且没任何办法变成公平锁.

### 可重入锁
又名`递归锁`,指在外层获取锁后,在内层会自动获取锁.

可一定程序避免死锁.

`ReentrantLock`与`synchronized`都是可重入锁.

以下代码描述了可重入锁:

```java
synchronized void setA() {
  setB();
}
synchronized void setB() {
}
```

### 独享锁/共享锁
独享锁指该锁只能被一个线程持有,共享锁指该锁可以同时被多个线程持有.

`ReentrantLock`与`synchronized`是独享锁,`ReadWriteLock`的读锁是共享锁,写锁是独享锁

### 互斥锁/读写锁
`ReentrantLock`是互斥锁,`ReadWriteLock`是读写锁

### 乐观锁/悲观锁
指看待并发同步的角度,悲观锁假定会发生冲突,因此会加锁(依靠数据库的加锁机制).
乐观锁假定不会发生冲突,只在提交时检测是否冲突,一般采用`版本号`方式实现,乐观锁不能解决`脏读`问题.

乐观锁适合大量读操作的场景,会带来大量性能提升.

### 分段锁
分段锁是一种锁的设计.

`ConcurrentHashMap`使用的就是分段锁.

### 偏向锁/轻量级锁/重量级锁
这三种指的是`synchronized`锁的状态.

偏向锁指一段代码一直被一个线程所访问,那么该线程会自动获取锁,降低获取锁的代价.
轻量级锁指锁是偏向锁的时候,被另一个线程所访问,偏向锁就会升级为轻量级锁,其它线程会通过自旋的形式尝试获取锁,不会阻塞,提高性能.
重量级锁指锁为轻量级锁的时候,另一个线程在自旋一定次数后,如果还没有获取到锁,就会进入阻塞,该锁升级为重量级锁.重量级锁会让其它申请的线程进入阻塞,性能降低.

### 自旋锁
自旋锁指尝试获取锁的进程不会立即阻塞,而是采用循环的方式去尝试获取锁,好处是减少线程上下文切换的消耗,坏处是会消耗CPU.

## 锁的优化
1. 减少锁持有时间
2. 减小锁粒度
3. 读写分离锁代替独占锁: 适合读多写少的情况
4. 锁分离: 像LinkedBlockingQueue, 把take()与put()分离
5. 锁粗化: 主要避免循环内反复申请锁

## 死锁

## java里线程为什么很'贵'?
1. 线程的创建与销毁成本很高
2. 线程本身占用较大内存,在java里一个线程至少会分配512k~1M的空间
3. 线程的切换成本高,切换时需要保存线程上下文,然后执行系统调用.如果线程过多,甚至可能导致切换时间大于线程执行时间.

## 常用线程池
1. `Executors.newSingleThreadExecutor()`: 只有一个线程的线程池
2. `Executors.newFixedThreadPool`: 固定大小的线程池
3. `Executors.newCachedThreadPool()`: 无限大小的线程池

## io流
1. `字符流`: 类名一般以`reader`或`writer`结尾
2. `字节流`: 类名一般以`stream`结尾
3. `转换流`: 将字节流转换为字符流
    * `InputStreamReader`
    * `OutputStreamWriter`

## 自省机制
todo

## 设计模式
todo

## mysql锁
### 类型
1. `共享锁(Shared Lock, 也叫S锁,读锁)`: 多个事务可以同时为一个对象加共享锁
2. `排他锁(Exclusive Lock, 也叫X锁,写锁)`: 如果一个事务给对象加了排他锁,其它事务就不能加任何锁了

### 粒度/级别
1. `表级`: 开销小, 加锁快;不会出现死锁;锁定粒度大,发生锁冲突的概率最高,并发度最低.
2. `页级`: 开销和加锁时间界于表锁和行锁之间;会出现死锁;锁定粒度界于表锁和行锁之间,并发度一般.
3. `行级`: 开销大, 加锁慢;会出现死锁;锁定粒度最小,发生锁冲突的概率最低,并发度也最高.

mysql显著的特点是不同的存储引擎支持不同的锁机制。
比如，MyISAM和MEMORY存储引擎采用的是表级锁（table-level locking）；
BDB存储引擎采用的是页面锁（page-level locking），但也支持表级锁；
InnoDB存储引擎既支持行级锁（row-level locking），也支持表级锁，但默认情况下是采用行级锁。

## 分库分表
