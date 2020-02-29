# 3. 时间和顺序

顺序是什么？顺序为什么这么重要？

你想通过"顺序是什么"来说明什么?

我想说，为什么我们要把顺序放在首位？为什么我们要关系A是不是在B之前发生？为什么我们不关注其他属性，比如“颜色”呢？

好的，抓狂的朋友们，让我们回到分布式系统上来回答这些问题吧。

你们可能记得，我之前说到分布式编程是一门用多台机器来解决同样你可以在一台机器解决的问题的艺术。

事实上，痴迷于顺序的核心是，任何一个系统在一个时间只能做一件事情，因此对于一些操作来说必然会有一套完整的顺序。就像人们穿过一个单通道的门一样，每个穿过者都会有前任和继任。这就是我们努力保护的基本编程模型。

传统的模型是一个单通道程序，一个进程，一个内存空间运行在一个CPU上。操作系统将这个事实抽象成看上去有多个CPU运行者多个程序一样，内存也在多个进程之间共享者。我没说到多线程编程和事件驱动编程；这也是在“一/一/一（指上面一个进程，一个内存，一个CPU）模型上的一个特殊抽象。程序被写出来是在一定的顺序方式下执行：你从头开始执行，然后逐渐执行到底部。

顺序作为一种属性获取了如此多的关注是因为定义正确性的最简单的方式是说“它看上去旧手机在一台机器上工作”。更通俗的意思是a)我们运行着相同的步骤，b)我们在相同的顺序运行 - 即使是有多台机器。

分布式系统最好的事情是普遍的保持顺序（就像在单机上定义的那样）。你不需要去关心有哪些步骤，因为它们会像在单机上一样被正确的执行。这就会变得很棒因为你可以使用相同的系统而不需要关心是哪些步骤。

事实上，一个分布式程序是运行在多个节点，多个CPU和多个操作流进来。你仍然可以分配一个全局顺序，但是这需要使用精确的时钟或者相同的通讯格式。你可以用一个完全精确的时钟来给每个步骤打上时间戳然后再使用这个时间戳来排出全局顺序。或者在全局排序中你可以使用一些通讯系统来分配一系列的数字顺序。

## 全局排序和局部排序

