# DBMS

## DBMS内部组成结构

### DBMS的内核【只接受SQL语句】

- 编译器（语法分析器）
- 授权检查
- 语义分析和查询处理
- 访问管理、并发控制、恢复模块【物理层】
  - 实现了关系模型的各种概念
  - 直接和操作系统打交道

UFI 提供给用户的即席访问接口

API 由数据库系统提供给数据库的各种使用方法

### DBMS运行状态下的进程结构

#### 单进程结构

-  把应用程序的代码和DBMS核心代码连接在一起，运行后就是一个单一的进程，仅一个exe文件

#### 多进程结构【满足多用户多任务】、多线程结构

- 将DBMS核心进程和应用程序的进程分开，当应用进程要访问数据库时，应用程序的进程进程CONNECT时向系统发出请求，系统建立一个对应的DBMS核心进程，并在两个进程间建立通信的管道。
  - 问题：
    - 当系统访问的应用程序很多时，会导致由过多的DBMS核心进程，会导致电脑的性能变差
  - 为了解决此问题，创建线程
- DBMS进程的结构
- 公共空间
  - DAEMON 
    - 监听数据库连接请求的端口号
    - 为成功建立连接的应用程序建立DBMS核心线程
  - catlog 目录
    - 关于数据的数据
  - locktable 封锁表
    - 用来控制对锁的申请
  - buffer 
    - 缓冲区

## DBMS的物理层实现【访问管理】

- 物理层的主要工作：把对数据库的访问转化成在操作系统层的物理的数据结构的访问

### 访问类型

#### 查询一个文件的大部分元组

- 判定标准：查询设计的元组数占该关系总元组数的15%以上
- 以15%为标准的原因：硬盘是块设备，数据库存储在硬盘上，以物理块为单位读写，当占了15%，那么要访问该关系的全部的物理块。

#### 查找某条特定元组

#### 查找若干条元组【不超过15%】

#### 范围查询

#### 更新

### 文件组织

#### 堆文件

- 最常见、最基础的文件组织方式
- 随着数据写入不停向文件尾部写
- 查找方式：顺序扫描
- 适用于：查询一个文件的大部分元组

#### Hash文件

- 用户在查询时效率更快
- 对特定属性值建立HASH函数

#### 堆文件+B加树索引

- 将关系存为堆文件上，在某属性上加上b+树索引

#### 动态Hash

#### 栅格结构文件

#### RAW DISK机制/Clustering （簇集存储）

- 允许用户自己控制数据在磁盘上如何存储
- 在某个磁盘的某个信道上按顺序存储所需要的数据
- 此存储方式可以减少磁头的查找，加快查找效率，适合经常被查找但不经常被修改的数据
- DBMS自己做了一个文件管理

### 索引

- b+树  【常用】
- 簇集索引（即簇集存储，因簇集存储方式本身即为一种索引，是按某属性排序之后的存储方式）  【常用】
- 倒排文件
- 动态hash
- 栅格文件【以多维数组存储】、分区hash

### 访问原语

## 查询优化

- 对可能的代价进行代价估算找出一个好的策略

#### 代数优化

- 把用户提交的初始查询改写成等价的效率更高的形式

DBMS内部会用关系代数表达查询，通过分析器生成查询树。

- 代数优化对原来生成的查询树进行变化，把一元操作尽量往叶端下压，从而减小二元操作的规模

##### 相关概念

- 查询树
  - 叶子结点表示关系，中间结点表示一个操作，从叶结点到根的顺序就是一个执行顺序
- 等价变换规则
  - 连接和笛卡尔乘积的交换律
    - 代数优化的时候连接或笛卡尔乘积操作的左右子树可以交换
  - 连接和笛卡尔乘积的结合律
    - 代数优化的时候连接或笛卡尔乘积操作时可以旋转
  - 投影操作的串接律
  - 选择操作的串接律
  - 选择与投影操作的交换律
  - 选择的条件只与单个表有关，可以把选择的操作压到连接操作的下面
  - 附和并兼容的可以压下去

##### 基本原则

- 运用等价变换规则把一元操作尽量往叶端下压
- 寻找并且合并公共子表达式

#### 操作优化

- 找到最有效的计算方法实现操作

##### 选择操作的优化

##### 投影操作的优化

##### 集合操作的优化

##### 连接操作的优化

###### 嵌套循环的改进【？？】

- 内循环和外循环

- 基于磁盘是块访问，由于CPU运算速度比I/O存取速度块的多。【减少了读盘次数，空间换时间】
- 每次I/O取一个物理块，用块的内容遍历块的内容进行

- 为什么只给一个缓冲区给内循环？
  - 

###### 归并扫描算法

- 两个关系事先做好外排序后挨个比较

###### B+树索引

- 使用最多

###### Hash 连接

- 在两个连接的公共属性上定义一个相同的hash函数，把两个表一起散列到一个hash文件中

##### 组合操作的优化

## 事务管理

### 恢复

#### 主要目的

- 能够从故障中恢复
- 尽可能地减少系统发生故障地可能

#### 要求满足

-  冗余【数据备份】是必要地
- 恢复机制要能够检测到所有故障

#### 常用地恢复策略

- 周期性备份
  - 长时间整个备份，短时间备份变化地部分
    - 恢复丢失地信息较少
- 备份+日志
  - 日志记录自上次备份以来数据库的所有改变
    - 改变：
      - B.I 旧的值
      - A.I 新的值
  - 相当于通过日志重演数据库的所有变化
  - 不会丢失信息，保证数据库数据的一致性，不会产生丢失更新

#### 事务

- 数据库最基本的运行单位
- 如果不自己定义事务，那么系统默认一条SQL语句当作一条事务

