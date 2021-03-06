# 《Concurrency in Go》
-----

> 并发和并行的区别傻傻分不清？第二章从大众最常见的误区：并发和并行的区别，进行切入。从而牵扯出了对于并发代码的建模问题。从CSP入手讲解了go语言的好处和golang的并发哲学。

-----
## Chapter 2：Modeling Your Code: Communicating Sequential Processes 对你的代码建模：通信顺序进程（CSP）
-----
### 1 Concurrency and Parallelism 并发与并行
### 1.1 The Difference Between Concurrency and Parallelism 并发与并行的区别
 
并发和并行，对于普通人而言总是忽略二者之间的区别，经常被混用于表达“某种和其他事物同时运行的事务”。但是这二者的区别对于一个开发者而言至关重要：最起码在讨论代码的时候你应该使用并发这个词。

**并发和并行的区别在你对代码进行建模的时候是一个非常强力的抽象。**

首先从一个简单的陈述句，我们来看清其中的区别：

> 并发属于代码，而并行属于一个运行中的程序。

直观的感受上人们很容易忽略程序真正运行时的情况，这都是因为高级程序设计语言为我们封装了很多，以至于我们只去考虑我们的代码是否是并发的。而并非去考虑我写出的程序是否真正是并行执行的。

一个人写出并发的代码，但是这个并发的代码所构建的程序是否能够真正的并行执行却是一个问题：比如在只有一个处理器的时候。

我们在第一章中说出过多核处理器的由来。但是当只有一个核心的时候我们的并发代码还能够正常的执行吗？

当然可以！我们的代码像我们想象的一样执行了起来，而且看上去比起串行确实有效率的提升。这是因为多个线程在复用同一个处理器，这一个处理器把自己的时间周期分了又分，好让每一个线程都有计算的时间。**但是事实上这些计算过程不是同时执行的！**

在这里你大概能体会到一点并发和并行的区别了，那么趁热打铁再说几句话：

> 首先我们没有编写并行的代码，只有我们希望可以并行执行的并发代码。
> 另外，并行是我们的程序运行时的属性，而不是我们代码的属性。

还有一个非常有意思的事情是：**并行也是依赖上下文的。**

在第一章的时候我们提到了判断一个操作的原子性时需要依赖上下文，在判断并行的时候我们也应该依赖这种思想。现在不妨思考一个问题：为什么大多数人会搞混并发和并行？**就是因为在运行并发代码的时候他们也同时认为程序是在并行计算的！** 因为考虑问题的上下文不同。

编写并发代码层面的人，假设有两个分别消耗x和y时间的操作。如果被认为是并行执行的上下文大于等于x+y，那么就可以认为这两个操作是并行执行的。反之则认为不是的。因为可以把一个上下文看做一个单位时间，既然在一个单位时间里完成了两个操作，那么为什么不认为是并行执行的呢？

### 1.2 所带来的问题

由以上可以看到：进程是维持并发的关键。但是进程的出现又带来了很多的问题：对于资源的权限，进程之间的干扰等等。虽然在现有的操作系统和进程的边界能帮助我们减轻很大一部分考虑逻辑正确性的压力，但是对于进程的控制是必不可少的。

>  事实上随着我们抽象栈的深入，如何正确的对事物进行建模变得越来越让人难以理解，对我们来说越来越重要。换言之，编写正确的并发逻辑越来越难，越需要我们将很简单的并发原语组合起来使用。

在golang之前的语言，大部分的主流编程语言都有一系列的抽象层。然后写一大堆天花乱坠的代码来维护你需要调用的线程。但是golang不一样（golang简直就是小天使

golang提供了著名的**goroutine和channel**，这对于开发者而言简直就是救星：我们几乎不在操作系统的线程层面考虑我们的问题了，取而代之的是，我们对于事物站在goroutine和channel的层面来进行思考，偶尔站在共享内存的角度思考（这也就是golang的并发哲学）。

golang中的goroutine和channel从何而来呢？**源于1978年Charles Antony Richard Hoare撰写的一篇论文。**

-----

### 2 Communicating Sequential Processes，CSP

### 2.1 什么是CSP？
在许多和golang有关的讨论中，CSP经常被赞美成golang成功的原因以及并发编程的“万能钥匙”。但是对于CSP的具体细节却又不求甚解，这一小节就介绍了CSP的相关知识。

**Communicating Sequential Processes，CSP。中文是通信顺序进程。是由Antony Hoare在1978年提出的概念。**

在1978年，大部分关于如何架构程序的研究都是针对编写顺序代码的方法：goto语句的使用正在被讨论，面向对象范型正在成为编程的基石，而并发并没有被给予充分的思考。而霍尔同志这个时候意识到要纠正这个现象，于是在以CSP为题的论文中提出了CSP的概念。

Hoare认为输入与输出是两个被忽略的编程原语，尤其是在并发的代码中。于是在论文中提出了使用进程间通信来使对于线程的操作以顺序形式编写的语言：CSP，这个时候还只是一个存在于论文中的编程语言。

Hoare的CSP语言中包含了用于建模输入和输出的原语。并且Hoare开创性地将进程这个概念运用到任何包含**需要输入来运行**且**产生**其他进程**将会消费的输出**的**逻辑片段**。

