### 静态绑定和动态绑定的区别

一个Java 程序要经过编写、编译、运行三个步骤，其中编写代码不在我们讨论的范围之内，那么我们的重点自然就放在了`编译` 和 `运行`这两个阶段，由于编译和运行阶段过程相当繁琐，下面就我的理解来进行解释：

Java 程序从源文件创建到程序运行要经过两大步骤：

1、编译时期是由编译器将源文件编译成字节码的过程

2、字节码文件由Java虚拟机解释执行

#### 绑定

绑定就是一个方法的调用与调用这个方法的类连接在一起的过程被称为绑定。

绑定主要分为两种：

静态绑定 和 动态绑定

绑定的其他叫法

静态绑定  == 前期绑定 == 编译时绑定

动态绑定  == 后期绑定 == 运行时绑定

为了方便区分：下面统一称呼为静态绑定和动态绑定

#### 静态绑定

在程序运行前，也就是编译时期 JVM 就能够确定方法由谁调用，这种机制称为静态绑定

识别静态绑定的三个关键字以及各自的理解

如果一个方法由 private、static、final 任意一个关键字所修饰，那么这个方法是前期绑定的

构造方法也是前期绑定

private：private 关键字是私有的意思，如果被 private 修饰的方法是无法由本类之外的其他类所调用的，也就是本类所特有的方法，所以也就由编译器识别此方法是属于哪个类的

```java
public class Person {

    private String talk;

    private String canTalk(){
        return talk;
    }
}

class Animal{

    public static void main(String[] args) {
        Person p = new Person();
        // private 修饰的方法是Person类独有的，所以Animal类无法访问(动物本来就不能说话)
//        p.canTalk();
    }
}
```

final：final 修饰的方法不能被重写，但是可以由子类进行调用，如果将方法声明为 final 可以有效的关闭动态绑定

```java
public class Fruit {

    private String fruitName;

    final String eatingFruit(String name){
        System.out.println("eating " + name);
        return fruitName;
    }
}

class Apple extends Fruit{

      // 不能重写final方法，eatingFruit方法只属于Fruit类，Apple类无法调用
//    String eatingFruit(String name){
//        super.eatingFruit(name);
//    }

    String eatingApple(String name){
        return super.eatingFruit(name);
    }
}
```

static：static 修饰的方法比较特殊，不用通过 new 出某个类来调用，由`类名.变量名`直接调用该方法，这个就很关键了，new 很关键，也可以认为是开启多态的导火索，而由类名.变量名直接调用的话，此时的类名是确定的，并不会产生多态，如下代码：

```java
public class SuperClass {

    public static void sayHello(){
        
        System.out.println("由 superClass 说你好");
    }
}

public class SubClass extends SuperClass{

    public static void sayHello(){
        System.out.println("由 SubClass 说你好");
    }

    public static void main(String[] args) {
        SuperClass.sayHello();
        SubClass.sayHello();
    }
}
```

SubClass 继承 SuperClass 后，在

