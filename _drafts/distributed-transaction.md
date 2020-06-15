# 分布式事务

ACID
Atomicity
Consistency
Isolation
Durability


CAP定律（Consistency, Availability, Partition Tolerance），Base理论
## 几种解决思路：
* XA接口，2PC, 3PC
* 消息驱动（事务消息，非事务消息）
* 补偿任务

跨分片事务也分布式事务，想要了解分布式事务，就需要了解“XA接口”和“两阶段提交”。值得提到的是，MySQL5.5x和5.6x中的xa支持是存在问题的，会导致主从数据不一致。直到5.7x版本中才得到修复。Java应用程序可以采用Atomikos框架来实现XA事务（J2EE中JTA）。

## References
* [分布式系统事务一致性解决方案](http://www.infoq.com/cn/articles/solution-of-distributed-system-transaction-consistency)
* [关于分布式事务、两阶段提交、一阶段提交、Best Efforts 1PC模式和事务补偿机制的研究](https://blog.csdn.net/bluishglc/article/details/7612811)
