
# inner_mail分库分表的总结
  线上inner_mail表已超过一亿条记录
## 分库分表前需排查的问题
* 分布式全局唯一ID
  * Twitter的Snowflake
  * UUID/GUID
  * MongoDB ObjectID(类似UUID)
* 跨库join
  * 全局表，类似数据字典
  * 字段冗余
  * 多次查询，再组装
* 跨库事务（分布式事务）
  * XA
  * BASE(柔性事务) Saga和Seata两种BASE类型

## References:
* [水平分库分表的关键步骤以及可能遇到的问题](http://www.infoq.com/cn/articles/key-steps-and-likely-problems-of-horizontal-split-table?utm_source=infoq&utm_campaign=user_page&utm_medium=link)
* [分库分表需要考虑的问题及思路](https://juejin.im/entry/591968f6a0bb9f005ff7af23)
* [Database sharding explained in plain English](https://www.citusdata.com/blog/2018/01/10/sharding-in-plain-english/)
