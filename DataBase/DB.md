# 数据库
[TOC]
## MySQL体系
https://blog.csdn.net/wangfeijiu/article/details/112454405
## 事务


## 查询优化
### 使用索引
![](.img/index.png)
![](.img/index_shortage.png)

### 联合索引
对多个字段同时建立的索引。联合索引是有顺序的，ABC，ACB是完全不同的两种联合索引。

以联合索引(a,b,c)为例，建立这样的索引相当于建立了索引a、ab、abc三个索引。

一个索引顶三个索引当然是好事，但是每多一个索引都会增加写操作的开销和磁盘空间的开销，需要谨慎使用。
https://zhuanlan.zhihu.com/p/29118331

#### 最左匹配
(A,B,C) 这样3列，mysql会首先匹配A，然后再B，C。如果用(B,C)这样的数据来检索的话，就会找不到A使得索引失效。如果使用(A,C)这样的数据来检索的话，就会先找到所有A的值然后匹配C，此时联合索引是失效的。把最常用的，筛选数据最多的字段放在左侧。

#### 原理
联合索引(A,B,C)是一棵B+Tree，其非叶子节点存储的是**第一个关键字的索引**，而叶节点存储的则是三个关键字A、B、C三个关键字的数据，且按照A、B、C的顺序进行排序。

当执行以下查询的时候，是无法使用这个联合索引的。

select * from STUDENT where B='b';

因为联合索引中是先根据A进行排序的。如果A没有先确定，直接对B和C进行查询的话，就相当于乱序查询一样，因此索引无法生效，查询是全表查询。

## 页分裂
https://www.cnblogs.com/ZhuChangwu/p/14041410.html


https://www.zhihu.com/question/21760988/answer/1760780564



https://www.zhihu.com/question/279538775/answer/1760623006