---
title: "手把手教你玩儿一下 Redis Module 之模块解读"
date: 2018-08-15
draft: false
tags: ["Redis", "C", "Fun","Redis-Module"]
categories: ["Redis", "C","Redis-Module"]
featured_image: https://wujunze.com/blog-images/r/pic/20190928151206.png
---

# 引子

  Redis 现在的 Stable 版本是4.0.11, 对Redis比较关注的童鞋都知道,4.0引入了 Module 。
  我们先一起看看 Redis 4.0都更新了啥?

<!-- more -->
## 官方说法
> Redis 4.0 was released as GA in July 2017. Contains several big improvements: a modules system, much better replication (PSYNC2), improvements to eviction policies, threaded DEL/FLUSH, mixed RDB+AOF format, Raspberry Pi support as primary platform, the new MEMORY command, Redis Cluster support for Nat/Docker, active memory defragmentation, memory usage and performance improvements, much faster Redis Cluster key creation, many other smaller features and a number of behavior fixed.

## 简单总结一下:
1.   自定义扩展 redis 的模块,  当然相比 redis lua 脚本来说，还是有些学习成本的…

2.   psync2 他的好处在于 redis 主从切换后，不需要重新进行 重新 fullsync 同步，只需要部分同步，有点类似 binlog 那种.  具体实现还没看，阿里自己是通过 timestamp 时间戳实现的.

3.   memory 指令可以估算内容大小

4.   非数据命令的异步执行，我们知道 redis 处理请求是单线程的，执行数据偏大的 flush 及 del 会阻塞的，redis 4.0 加了异步操作的功能…. 


# 咱们今天就玩儿一下 Redis Module
## 模块可以做什么 
### 1. 站在巨人的肩膀上 (**访问 Redis 数据空间**)

**官方模块教程 https://redis.io/topics/modules-intro**

 Redis 提供了两套数据访问的 API，一套是较高层的，类似于 Lua 脚本的 API，往往用来调用 API 没有提供支持的 Redis 命令。
 另一套是底层 API，速度很快，基本和 Redis 原生命令一样快，也提供了一些对各种数据结构的进行处理的函数，是推荐的数据访问方式。

 高层 API 使用RedisModule_Call调用 Redis 命令，如：
```c
RedisModuleCallReply *reply;
reply = RedisModule_Call(ctx,"INCR","sc",argv[1],"10");
```

 底层 API 使用RedisModule_OpenKey打开并获取 Key 指针继而进行后续处理，如：
```
RedisModuleKey *key;
key = RedisModule_OpenKey(ctx,"somekey",REDISMODULE_READ);
```

### 2. 走的更远 (实现新的数据结构)
对于简单的数据结构，可以使用 DMA(direct memory access) 将结构编码保存到 Redis 的 String 类型中，如：

```
// 获取字符串内存指针继而修改其内容
size_t len;
char *myptr = RedisModule_StringDMA(key,&len,REDISMODULE_WRITE);
//  增大，减少或创建字符串
RedisModule_StringTruncate(key,1024);
```

也可以使用 API 注册并实现新的数据结构，可以控制内存的分配与释放，RDB 序列化，AOF 重写等。这里是 Redis 官方的一个例子，好奇的同学可以自己点进去看看。
### 3.  灵活拓展  (实现阻塞命令)
阻塞命令（Blocking commands）会阻塞客户端，直到某个期望的事件发生才会返回，比如 List 的BLPOP命令。模块 API 也提供了实现阻塞命令的功能。
Redis 在内置命令集中有一些阻塞命令。其中最常用的是 BLPOP（或对称 BRPOP），它阻止等待到达列表的元素。
关于阻塞命令的有趣事实是它们不会阻塞整个服务器，而只是阻塞它们的客户端。通常阻塞的原因是我们期望发生一些外部事件：这可能是 Redis 数据结构中的一些变化，例如 BLPOP 情况，线程中发生的长计算，从网络接收一些数据，等等。

详细见文档:  https://redis.io/topics/modules-blocking-ops  

### 4.  自由发挥 (加载模块)

模块有两种加载方式，一是在配置文件redis.conf中使用

` loadmodule /path/to/mymodule.so ` 在 Redis 启动时加载。


