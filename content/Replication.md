# 4. 复制

复制问题是分布式系统许多问题中的一个。我选择比其他问题比如领导选举，错误发现，互斥，一致性问题和全局快照更着重看待这个问题的原因是这通常是大部分人们最关系的问题。例如，判断并行数据库不同的一个依据就是它们复制功能的不同。并且，复制会带来了更多相关的子问题，例如领导选举，错误发现，一致性问题和原子广播。

复制是一组通讯问题。哪种组织和通信模式给我们带来了我们渴望的性能和可靠的性质？在面对网络隔离和同时多个节点故障的时候我们如何来确保容灾，持续运行和不产生不一致的情况呢？

再说一遍，现在有很多复制的方法。在这里我将会关注对一个系统的复制来说是高层级的模式。

![image-20200301133507505](../images/image-20200301133507505.png)

组织和通讯模式可以被拆分成这一些阶段：

1. （请求）客户端发送请求给服务端
2. （同步）同步模块把副本落好位置
3. （响应）返回一个想要给客户端
4. （异步）异步模块把副本落好位置

这个模型是基于[这个文章](https://www.google.com/search?q=understanding+replication+in+databases+and+distributed+systems)的一个简易版。注意在每个模块消息传递模式是依赖于特定的算法：我会有意的避免去讨论这个特点的算法。

有了这些阶段，我们可以创建什么类型的通讯模式啦？已经哪个是我们要选择的能实现高性能和高可用的模式？

## 同步模式

第一个模式是同步模式（也可以被称为活跃的，或者热切的，或者推，或者悲观的复制）。让我们把样子画出来：
![image-20200301134627004](../images/image-20200301134627004.png)

这里我们可以看到三个不同的阶段：首先是客户端发送请求，然后，同步模块把副本落好位置。在这同时客户端是被阻塞的 - 等待系统的一个响应。

在同步时期，第一个服务器接触到了另外两个服务器并且等待它收到了这些服务器的响应。最后，它发送一个响应给客户端通知结果（比如成功或者失败）。

所有的这一切看上去都是简洁明了的。那关于这种组织和通讯模式，除了讨论同步时期的算法细节之外我们还能说些什么呢？首先，我们看到这是一个N到N的写方法：在一个响应返回之前，它需要系统中所有的服务器看到并且认可。

从性能的角度来看，这意味着这个系统会由最慢速度的服务器来决定它的速度。系统会对网络延迟问题变得很敏感，一位它需要所有的服务器都返回后才能运行。

## 深入阅读

### Primary-backup and 2PC

- [Replication techniques for availablity](http://scholar.google.com/scholar?q=Replication+techniques+for+availability) - Robbert van Renesse & Rachid Guerraoui, 2010
- [Concurrency Control and Recovery in Database Systems](http://research.microsoft.com/en-us/people/philbe/ccontrol.aspx)

### Paxos

- [The Part-Time Parliament](http://research.microsoft.com/users/lamport/pubs/lamport-paxos.pdf) - Leslie Lamport
- [Paxos Made Simple](http://research.microsoft.com/users/lamport/pubs/paxos-simple.pdf) - Leslie Lamport, 2001
- [Paxos Made Live - An Engineering Perspective](http://research.google.com/archive/paxos_made_live.html) - Chandra et al
- [Paxos Made Practical](http://scholar.google.com/scholar?q=Paxos+Made+Practical) - Mazieres, 2007
- [Revisiting the Paxos Algorithm](http://scholar.google.com/scholar?q=Paxos+Made+Practical) - Lynch et al
- [How to build a highly available system with consensus](http://research.microsoft.com/lampson/58-Consensus/Acrobat.pdf) - Butler Lampson
- [Reconfiguring a State Machine](http://research.microsoft.com/en-us/um/people/lamport/pubs/reconfiguration-tutorial.pdf) - Lamport et al - changing cluster membership
- [Implementing Fault-Tolerant Services Using the State Machine Approach a Tutorial](http://citeseer.ist.psu.edu/viewdoc/summary?doi=10.1.1.20.4762) - Fred Schneider

### Raft and ZAB

- [In Search of an Understandable Consensus Algorithm](https://ramcloud.stanford.edu/wiki/download/attachments/11370504/raft.pdf), Diego Ongaro, John Ousterhout, 2013
- [Raft Lecture - User Study](http://www.youtube.com/watch?v=YbZ3zDzDnrw)
- [A simple totally ordered broadcast protocol](http://research.yahoo.com/pub/3274) - Junqueira, Reed
- [Zookeeper Atomic Broadcast](http://research.yahoo.com/pub/3514)