![Image](https://vue-admin-imgages.oss-cn-hangzhou.aliyuncs.com/2022-10-11/8072f35f-bf83-4278-8fca-de2d006c8342_interview-javabase-20.png)

是无法重写 sayHello 方法的，也就是说 sayHello() 方法是对子类隐藏的，但是你可以编写自己的 sayHello() 方法，也就是子类 SubClass 的sayHello() 方法，由此可见，方法由 static 关键词所修饰，也是编译时绑定

#### 动态绑定

在运行时根据具体对象的类型进行绑定

除了由 private、final、static 所修饰的方法和构造方法外，JVM 在运行期间决定方法由哪个对象调用的过程称为动态绑定

如果把编译、运行看成一条时间线的话，在运行前必须要进行程序的编译过程，那么在编译期进行的绑定是前期绑定，在程序运行了，发生的绑定就是后期绑定

```java
public class Father {

    void drinkMilk(){
        System.out.println("父亲喜欢喝牛奶");
    }
}

public class Son extends Father{

    @Override
    void drinkMilk() {
        System.out.println("儿子喜欢喝牛奶");
    }

    public static void main(String[] args) {
        Father son = new Son();
        son.drinkMilk();
    }
}
```

Son 类继承 Father 类，并重写了父类的 dringMilk() 方法，在输出结果得出的是儿子喜欢喝牛奶。那么上面的绑定方式是什么呢？

上面的绑定方式称之为`动态绑定`，因为在你编写 Father son = new Son() 的时候，编译器并不知道 son 对象真正引用的是谁，在程序运行时期才知道，这个 son 是一个 Father 类的对象，但是却指向了 Son 的引用，这种概念称之为多态，那么我们就能够整理出来多态的三个原则：

- 继承
- 重写
- 父类对象指向子类引用

也就是说，在 Father son = new Son() ，触发了动态绑定机制。

动态绑定的过程

1. 虚拟机提取对象的实际类型的方法表；
2. 虚拟机搜索方法签名；
3. 调用方法。

#### 动态绑定和静态绑定的特点

静态绑定

静态绑定在编译时期触发，那么它的主要特点是

1、编译期触发，能够提早知道代码错误

2、提高程序运行效率

动态绑定

1、使用动态绑定的前提条件能够提高代码的可用性，使代码更加灵活。

2、多态是设计模式的基础，能够降低耦合性。





### 强引用、若引用、虚引用和幻象引用的区别

我们说的不同的引用类型其实都是逻辑上的，而对于虚拟机来说，主要体现的是对象的不同的`可达性(reachable)` 状态和对`垃圾收集(garbage collector)`的影响。

可以通过下面的流程来对对象的生命周期做一个总结

![Image](https://vue-admin-imgages.oss-cn-hangzhou.aliyuncs.com/2022-10-11/11217992-ca68-4a3c-8ef9-b8031882583c_interview-javabase-17.png)

对象被创建并初始化，对象在运行时被使用，然后离开对象的作用域，对象会变成不可达并会被垃圾收集器回收。图中用红色标明的区域表示对象处于强可达阶段。

JDK1.2 介绍了 `java.lang.ref` 包，对象的生命周期有四个阶段：`􏲧强可达􏰛(Strongly Reachable􏰜)`、`软可达(Soft Reachable􏰜)`、`弱可达(Weak Reachable􏰜)`、 `幻象可达(Phantom Reachable􏰜)`。



如果只讨论符合垃圾回收条件的对象，那么只有三种：软可达、弱可达和幻象可达。

- 软可达：软可达就是􏱬我们只能通过软引用􏳂才能访问的状态，软可达的对象是由 `SoftReference` 引用的对象，并且没有强引用的对象。软引用是用来描述一些还有用但是非必须的对象。垃圾收集器会尽可能长时间的保留软引用的对象，但是会在发生 `OutOfMemoryError` 之前，回收软引用的对象。如果回收完软引用的对象，内存还是不够分配的话，就会直接抛出 OutOfMemoryError。
- 弱可达：弱可达的对象是 `WeakReference` 引用的对象。垃圾收集器可以随时收集弱引用的对象，不会尝试保留软引用的对象。
- 幻象可达：幻象可达是由 `PhantomReference` 引用的对象，幻象可达就是没有强、软、弱引用进行关联，并且已经被 finalize 过了，只有幻象引用指向这个对象的时候。

除此之外，还有强可达和不可达的两种可达性判断条件

- 强可达：就是一个对象刚被创建、初始化、使用中的对象都是处于强可达的状态
- `不可达(unreachable)`：处于不可达的对象就意味着对象可以被清除了。

下面是一个不同可达性状态的转换图

![Image](https://vue-admin-imgages.oss-cn-hangzhou.aliyuncs.com/2022-10-11/2811c535-f172-4aa7-8957-bdcf8baf40da_interview-javabase-19.png)

判断可达性条件，也是 JVM 垃圾收集器决定如何处理对象的一部分考虑因素。

所有的对象可达性引用都是 `java.lang.ref.Reference` 的子类，它里面有一个`get()`方法，返回引用对象。如果已通过程序或垃圾收集器清除了此引用对象，则此方法返回 null 。也就是说，除了幻象引用外，软引用和弱引用都是可以得到对象的。而且这些对象可以人为`拯救`，变为强引用，例如把 this 关键字赋值给对象，只要重新和引用链上的任意一个对象建立关联即可。