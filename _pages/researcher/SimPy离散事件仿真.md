---
date: 2023.08.20
---

> 最近需要使用离散事件仿真技术完成论文的实验部分，因此接触了这个领域的工具SimPy，这两天用起来的感觉确实可以实现仿真的整个过程。首先放上[官方文档](https://simpy.readthedocs.io/en/latest/)谨供参考，其次如果对仿真实例感兴趣可以看[我的代码](https://github.com/yuyalun-allen/ttl_discrete_sim)

# 核心概念
SimPy基于离散事件仿真的两个核心概念建立，环境和事件。

## Environment
环境是所有事件发生的容器，我理解中环境最重要的作用是提供不同事件间统一的标准，如**时间**。

## Process
事件是仿真的主体，仿真由环境中不同事件及其交互构成。事件可以创建另一些事件，也可以中断其他事件，或者通过共享资源影响彼此的行为。

## 另一个概念：Resource
资源是离散事件仿真中常用的概念，事件可以取得资源或者释放资源。虽然并不是必须，但SimPy提供了这一概念的抽象，让仿真框架的功能大大拓展。

# SimPy
以上三个概念可以分别通过以下类的构造函数创建：

``` python
env = simpy.Environment()
simpy.Resource(env, capacity)
env.process(my_process(env))
```

然后通过run方法开始仿真：
```python
env.run(until=TIME_LIMIT)
```

## 如何定义一个事件？
SimPy灵活地利用Python的特性进行各种概念和功能的设计。

从python程序的视角来看，env是主线程，process是子线程，而子线程是由python的generator实现。只需要把一个generator传递给`env.process()`，即可定义一个事件。

> Tip:向不熟悉python的同学介绍一下generator，python中的generator是一种特殊的函数，每调用一次generator，它都会通过`yield`返回值。在不同调用之间，generator内部的状态会被保留。

> 这就让generator有了模仿线程的能力，想象一下，generator中有一个永不`return`的while死循环，但是可以通过`yield`返回值，并可以在while语句前，初始化**线程的local变量**。

在我理解中有两种定义事件的场景，分别是**串行**和**并行**。根据对generator的理解，要想创建串行事件，就必须在`yield`语句中定义；对应地，想要创建并行事件，则不可以在yield语句中定义。*可以理解吗* ;-)

## 如何中断事件？
SimPy使用python的异常机制实现中断，换言之：

A为被中断事件，B为中断事件。需要在B中调用A的`interrupt()`方法，A需要捕获`Interrupt`异常，并做异常处理。

*示例代码看文档即可，此处不赘述*

## 资源又像什么呢？
我的朋友，你看资源是否就像操作系统中的信号量呢？是不是很好理解？

*不过目前在项目中还没有使用，等用到再来补充*

To Be Continue ...
---