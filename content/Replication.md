# 4. 复制

复制问题是分布式系统许多问题中的一个。我选择比其他问题比如领导选举，错误发现，互斥，一致性问题和全局快照更着重看待这个问题的原因是这通常是大部分人们最关系的问题。例如，判断并行数据库不同的一个依据就是它们复制功能的不同。并且，复制会带来了更多相关的子问题，例如领导选举，错误发现，一致性问题和原子广播。

复制是一组通讯问题。那种协议和通信模式给我们带来了我们渴望的性能和可靠的性质？





## 深入阅读

### Primary-backup and 2PC

- [Replication techniques for availablity](http://scholar.google.com/scholar?q=Replication+techniques+for+availability) - Robbert van Renesse & Rachid Guerraoui, 2010
- [Concurrency Control and Recovery in Database Systems](http://research.microsoft.com/en-us/people/philbe/ccontrol.aspx)

## Paxos

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

