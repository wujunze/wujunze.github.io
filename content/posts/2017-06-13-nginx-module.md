---
title: "实战开发一个 Nginx 扩展 (Nginx Module)"
date: 2017-06-13
draft: false
tags: ["Nginx", "C", "Fun","Nginx-Module"]
categories: ["Nginx", "C","Nginx-Module"]
featured_image: https://wujunze.com/blog-images/r/pic/20190829192836.png
---

# 实战开发一个Nginx扩展 (Nginx Module)
## repo地址 https://github.com/wujunze/nginx-http-echo-module 
## nginx_module_echo
使用 echo 指令输出一个字符串
<!-- more -->

## Nginx 版本
Nginx1.0.10 https://github.com/nginx/nginx/releases/tag/release-1.0.10
![](https://wujunze.com/blog-images/r/pic/20190829192857.png)

## 开发环境
```shell
OS : CentOS Linux release 7.2.1511 (Core)
```
![](https://wujunze.com/blog-images/r/pic/20190829192908.png)
![](https://wujunze.com/blog-images/r/pic/20190829192916.png)

## 安装一个干净的 Nginx
1. 下载 Nginx10.10 并且解压它
![](https://wujunze.com/blog-images/r/pic/20190829192943.png)


2. 安装 gcc 和 Nginx 需要的 lib
![](https://wujunze.com/blog-images/r/pic/20190829192957.png)
![](https://wujunze.com/blog-images/r/pic/20190829193007.png)

3. ./configure --prefix=/usr/local/nginx && make && make install
![](https://wujunze.com/blog-images/r/pic/20190829193024.png)
![](https://wujunze.com/blog-images/r/pic/20190829193036.png)
![](https://wujunze.com/blog-images/r/pic/20190829193134.png)
4. 运行 Nginx
![](https://wujunze.com/blog-images/r/pic/20190829193147.png)
![](https://wujunze.com/blog-images/r/pic/20190829193154.png)

## 定义模块配置结构
```C
typedef struct {
    ngx_str_t ed;  //该结构体定义在这里 https://github.com/nginx/nginx/blob/master/src/core/ngx_string.h
} ngx_http_echo_loc_conf_t;
```
![](https://wujunze.com/blog-images/r/pic/20190829193219.png)

#定义echo模块的指令和参数转化函数
![](https://wujunze.com/blog-images/r/pic/20190829193229.png)
## 定义模块 Context
1. 定义 ngx_http_module_t 类型的结构体变量
![](https://wujunze.com/blog-images/r/pic/20190829193251.png)
2. 初始化一个配置结构体
![](https://wujunze.com/blog-images/r/pic/20190829193303.png)
3. 将其父 block 的配置信息合并到此结构体 实现了配置的继承
![](https://wujunze.com/blog-images/r/pic/20190829193313.png)

## 编写Handler  模块真正干活儿的部分
![](https://wujunze.com/blog-images/r/pic/20190829193331.png)

## 组合Nginx Module
![](https://wujunze.com/blog-images/r/pic/20190829193404.png)

## 整理模块代码 按照Nginx官方规范
![](https://wujunze.com/blog-images/r/pic/20190829193431.png)

## 编写config文件
```shell
ngx_addon_name=ngx_http_echo_module
HTTP_MODULES="$HTTP_MODULES ngx_http_echo_module"
NGX_ADDON_SRCS="$NGX_ADDON_SRCS $ngx_addon_dir/src/ngx_http_echo_module.c"
```
## 编译安装echo模块
```shell
 ./configure --prefix=/usr/local/nginx/ --add-module=/root/ngx_dev && make && make install
```
## 安装成功
![](https://wujunze.com/blog-images/r/pic/20190829193450.png)

## 修改Nginx配置文件测试Module
![](https://wujunze.com/blog-images/r/pic/20190829193504.png)

## Nginx echo Module 运行成功
![](https://wujunze.com/blog-images/r/pic/20190829193530.png)

## repo地址 https://github.com/wujunze/nginx-http-echo-module 
# 如果这个repo对你有帮助  欢迎star fork   

## Inspire And Thanks
1. http://wiki.nginx.org/Configuration
2. http://tengine.taobao.org/book/
3. https://www.nginx.com/resources/wiki/modules/  

> 笔者才疏学浅,仓促成文,如有不当之处,还请大家斧正.

# Thanks