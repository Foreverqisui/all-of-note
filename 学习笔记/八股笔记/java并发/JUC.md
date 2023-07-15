# JUC

![f369cbeb60f0f566d996df0fd293fe1](D:\A-工作文档\学习笔记\图片\f369cbeb60f0f566d996df0fd293fe1.jpg)

# 一、volatile关键字

> 轻量级的synchronized
>
> 定义：由于java允许线程访问共享变量，为了确保**共享变量**能被准确和一致地更新，线程应确保通过排它锁单独获得这个变量,相比较锁**volatile某些情况下更加方便**，不会引起线程上下文的切换

## 1、如何保证可见性

> * Lock前缀指令引起CPU缓存写回内存
>
> * 一个CPU缓存写回会导致其他CPU缓存无效
>
> * 通过MESI来维护缓存一致性
>
>   * #### MESI协议
>
>     > 作用：实现事务串行化，降低总线带宽，弥补总线嗅探的不足
>     >
>     > modified（已修改）：状态表明是脏标记，表明Cache Block已更新，但未写入内存
>     >
>     > invalidated（已失效）：表明Cache Block已经失效，不可读取该状态的数据
>     >
>     > exclusive（独占）：表明是干净状态，没有其它核心有该数据
>     >
>     > shared（共享）：表示相同数据存在多个核心里，不能直接修改

# 二、synchronized关键字

## 1、使用

> * 对普通同步方法，锁是当前实例对象
> * 对于静态同步方法，锁是当前类的Class对象
> * 对于同步方法快，锁时Synchronized括号里配置的对象

## 2、具体实现

> 对于代码块是通过监视器Monitor使用monitorenter和monitorexit指令实现

## 3、Java对象头

> synchronized用的锁存在Java对象头里
>
> ![87cdc63803f62e8cc33161dba72cd88](D:\A-工作文档\学习笔记\图片\87cdc63803f62e8cc33161dba72cd88.jpg)

## 4、锁升级

### 偏向锁

> 

### 