##### ACID准则

- 原子性：要么全部完成（commit），要么什么都不做(rollback)
- 保持一致性：数据库本身状态一致，经过一个事务后，数据库保持另一个状态一致
- 隔离性：事务之间不能干扰
- 持久性：一个事务只要成功完成，那么影响永久

##### ACID准则的应用

- 如银行转账问题，把转账看出一个事务，如从A的账户转前给B账户

#### 恢复使用的相关数据结构

- 恢复信息必须存储在非挥发存储器【不会因为断电就信息丢失】

##### 数据结构包括

- commit list:所有已经提交的事务的列表
- Active list:当前在运行的事务的列表
- log

#### 两个规则

##### 提交规则

- 当事务的运行提交之前要把所有的更新后项【新值】写到硬盘上

##### 日志提前规则

- 如果直接修改数据库，在更新之前要把被修改数据的老值先写到日志中

##### 数据库恢复方法

###### 幂等性

- 数据库的redo 和undo 做n次和做1次的效果相同

###### 三种更新策略

通过active list 和commit list 的TID的状态判断故障时事务的进展情况是要                                                                                     redo 还是undo 

- 在commit 之前AI直接写入DB【需要undo,不需要redo】
  - TID写入active list
  - BI写入日志
  - AI写入DB
  - commit命令后将事务的tid写入commit list
  - 把事务的TID减去active list
  - 出现故障的话，进行重启动恢复
- 遇到commit 命令后将改动的数据写入DB【比第一种更新策略效率高】【需要redo,不需要undo】
  - 把TID写入active list
  - AI写入DB
  - 遇到commit 命令后把TID写入commit list
  - 遇到commit 命令后将改动的数据AI写入DB
  - 把TID从active list中删除
- AI写入DB和commit命令并发执行【可能需要redo ,也可能需要undo】
  - 把TID写入active list
  - AI 和BI都写入DB
  - 利用磁盘空闲时用后台线程把AI写入DB
  - 遇到commit 命令后把TID写入commit list。
  - 确保AI完全写入DB
  - 把TID从active list中删除

### 并发控制

- 并发就是在一个多任务系统中允许多事务同时访问数据库

- 并发提高反应时间和数据库利用率
- 对事务的并发运行进行管理，防止出现以下问题：
  - 读写冲突
  - 写写冲突

#### 可串行化

- 判断并发事务运行结果是否正确
- 按照系统并行运行事务随机调度的运行结果和按照某种串行顺序的运行结果相同，那么说明是可串行化的。
- 如果调度是可串行化的，那么调度是正确的
- 也就是说n个事务的并发运行的正确结果有n！种串行序列其中一个

#### 封锁法及其锁协议

- 封锁法是保证可串行化的方法之一，不是唯一的方法

##### 核心想法

- 要求某个事务对数据进行读写操作时要先申请一个锁，按照抢到锁的顺序运行

##### 锁协议

- X锁协议

  - 整个系统只有一种锁，排他锁

  - 把并发运行的事务强行串行化

  - 定义

    - 定义一：在一个事务里，如果所有的加锁请求都在所有的锁之前，就是两段加锁协议，就是两阶段事务。

      - 两段加锁协议

      - ```
        LOCK A
        LOCK B
        LOCK C
        ……
        UNLOCK A
        UNLOCK B
        UNLOCK C
        ```

      - 就是要的锁一起申请完，然后一起释放

    - 定义二：如果在申请锁之后再访问数据就是well-formed,如果未申请锁就直接访问数据不是well-formed.

  - 定理及结论

    - 如果S是一个well-formed的两阶段事务，那么它一定是可串行化的。
    - 如果S是一个well-formed的两阶段事务，且把更新操作的锁推迟到事务结束的时候，那么它不光是可串行化的，在恢复时不会出现多米诺现象。
    - 如果S是一个well-formed的两阶段事务，且把所有的锁推迟到事务结束的时候，那么它是一个严格的两阶段加锁协议。

- （S，X）锁协议

  - S是共享锁：允许同时进行的读操作【提高效率】
  - X是排他锁：更新数据

- （S,U,X）锁协议

  - 要对数据更新操作时先申请U锁，U锁时仍可读，直到要写数据时把U锁升级成X锁
  - 把排他锁尽量后写。【可以和数据库的策略配合使用】
  - 进一步提高运行效率

#### 死锁与活锁

- 死锁：等待锁关系之间出现循环等待。
- 活锁：某个时候等待相当长时间仍然申请不到锁。【饥饿现象】
  - 解决方法：
    - 按先进先出排个队

##### 死锁的解决办法

- 防止出现死锁
- 找出牺牲者，避免死锁

#### 死锁检测与死锁预防

##### 死锁检测

- 超时法：设定一个等待时限
  - 超过这个时限就是死锁了
- 构造等待图：顶点集合就是参于的事务，等待关系就是边
  - 一旦等待图出现环路，那么就是出现死锁了
  - 检查环路的时机
    - 出现一条新边
    - 安排一个后台线程，周期性检查

##### 死锁预防

- 给每个事务安排时间戳
  - 时间戳的作用：
    - TID 事务的ID
    - 比较两个事务的年龄
  - 等死法：当TA要申请的锁被Tb占有了，比较两者时间戳，如果自己年龄更小，就杀了自己sleep一会再运行，自己年龄更大，就等待。
    - 等待关系单方面，不会出现死锁
  - 等法：当TA要申请的锁被Tb占有了，比较两者时间戳，如果比较更小，就等待，自己年龄大，就杀了自己sleep一会再运行。

##### 多粒度封锁【？？？】