查看加载的模块:
![查看加载的模块](https://wujunze.com/blog-images/r/pic/20190829144820.png)


另一种方式在运行时使用命令 ``` MODULE LOAD /path/to/panda.so``` 加载。
![](https://wujunze.com/blog-images/r/pic/20190829145258.png)

使用MODULE UNLOAD panda卸载。

![](https://wujunze.com/blog-images/r/pic/20190829145336.png)

在载入的模块的时候可以传入参数，如：loadmodule panda.so hello panda 1234，
参数会被传入模块的OnLoad方法中。


### 5.  人多力量大 (对 Redis Cluster 的支持)

官方文档提供了两个API
```c
RedisModule_IsKeysPositionRequest(ctx);
RedisModule_KeyAtPos(ctx,pos);
```
在首次RedisModule_CreateCommand() 时需要 调用API方法  
```c
int RedisModule_CreateCommand(RedisModuleCtx *ctx, const char *name, RedisModuleCmdFunc cmdfunc, const char *strflags, int firstkey, int lastkey, int keystep);
```
 
其中的strflags参数的一个 flag 为getkeys-api，意思是这个方法是否支持返回参数中 key。一个方法接收的参数有多个，但并不是每一个都是 key，比如LRANGE key start stop中，只有第一个参数是 key。而 Redis 需要知道一个命令涉及到哪些 key，才能在集群中找到对应的服务器并执行命令。

可以参考这个模块的代码  https://github.com/RedisLabsModules/redex/blob/master/src/rxzsets.c  

## 官方模块仓库 ([Redis Module Hub](https://redis.io/modules))
官方模块仓库收录很多优质的模块:
![](https://wujunze.com/blog-images/r/pic/20190829145357.png)

#### 新数据结构，如：

[rejson](https://rejson.io/) 提供了对原生 JSON 格式支持，允许对 JSON 数据内的值进行获取与修改

[Redis Graph](https://redisgraph.io/) 添加了对图数据库的支持
等等

####   对现有数据结构功能的扩展，如：
[rxkeys](https://github.com/RedisLabsModules/redex) 提供了按正则表达式批量获取与删除条目的功能
[rxhashes](https://github.com/RedisLabsModules/redex) 提供了在 Hash 中改变现有条目的值并返回原值的原子操做
[rxlists](https://github.com/RedisLabsModules/redex) 提供了 7 个新的列表操作方法。

### 百闻不如一run 

写了一个非常简单的Redis Module  用于学习 

> # redis-module-panda

>A simple redis module ,  Just for fun

## Inspire And Thanks

https://redis.io/topics/modules-intro
https://redislabs.com/community/redis-modules-hub/
https://github.com/erans/redissnowflake
https://github.com/dayeshisir/redis_module_timestamp

## 安装

### 1. 克隆代码
```shell
git clone https://github.com/wujunze/redis-module-panda.git
```

### 2. make

```bash
make
```
![](https://wujunze.com/blog-images/r/pic/20190829145419.png)

### 3. 加载模块
#### **redis 的版本必须大于4.0**
![](https://wujunze.com/blog-images/r/pic/20190829145434.png)


#### **查看已经安装的模块**

![](https://wujunze.com/blog-images/r/pic/20190829145504.png)

#### **加载模块**
![](https://wujunze.com/blog-images/r/pic/20190829145525.png)

## Run
```shell
127.0.0.1:6379> panda.hello
```
![](https://wujunze.com/blog-images/r/pic/20190829145535.png)

### 测试命令
![](https://wujunze.com/blog-images/r/pic/20190829145547.png)

## 卸载模块
![](https://wujunze.com/blog-images/r/pic/20190829145602.png)


## 完整的测试过程
![](https://wujunze.com/blog-images/r/pic/20190829145614.png)



## **代码仓库: https://github.com/wujunze/redis-module-panda** 
## 觉得有收获请star哦,  还有下集,Thanks

## Inspire And Thanks
https://redis.io/topics/modules-intro

https://redislabs.com/community/redis-modules-hub/

https://liangshuang.name/2017/08/19/redis-module/

https://github.com/erans/redissnowflake

https://github.com/dayeshisir/redis_module_timestamp



> 笔者才疏学浅,仓促成文,如有不当之处,还请大家斧正.