一个分布式系统处在自然状态是一个[局部顺序](http://en.wikipedia.org/wiki/Partially_ordered_set)。不仅是网络还有系统上所有的独立节点都是通过相对顺序来做保证的；但是对于每一个节点本身来说，你看到的是一个本地的顺序。

一个[全局排序](http://en.wikipedia.org/wiki/Total_order)是一个用一些集合来定义每一个元素的顺序的二进制联系。

两个不同的元素之间存在着其中一个比另外一个大的这种比较关系。在一个局部排序集合中，一些元素对之间没有相互比较导致这个局部排序不能准确描述每一项的顺序。

不论全局排序还是局部排序都是可传递的以及对称的。下面描述a，b，c关系的语句对于全局排序和局部排序都适用：

```
If a ≤ b and b ≤ a then a = b（对称性）;
If a ≤ b and b ≤ c then a ≤ c（传递性）;
```

但是，一个全局排序是[全局性](http://en.wikipedia.org/wiki/Total_relation)：

```
a ≤ b or b ≤ a (全局性) for all a, b in X 
```

而一个局部排序只有[自反性](http://en.wikipedia.org/wiki/Reflexive_relation):

```
a ≤ a (自反性) for all a in X
```

注意有全局性同样也意味着有自反性；所以一个局部排序是一个全局排序的弱变体。对于在一个局部排序体系里的所有元素来说，是不包含全局性的 - 换句话说就是有些元素没法进行比较。

Git的分支就是一个局部排序的例子。正如你可能知道，git版本控制系统允许你从一个基础分支（比如master分支）拉出很多新分支。每个分支都能看到从同一个祖先继承下来的所有代码变更的历史记录。

```
[ branch A (1,2,0)]  [ master (3,0,0) ]  [ branch B (1,0,2) ]
[ branch A (1,1,0)]  [ master (2,0,0) ]  [ branch B (1,0,1) ]
                  \  [ master (1,0,0) ]  /
```

A分支和B分支都起源了同一个祖先，但是它们之间是没有确定的顺序的：它们呈现不同的历史并且如果没有做而外的工作（合并）它们不能再连成为一个独立的线性历史。当然，你可以用一个随意的顺序（比如，按照祖先优先然后再以A优先B或者B优先A的顺序打断串）来拉下所有的提交 - 但是这样会因为没有一个全局顺序而丢失掉一些信息。

在单节点系统中，一个全局顺序是必须要出现的：在一个单独的进程中指令的执行和消息的处理都是在一个确定的，可观测到的顺序中。我们可以完全依赖这个全局顺序 - 这样进程都是在预期下运行。同样这个顺序可以在分布式系统中维护着，但是作为代价：通讯是昂贵的，时间的同步也是困难和不稳定的。

## 什么是时间？

时间是顺序的源 - 时间允许我们来定义操作的顺序 - 巧合的是时间还是人们都可以理解的表达方式（一秒，一分，一天等等）。

在一些场景中，时间仅仅跟其他整型计数器一样。他之所以如此重要的原因仅仅是刚好大部分电脑都自带了时间传感器，也就是我们的说的时钟。

时间戳确实是一个简单的数值可以用来表述从宇宙开始到当前时刻的状态 - 如果一些事是在特定的时间戳发生，那么它们会受之前发生的每一件事情的影响。



假定每个地方的时间进程都是相同的速率 - 这是我在这一瞬间返回的一个大假设 - 在一个程序中使用的时间和时间戳都有一些有用的说明。这三个说明是：

- 顺序
- 间隔
- 描述

*顺序*，我之前说到时间是顺序的源，这个意思是：

- 我们可以将无序的事件绑上时间戳来对它们排序
- 我们可以使用时间戳来加强一个特定的操作或者传说信息的顺序（例如，如果操作是乱序到达的，就可以延迟这个操作）
- 我们可以使用时间戳的值来检查一些事件是不是比另一些事件提前发生

*说明* - 时间是一个普遍的比较数组。可以用日期这个时间戳来表述绝对值，这对人们都很有帮助。但出现故障时在日志文件里打上一个时间戳，这样你可以说是在上周六发生一个雷击。

*间隔* - 间隔能及时地描述的是在真实世界中存在的相互联系。算法一般不关心时间的绝对值或者日期值，但是它们会用间隔来做一些正确性的调用。特别的，等待花费的时间总量可以提供判断系统是否被隔离了或者发生很大延迟的线索。



在之后的两章中我们会检查复制，用于容灾的强一致性系统 - 这系统会提供强保证成为对错误产生有强烈的弹性。这些系统提供了第一个例子的解决方案：如果你需要正确性保证并且愿意为此而花费。然后，我们会讨论弱一致性，系统在面对分片同时可以保证可用性，但是只能给你一个能达到的最好的结果。

## 深入阅读

### Lamport clocks，vector clocks

- [Time, Clocks and Ordering of Events in a Distributed System](http://research.microsoft.com/users/lamport/pubs/time-clocks.pdf) - Leslie Lamport, 1978

### Failure detection

- [Unreliable failure detectors and reliable distributed systems](http://scholar.google.com/scholar?q=Unreliable+Failure+Detectors+for+Reliable+Distributed+Systems) - Chandra ans Toueg
- [Latency- and Bandwidth-Minimizing Optimal Failure Detectors](http://www.cs.cornell.edu/people/egs/sqrt-s/doc/TR2006-2025.pdf) - So & Sirer, 2007
- [The failure detector abstraction](http://scholar.google.com/scholar?q=The+failure+detector+abstraction), Freiling, Guerraoui & Kuznetsov, 2011

### Snapshots

- [Consistent global states of distributed systems: Fundamental concepts and mechanisms](http://scholar.google.com/scholar?q=Consistent+global+states+of+distributed+systems%3A+Fundamental+concepts+and+mechanisms), Ozalp Babaogly and Keith Marzullo, 1993
- [Distributed snapshots: Determining global states of distributed systems](http://scholar.google.com/scholar?q=Distributed+snapshots%3A+Determining+global+states+of+distributed+systems), K. Mani Chandy and Leslie Lamport, 1985

Causality

- [Detecting Causal Relationships in Distributed Computations: In Search of the Holy Grail](http://www.vs.inf.ethz.ch/publ/papers/holygrail.pdf) - Schwarz & Mattern, 1994
- [Understanding the Limitations of Causally and Totally Ordered Communication](http://scholar.google.com/scholar?q=Understanding+the+limitations+of+causally+and+totally+ordered+communication) - Cheriton & Skeen, 1993