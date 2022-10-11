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