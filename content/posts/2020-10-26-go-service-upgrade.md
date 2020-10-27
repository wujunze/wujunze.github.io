---
title: "让你的 Go 服务优雅的重启 (解决代码发布 Go 服务闪断的问题)"
date: 2020-05-12
draft: false
tags: ["Golang"]
categories: ["grace","http","overseer","upgrade"]
featured_image: https://wujunze.com/blog-images/r/pic/20201027144234.png
---

# 起因
有同学反馈 发布 Go 服务代码  监控报警会出现一堆 5xx 报警

# 原因
查看生产环境的项目部署脚本     发现目前的部署脚本会 
```shell script
 supervisorctl restart  monkey_interact_service
```
暴力重启 supervisor 托管的守护进程   
会导致  go 的 server      先 stop 停止web服务,  再 start  提供新的 web 服务,   
导致上线过程中的请求失败   监控报警群里一吨报警

# 解决方案 
给 Go 进程发送   **SIGUSR2**  信号 优雅重启  过程中不会中断 web 服务    
go  web服务可以采用     gracehttp    oversee  等成熟的技术方案    
https://github.com/facebookarchive/grace   
https://github.com/jpillora/overseer    

# 验证 --- 实践是检验代码的唯一标准
未使用优雅重启前      
```supervisorctl restart  monkey_interact_service```      
用 wrk 压测 开12 个线程  每秒钟 4000 个请求  请求 20s       
有 209456 个成功请求    
有 163580 个错误请求     
![](https://wujunze.com/blog-images/r/pic/20201027175038.png)

使用优雅重启后     
```supervisorctl signal SIGUSR2 monkey_interact_service```   
同样的 使用 wrk 压测   开12 个线程  每秒钟 4000 个请求  请求 20s     
223552 个成功请求   
**0个**错误请求   
![](https://wujunze.com/blog-images/r/pic/20201027175056.png)

# supervisord 版本要求
supervisord  >= 3.2.0     
由于 supervisord 3.2.0 才增加对  signal 信号的支持     
 详见 http://supervisord.org/changes.html#id20    
![](https://wujunze.com/blog-images/r/pic/20201027175127.png)

# oversee 的原理简析

1. overseer添加了Fetcher，当Fetcher返回有效的二进位流(io.Reader) 时，主进程会将它保存到临时位置并验证它，替换当前的二进制文件并启动。
Fetcher运行在一个goroutine中，预先会配置好检查的间隔时间。Fetcher支持File、GitHub、HTTP和S3的方式。详细可查看包package fetcher

2. overseer添加了一个主进程管理平滑重启。子进程处理连接，能够保持主进程pid不变。
![](https://wujunze.com/blog-images/r/pic/20201027175744.png)


> 笔者才疏学浅,仓促成文, 如有不当之处,还请大家斧正. 