CSP的原语在这里不再一一赘述，但是这和golang中的channel的相似性是显而易见的（也太相似了吧！）。为了能够使进程之间安全的通信，Hoare花了很大的力气来优化他的输入输出原语。

在这之后，Hoare花了六年的时间来提炼CSP的想法为“进程微积分”的东西。进程微积分是一种对于并发系统进行数学化建模的方式，并且提供了代数法则来进行这些系统的变换来分析它们不同的属性。

**经过经验的判断，Hoare对于并发的建议显然是正确的，但是在golang发布之前，很少有语言能够真正的为这些原语提供支持。golang是最早将CSP的原则纳入其核心的语言之一，并将这种编程风格引入到大众中。它的成功也使得其他语言尝试添加这些原语。**


-----
### 3 How This Helps You 这将如何帮助你

在之前的讨论中，我们发现了一件事情：golang的并发风格和其他语言为何有如此大的不同？

通常来说，一种语言会将他们的抽象链结束在系统线程和内存访问同步的层级。golang则采用了一个不同的路线，并且使用goroutine和channel来代替这些概念。

goroutine把我们从必须按照并行的方式思考中解放出来，作为替代，它允许我们以更加自然的等级对问题进行建模。如果对于一个问题，我们需要对这个并行问题进行建模。如果使用以前的方式进行思考，你会考虑以下问题：

+ 我的语言原生支持线程吗？还是我需要导入一个新的库？
+ 我的进程限制边界应该是什么？
+ 线程在操作系统中的重量级有多高？
+ 我的程序需要运行的操作系统处理这些线程的时候有什么不同？
+ 我需要创建一个工作线程池来承载创建的线程，该如何找到最佳的线程池大小？

**这太恶心了，而且你思考了半天却很可能和你原本的问题一点边都不搭！！！！**

我们对于原本问题的抽象的过程由于这些编程语言中并发原语的缺失而变得困难起来。但是这个世界上无数的优秀的程序员的不懈努力使得即使是如此的复杂还是可以开发许多并发的代码。但是当一种更为自然且简单的方式出现的时候我们会怎么想呢？

**golang中的goroutine和channel让我们对于问题进行的思考自然的直接映射为如何使用golang进行编程。**

golang给我们的性能的保证让我们能够放心大胆的使用goroutine：goroutine是很轻量级的，我们通常情况下并不需要为创建新的goroutine所带来的代价而担心。

建立在抽象并行问题的复杂性的基础之上，几乎每一种语言都有专门的框架来支持开发者们简单的进行开发。但是毕竟是未能从根本上使得考虑问题的方式变得更加的自然和合理。
别人撰写的框架时所遇到的复杂问题并未被隐藏，它依然存在，而这种复杂性的累加是滋生bug的温床。

**显然对于问题更加自然和合理的抽象更好，对吧？**

对于一个问题能够更加自然的进行抽象能让你提高将问题建模为并发方式的比例提高了，能够在更细的颗粒度上编写并发代码，从而提高程序的运行效率。

不仅仅是goroutine，channel和select语句等等也是golang诸多并发拼图中的一块，以后会详细讲解各种用法。

-----

### 4 Go’s Philosophy on Concurrency Go语言的并发哲学

> CSP 一直都是golang设计的重要组成部分

但是如果你想要玩儿内存访问同步，其实也ok，golang中的sync而已满足你的需求。虽然有很多选择对于开发者来说很棒。但是对于golang的初学者却总是抱有一个疑惑：为什么还有支持内存访问同步？使用CSP样式编写程序不应该是golang编写并发代码的唯一方式吗？

在sync包中这样写到：

> sync包提供了基本的同步单元，比如互斥锁。除了Once类型和WaitGroup类型以外，大部分都是适用于低水平程序线程，对于高水平的同步，使用channel通信会更好一些。

![在这里插入图片描述](https://img-blog.csdnimg.cn/20190522173525333.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1NoaWluYV9PcmV6,size_16,color_FFFFFF,t_70)
而在Go语言的FAQ中，也有如下陈述：

> 为了尊重mutex，sync包实现了mutex，但是我们希望Go语言的编程风格将会激励人们尝试更高等级的技巧。尤其是如何去构建你的程序，以便一次只有一个goroutine负责某个特定的数据。
> 不要通过共享内存来进行通信。相反，通过通信来共享内存。有数不清的关于Go语言核心团队的文章，讲座和访谈，相对于使用像sync.Mutex这样的原语，他们更加拥护CSP。

那么现在你就懵逼了，既然不推荐使用为什么还要公开？直接都用channel不ok吗？？？

> ”使用最好描述和最简单的那个方式。“

何时应该使用锁，而何时又应该使用channel。对于不同的问题会有不同的答案。而如何判断呢？书中给出了一个决策树。

![在这里插入图片描述](https://img-blog.csdnimg.cn/20190522174431820.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1NoaWluYV9PcmV6,size_16,color_FFFFFF,t_70)

Go语言的并发哲学可以这样总结：

> 追求简洁，尽可能使用channel，并且认为goroutine的使用是没有成本的。
