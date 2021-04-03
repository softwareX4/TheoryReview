[TOC]
## C++
### 值传递、指针传递和引用传递
- 值传递相当于实际参数的副本，在函数内部修改其值不会影响主调函数的值。
- 指针传递是将实参的地址传入，通过修改指针指向的地址的值来改变实参的值。指针本身占用主存空间，指针的指向可以改变。
- 引用传递即传递实参的别名，操作形参就是操作实参。
```c
 int x=1;
 int *y=&x; //用于指针传递，y有自己独立的内存地址，存储的内容是x的地址，*y是x的值
 int &z=x; //用于引用传递，可以理解为z就是x，x就是z，只不过名字不一样
```
> [值传递和引用传递-----函数参数传递的两种方式](https://www.cnblogs.com/codingmengmeng/p/5865510.html)


## JAVA
### 对象序列化
对象的复用。保存在内存中的各种对象的状态，并且可以把保存的对象状态再读出来。
- 使用情况
  - 当你想把的内存中的对象保存到一个文件中或者数据库中时候；
  - 当你想用序列化在网络上传送对象的时候；
  - 当你想通过RMI传输对象的时候；
#### 接口Serializable
  - 是一个标志，没有任何方法，以二进制的方式有限的持久化对象，不会调用任何的构造器
  >空接口如何实现序列化？
  事实上它只是一个标志，真正的序列化工作不需要它完成，而是在ObjectOutputStream的writeObject0()会判断是否为Serializable的实例
  ![](.img/instance.jpg)
   - 在恢复序列化一个对象的时候要保证它的CLASSPATH下有**其类的.class文件**，否则会抛出ClassNotFound
   - 如果仅仅只是让某个类实现Serializable接口，而没有其它任何处理的话，则就是使用默认序列化机制。使用默认机制，在序列化对象时，**不仅会序列化当前对象本身，还会对该对象引用的其它对象也进行序列化**，同样地，这些其它对象引用的另外对象也将被序列化，以此类推。所以，如果一个对象包含的成员变量是容器类对象，而这些容器所含有的元素也是容器类对象，那么这个序列化的过程就会较复杂，开销也较大。

  - 可以为成员添加**transient**关键字，不让Serializable自动序列化，如private String tansient password
  > **static修饰的也不会被序列化**
  - 添加writeObject()方法与readObject()方法(private)，则会优先调用。（不是通过覆盖或者重写机制，而是利用**反射**机制）
  
> 序列化一个对象:
> 1. 创建OutputStream 对象，封装到 ObjectOutputStream 对象内。
> 2. 此时，调用writeObject()，并将其发送给OutputStream。
> 相反的过程是将一个InputStream 封装到 ObjectInputStream 内，然后调用 readObject()。

#### Externalizable替代接口Serializable
  - 继承于Serializable，控制具体的序列化过程，**不会自动序列化**，序列化的细节需要由程序员去完成。
  - 当读取对象时，会调用被序列化类的无参构造器去创建一个新的对象，然后再将被保存对象的字段的值分别填充到新对象中。由于这个原因，实现Externalizable接口的类必须要提供一个**无参的构造器**，且它的**访问权限为public**。

![](.img/Serializable.png)

#### serialVersionUID
1. serialVersionUID是序列化前后的唯一标识符
2. 默认如果没有人为显式定义过serialVersionUID，那编译器会为它自动声明一个
3. **一旦像更改了类的结构或者信息，则类的serialVersionUID也会跟着变化**
serialVersionUID序列化ID，可以看成是序列化和反序列化过程中的“暗号”，在反序列化时，JVM会把字节流中的序列号ID和被序列化类中的序列号ID做比对，只有两者一致，才能重新反序列化，否则就会报异常来终止反序列化的过程。 

####  为什么要序列化？
  - 序列化的原本意图是希望对一个Java对象作一下“变换”，变成字节序列，这样一来方便持久化存储到磁盘，避免程序运行结束后对象就从内存里消失
  - 变换成字节序列也更便于网络运输和传播

#### 序列化可能会破坏单例模式

```java
public class Singleton implements Serializable {

    private static final long serialVersionUID = -1576643344804979563L;

    private Singleton() {
    }

    private static class SingletonHolder {
        private static final Singleton singleton = new Singleton();
    }

    public static synchronized Singleton getSingleton() {
        return SingletonHolder.singleton;
    }
}



public class Test2 {

    public static void main(String[] args) throws IOException, ClassNotFoundException {

        ObjectOutputStream objectOutputStream =
                new ObjectOutputStream(
                    new FileOutputStream( new File("singleton.txt") )
                );
        // 将单例对象先序列化到文本文件singleton.txt中
        objectOutputStream.writeObject( Singleton.getSingleton() );
        objectOutputStream.close();

        ObjectInputStream objectInputStream =
                new ObjectInputStream(
                    new FileInputStream( new File("singleton.txt") )
                );
        // 将文本文件singleton.txt中的对象反序列化为singleton1
        Singleton singleton1 = (Singleton) objectInputStream.readObject();
        objectInputStream.close();

        Singleton singleton2 = Singleton.getSingleton();

        // 运行结果竟打印 false ！
        System.out.println( singleton1 == singleton2 );
    }

}
```
运行后我们发现：反序列化后的单例对象和原单例对象并**不相等**了。
解决办法是：在单例类中**手写readResolve()函数**，直接返回单例对象：
```java
private Object readResolve() {
    return SingletonHolder.singleton;
}
```

### final关键字
- 数据
一旦被赋值，就不可改变它的值。
  - 基本类型：数值不变
  - 对象引用：引用恒定不变
  - 参数：不能更改参数引用指向的对象
  主要用于向匿名内部类传递数据
- 方法
不可被覆盖
  - 在继承中使方法的行为不变
  - 效率
  在java**早期实现**中，如果将一个方法指明为final，就是同意编译器将针对该方法的调用都转化为内嵌调用。如果是内嵌调用，虚拟机不再执行正常的方法调用（参数压栈，跳转到方法处执行，再调回，处理栈参数，处理返回值），而是**直接将方法展开**，以方法体重的实际代码替代原来的方法调用。这样减少了方法调用的开销。（现在由JVM决定是否内联）
  private隐式上就是final
- 类
不可被继承

### 继承与初始化
- 类的初始化在初次使用时加载。即创建第一个对象时，或者static初始化时。
- static对象及static代码段在加载时依赖书写顺序。
- 初始化顺序：
A extends B
加载B类，加载A类，B的static初始化，A的static初始化。创建对象：B的构造器调用，实例变量按次序初始化。执行构造器的其余部分。A的构造器调用，...。

### 内部类
每个内部类都能独立的继承自一个接口的实现，无论外围类是否已经继承 。
- 闭包
通过**接口+内部类**实现，广泛用于**回调**函数中。
  - 在JAVA中，内部类可以访问到外围类的变量、方法或者其它内部类等所有成员，即使它被定义成private了，但是外部类不能访问内部类中的变量。这样通过内部类就可以提供一种**代码隐藏和代码组织**的机制，并且这些被组织的代码段还可以自由地访问到包含该内部类的外围上下文环境。
  ![](.img/callback.png)
  - 回调的核心就是：回调方将本身即this传递给调用方.这样调用方就可以在调用完毕之后告诉回调方它想要知道的信息。
![](.img/innerClass.png)
![](.img/innerClass2.png)
即通过Caller调用传入对象的increment方法时，实际上传入了匿名内部类对象，而该内部类实际上调用了外围类的increment方法。
此外，Callee2类通过重写调用了increment方法，而如果它必须实现Incrementable接口，就**必须存在两个同名方法，而作用表现不同。** 它本身的increment方法并不是实现Incrementable。此时可以通过内部类来实现Incrementable。并且无需修改给外围类添加东西，也不需要修改接口。
> 内部类Closure返回了一个Callee2的钩子（而非引用），是很安全的，**外部只能调用Callee2的increment方法**，而不能获取到其他信息。

闭包、框架

### 容器
在编写代码的时候并不知道会产生多少对象，因而数组无法存放。
Collection是所有序列容器类的根接口，容器之间的共性通过迭代器实现，即实现Collection的类需要实现Iterator。
![](.img/collection.png)
#### List
- ArrayList
随机访问快，插入删除慢
- LinkedList
插入删除快，随机访问慢
- Set
通过值确定归属性
  - HashSet
  散列函数
  - TreeSet
  红黑树
  - LinkedHashSet
### 注解
三种标准注解（@Override、@Deprecated、@SuppressWarnings）和四种元注解
![](.img/innotion.png)

使用注解根据数据库表生成JavaBean
### 并发
#### 多线程
##### 实现方式
- **Runnable接口**
实现**Runnable接口**并实现run()方法
- **Thread类**
把Runnable对象传入**Thread类**，通过调用start方法启动
- **Executor**
在客户端和任务之间提供一个间接层，无须显示管理线程生命周期

  ```java
  ExecutorService exec = Executors.newCachedThreadPool();
  for(int i = 0; i < 5; ++i){
    exec.execute(new Liftoff()); //Liftoff实现了Runnable接口
  }
  exec.shutdown();
  ```
  - CachedThreadPool为每个任务创建一个线程
  - FixedThreadPool预先分配线程数量，可能时复用
  - SingleThreadExecuto线程数量为1，如果提交多个任务，则排队。并且按提交顺序完成。 
  
- **Callable接口**
希望任务在完成时有返回值。类型参数表示的是从call()方法中返回的值，必须使用ExecutorService.submit()方法调用。submit()方法返回Future对象，对Callable返回结果的特定类型进行了从参数化，可以用isDone()查询Future是否完成，用get()获取结果（若未完成则阻塞）。
![](.img/callable.png)
![](.img/callable2.png)

##### Join方法
A调用B.join()，A被挂起直到B结束

#### ThreadLocl
在线程内部维护其自己的变量，别的线程不可见。
比如Spring在为保证所有对数据库的CRUD都在一个数据库连接上进行，会设置为ThreadLocl。

### 锁


