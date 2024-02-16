# 2_MySQL事务篇

## 1. 一条Insert语句的执行流程

```sql
CREATE TABLE `tab_user` (
 `id` int(11) NOT NULL,
 `name` varchar(100) DEFAULT NULL,
 `age` int(11) NOT NULL,
 `address` varchar(255) DEFAULT NULL,
 PRIMARY KEY (`id`)
) ENGINE=InnoDB;
```

```sql
Insert into tab_user(id,name,age,address) values (1,'刘备',18,'蜀国');
```

![image-20240216203119728](https://picture-bed01.oss-cn-beijing.aliyuncs.com/imgs/202402162031799.png)



## 2. 事务四大特性ACID

数据库事务具有ACID四大特性。ACID是以下4个词的缩写：

- **原子性（Atomicity）**： 原子性是指事务是一个不可分割的工作单位，事务中的操作要么都发生，要么都不发生。
- **一致性（Consistency）**： 事务前后数据的完整性必须保持一致
- **隔离性（Isolation）**：多个用户并发访问数据库时，一个用户的事务不能被其它用户的事务所干扰，多个并发事务之间数据要相互隔离。**隔离性由隔离级别保障！**
- **持久性（Durability）**： 一个事务一旦被提交，它对数据库中数据的改变就是永久性的，接下来即使数据库发生故障也不应该对其有任何影响



## 3. 事务并发问题

1. 脏读：一个事务读到了另一个事务**未提交**的数据
2. 不可重复读：一个事务读到了另一个事务**已经提交(update)**的数据。引发事务中的多次查询结果不一致

3. 虚读/幻读：一个事务读到了另一个事务**已经插入(insert)**的数据。导致事务中多次查询的结果不一致
4. **丢失更新的问题！**



## 4. 事务隔离级别

1. read uncommitted 读未提交【RU】，一个事务读到另一个事务没有提交的数据
   存在：3个问题（脏读、不可重复读、幻读）。

2. read committed 读已提交【RC】，一个事务读到另一个事务已经提交的数据
   存在：2个问题（不可重复读、幻读）。
   解决：1个问题（脏读）

3. repeatable read:可重复读【RR】，在一个事务中读到的数据始终保持一致，无论另一个事务是否提交
   解决：3个问题（脏读、不可重复读、幻读）

4. serializable 串行化，同时只能执行一个事务，相当于事务中的单线程 解决：3个问题（脏读、不可重复读、幻读）

**安全和性能对比**

安全性：serializable > repeatable read > read committed > read uncommitted

性能 ：serializable < repeatable read < read committed < read uncommitted

**常见数据库的默认隔离级别：**
MySql：repeatable read
Oracle：read committed

## 5. 事务底层原理详解

### 5.1 丢失更新问题

两个事务针对同一数据进行修改操作时会丢失更新，这个现象称之为丢失更新问题

![image-20240216204033089](https://picture-bed01.oss-cn-beijing.aliyuncs.com/imgs/202402162040157.png)

### 5.2 解决方案

**（1）基于锁并发控制LBCC：**

Lock Based Concurrency Control

![](https://picture-bed01.oss-cn-beijing.aliyuncs.com/imgs/202402162042706.png)

这种方案比较简单粗暴，就是一个事务去读取一条数据的时候，就上锁，不允许其他事务来操作。

假如 当前事务只是加**读锁**，那么其他事务就不能有**写锁**，也就是不能修改数据；而假如当前事务需要加**写锁**，那么其他事务就不能持有任何锁。总而言之，能加锁成功，就确保了除了当前事务之外，其他事务不会对当前数据产生影响，所以自然而然的，当前事务读取到的数据就只能是**最新的，而不会是快照数据**。

**（2）基于版本并发控制MVCC：**

Multi Version Concurrency Control

![image-20240216204556493](https://picture-bed01.oss-cn-beijing.aliyuncs.com/imgs/202402162045551.png)

**MVCC使得普通的SELECT请求不加锁，读写不冲突，显著提高了数据库的并发处理能力**

### 5.3 MVCC实现原理【InnoDB】

**MVCC 实现原理关键在于数据快照，不同的事务访问不同版本的数据快照，从而实现事务下对数据的隔离级别。**MVCC 实现原理关键在于数据快照，不同的事务访问不同版本的数据快照，从而实现事务下对数据的隔离级别。

MVCC 的实现依赖与**Undo日志** 与 **ReadView**

![image-20240216205018933](https://picture-bed01.oss-cn-beijing.aliyuncs.com/imgs/202402162050979.png)

InnoDB下的表有**默认字段**和**可见字段**，默认字段是实现MVCC的关键，默认字段是隐藏的列。默认字段最关键的两个列，**一个保存了行的事务ID，一个保存了行的回滚指针。**

**每开始新的事务，都会自动递增产生一个新的事务id。事务开始后，生成当前事务影响行的ReadView。当查询时，需要用当前查询的事务id与ReadView确定要查询的数据版本。**

#### undo日志：

Redo日志记录了事务的行为，可以很好地通过其对页进行“重做”操作。但是事务有时还需要进行回滚操作，这时就需要undo。因此在对数据库进行修改时，InnoDB存储引擎不但会产生Redo，还会产生一定量的Undo。这样如果用户执行的事务或语句由于某种原因失败了，又或者用户用一条Rollback语句请求回滚，就可以利用这些undo信息将数据回滚到修改之前的样子。在多事务读取数据时，有了Undo日志可以做到读不加锁，读写不冲突。

#### ReadView：

**MVCC的核心问题就是：判断一下版本链中的哪个版本是当前事务可见的！**

- 对于使用 RU 隔离级别的事务来说，直接读取记录的最新版本就好了，不需要Undo log。
- 对于使用 隔离级别的事务来说，使用加锁的方式来访问记录，不需要Undo log。
- 对于使用 RC 和 RR 隔离级别的事务来说，需要用到undo 日志的版本链。

#### ReadView案例分析：

- 案例01-读已提交RC隔离级别下的可见性分析

  每次读取数据前都生成一个ReadView，默认tab_user表中只有一条数据，数据内容是刘备。

- 案例02-可重复读RR隔离级别下的可见性分析

  在事务开始后第一次读取数据时生成一个ReadView。对于使用 RR 隔离级别的事务来说，只会在第一次执行查询语句时生成一个 ，之后的查询就不会重复生成了



### 5.4 MVCC下的读操作

在MVCC并发控制中，读操作可以分成两类：**快照读 (Snapshot Read)与当前读 (Current Read)**

- 快照读：读取的是记录的可见版本 (有可能是历史版本)，不用加锁。刚才案例中都是快照读。
- 当前读：读取的是记录的最新版本，并且当前读返回的记录，都会加上锁，保证其他事务不会再并发修改这条记录



## 6. 小结

- MVCC指在使用RC、RR隔离级别下，使不同事务的 读-写 、 写-读 操作并发执行，提升系统性能
- MVCC核心思想是**读不加锁，读写不冲突**
- RC、RR这两个隔离级别的一个很大不同就是生成ReadView的时机不同
  - RC在每一次进行普通SELECT操作前都会生成一个ReadView
  - RR在第一次进行普通SELECT操作前都会生成一个ReadView，之后的查询操作都重复这个ReadView



