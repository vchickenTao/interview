### Java 提供了哪些 I/O 方式

Java I/O 方式有很多种，传统的 I/O 也称为 `BIO`，主要流有如下几种

![Image](https://vue-admin-imgages.oss-cn-hangzhou.aliyuncs.com/2022-10-11/0e9b0191-e652-477e-8dad-6328e8b8a608_interview-javabase-15.png)

Java I/O 包的实现比较简单，但是容易出现性能瓶颈，传统的 I/O 是基于同步阻塞的。

JDK 1.4 之后提供了 `NIO`，也就是位于 `java.nio` 包下，提供了基于 channel、Selector、Buffer的抽象，可以构建多路复用、同步非阻塞 I/O 程序。

JDK 1.7 之后对 NIO 进行了进一步改进，引入了 `异步非阻塞` 的方式，也被称为 `AIO(Asynchronous IO)`。可以用生活中的例子来说明：项目经理交给手下员工去改一个 bug，那么项目经理不会一直等待员工解决 bug，他肯定在员工解决 bug 的期间给其他手下分配 bug 或者做其他事情，员工解决完 bug 之后再告诉项目经理 bug 解决完了。