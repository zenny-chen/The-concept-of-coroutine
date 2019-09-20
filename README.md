# The concept of coroutine
协程（coroutine）概念

<br />

协程（***Coroutine***）是计算机程序的组成部分，它归纳了用于非抢占式多任务的子例程，以允许执行被延迟跟恢复。协程非常适合用于这些程序部件：相互协作性的任务，异常处理，事件循环（或消息循环），迭代器，无穷列表和管道。

根据Donald Knuth所提到的，Melvin Conway在1958年所创造出的术语***协程***，他将协程应用到了构建一个汇编程序。在1963年，第一本对协程解释的书出版了。

### 相互协作型多任务（Cooperative Multitasking）

相互协作型多任务也称为**非抢占式多任务**。它是一种计算机多任务的风格，在此风格下，操作系统不会对一个正在运行的进程进行上下文切换而调度到另一个进程。取而代之的是，进程自愿地周期性地释放控制，或是当它处于空闲状态时，或是在逻辑上被阻塞时，为了允许多个应用能并发运行。这种类型的多任务被称为“相互协作型的”是因为所有的程序必须为整个调度模式进行相互协作才能正常工作。在这种模式下，一个操作系统的进程调度器被称为**相互协作的调度器**，将其功能降低到只是启动进程，并让这些进程将控制自愿地返还给它。

### 与子例程的对比

子例程是协程的特殊情况。当子例程被调用时，执行从起始处开始，而一旦一个子例程退出时，它就结束了；一个子例程的一个实例仅返回一次，而在两次调用之间不会维护状态。相比之下，协程可以通过调用其他协程退出，然后又可以在此之后返回到在原始协程中它们被调用时的那一点；从协程的视角来看，它并没有退出，而是调用了其他协程。因而，一个协程实例会维护其状态，并且在两次调用之间会有所不同；对于一个给定的协程，一次可能会有多个实例。通过“释放（***yield***）”当前协程而调用另一个协程与简单地调用另一个例程之间的区别是，两个协程之间的相互关系，它们相互释放，不是调用者-被调用者的关系，而是对称关系。

任一子例程可以被翻译为不调用“释放”的协程。

这里有一个例子描述了协程有用之处。假定你有一个生产者-消费者关系，一个协程创建了项目并将它们添加到队列中，另一个协程从队列移除项目并使用它们。出于高效原因，你想一次添加和移除多个项目。代码会类似于如下所示：
```csharp
var q := new queue

coroutine produce
    loop
        while q is not full
            create some new items
            add the items to q
        yield to consume

coroutine consume
    loop
        while q is not empty
            remove some items from q
            use the items
        yield to produce
```

该队列然后在将控制通过使用 ***yield*** 命令释放给其他协程之前被完全填充或清空。对协程的后续进一步地调用正好从 ***yield*** 之后的位置开始，在协程的外部循环中。
尽管这个例子经常用作为对多线程的介绍，不过对于两个线程而言并不需要这样：***yield*** 语句可以通过一个跳转直接从一个例程跳转到另一个。

