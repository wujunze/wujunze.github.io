---
title: "使用 git bisect 定位你的 BUG"
date: 2019-10-30
draft: false
tags: ["Git","Debug", "Tools"]
categories: ["Git","Debug", "Tools"]
featured_image: https://wujunze.com/blog-images/r/pic/git_log.png
---

  Git 是开发者的好帮手，今天跟大家分享的是用 git bisect 来找到你代码中的 bad commit 。
<!-- more -->
## 背景
  你可能遇到过这种情况, 昨天下班前把模块开发完了, 单元测试验证通过, `git commmit` 盖上电脑 开开心心下班啦 😄
  第二天啥上午来了,继续开发,提交了几个 commit ,下午部署了一个版本,发现昨天测试通过的代码出现了 BUG 😂
  这个时间你会怎么做, 可能的翻出现 BUG 代码文件的 `git log`  一翻发现 有20个 commit   🤦‍
  此时你的心情可能是崩溃的 🔥   
  
  告别人肉排查 bad  commit   借助 `git bisect` 找 BUG  ✌️
## 具体方法 
### 先通过 `git log` 命令 找到你确定代码是 OK 的 git hash   再找到你当前出现 BUG 的 git hash

### 然后 使用 ```git bisect start``` 开始咱们的奇妙 debug 之旅 🚀

### 开始对 commit 做标记 

#### 告诉 git 这个 commit(刚才找出来确定代码是OK的 commit hash) 是 OK 的 ```git bisect good 5d5dba7```
#### 告诉 git 当前最后一个 commit 代码是有 BUG 的  ```git bisect bad 692ac39 ```

## git 会进行二分查找 
### 然后开始开始 test  验证当前 hash 的 commit 的代码是不是 OK 的   
### 如果当前代码 test 不通过 就标记为 bad  ```git bisect bad```
### 反之如果当前代码 test 通过 就标记为 good  ```git bisect good```

## 反复的进行 test 和标记  直到找出那个 bad commit 为止

## 然后退出 git bisect 模式 ``` git bisect reset ```

附上操作流程图  ![git bisect](https://wujunze.com/blog-images/r/pic/git_debug.png)

## 附上官方文档  [Git Docs](https://git-scm.com/docs/git-bisect)  
## 附上**二分查找**可视化动画 [binary search visualization](https://www.cs.usfca.edu/~galles/visualization/Search.html) 

## 附上我的命令行操作记录  
```bash
wujunze@Mac: ~/monkey/code/monkey-api develop
 $ git bisect start                                                                                                                                              [20:31:46]
 
wujunze@Mac: ~/monkey/code/monkey-api develop
 $ git logg                                                                                                                                                      [20:31:50]
 
wujunze@Mac: ~/monkey/code/monkey-api develop
 $ git bisect good 16e91a8                                                                                                                                       [20:31:54]
 
wujunze@Mac: ~/monkey/code/monkey-api develop
 $ git logg                                                                                                                                                      [20:31:59]
 
wujunze@Mac: ~/monkey/code/monkey-api develop
 $ git bisect bad 692ac39                                                                                                                                        [20:32:04]
Bisecting: 9 revisions left to test after this (roughly 3 steps)
[cd1a0814fe21aa3e06020efb5aa4118ead17acce] not filter
 
wujunze@Mac: ~/monkey/code/monkey-api cd1a081
 $ git bisect bad                                                                                                                                                [20:32:07]
Bisecting: 4 revisions left to test after this (roughly 2 steps)
[63bf3176854a4fe112d612cee3f6bacce9e77e7d] fix merge
 
wujunze@Mac: ~/monkey/code/monkey-api 63bf317
 $ git bisect good                                                                                                                                               [20:32:11]
Bisecting: 2 revisions left to test after this (roughly 1 step)
[798239a0397c52127c721b8b84bb430b5fd0e83b] debug
 
wujunze@Mac: ~/monkey/code/monkey-api 798239a
 $ git bisect bad                                                                                                                                                [20:32:14]
Bisecting: 0 revisions left to test after this (roughly 0 steps)
[5d5dba7c3fc947768cc609493de9808f3d9cf635] fix assert logic
 
wujunze@Mac: ~/monkey/code/monkey-api 5d5dba7
 $ git bisect bad                                                                                                                                                [20:32:23]
5d5dba7c3fc947768cc609493de9808f3d9cf635 is the first bad commit
commit 5d5dba7c3fc947768cc609493de9808f3d9cf635
Author: wujunze <itwujunze@163.com>
Date:   Tue Oct 29 18:20:36 2019 +0800

    fix assert logic

:040000 040000 b5d77b6ac82d8427d1bc3a9db2213f6c10ea0d63 3f49c18b6569282f7fa2a2c935b9ba73d6d0fbc0 M      app
 
wujunze@Mac: ~/monkey/code/monkey-api 5d5dba7
 $ git bisect reset                                                                                                                                              [20:32:27]
Previous HEAD position was 5d5dba7 fix assert logic
Switched to branch 'develop'
Your branch is up to date with 'origin/develop'.
 
wujunze@Mac: ~/monkey/code/monkey-api develop
 $                                                                                                                                                               [20:36:38]                                                                               
```
 
 ## 百闻不如一 Run  大家可以自己试一下  有什么问题可以在文章下面评论
 
> 笔者才疏学浅,仓促成文,如有不当之处,还请大家斧正. 
 
 
  
  
    
