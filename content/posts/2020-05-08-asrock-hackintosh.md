---
title: "Hackintosh (黑苹果)折腾"
date: 2020-05-08
draft: false
tags: ["MacOS","Apple", "Hackintosh", "ASRock-Z370-Pro4", "Hardware", "9900K"]
categories: ["Apple","Mac","macOS","Hardware"]
featured_image: https://wujunze.com/blog-images/r/pic/20200510211809.png
---

## ASRock-Z370-Pro4-9900K-Hackintosh

## 系统版本 
**MacOS Catalina**

## EFI 文件 GitHub 链接
EFI 引导文件可以去 ```git clone https://github.com/wujunze/ASRock-Z370-Pro4-9900K-Hackintosh``` 

## 详细配置

|  硬件 | 品牌型号  |
|  ----  | ----  |
|  处理器	  | 英特尔 Core i9-9900K 3.6GHz 八核心十六线程  |
| 主板  | 华擎 Z370 Pro4 |
| 内存	  | 芝奇 DDR4 3200MHz 8GB x 2|
| 硬盘	  | 三星 970 Pro MLC 1T |
| 显卡	  | 蓝宝石 白金 满血版 RX560 4G|
| 显示器	  | LG 27UL850 27英寸 4K Type-C IPS|
| 蓝牙和无线网卡		  | 博通 BCM943602CS |

## 部分截图
![boot menu](https://wujunze.com/blog-images/r/pic/20200508193513.png)
![loading](https://wujunze.com/blog-images/r/pic/20200508193103.png)
![install one](https://wujunze.com/blog-images/r/pic/20200508193138.png)
![选择语言](https://wujunze.com/blog-images/r/pic/20200510205422.png)
![install end](https://wujunze.com/blog-images/r/pic/20200508193214.png)
![htop one](https://wujunze.com/blog-images/r/pic/20200508193259.png)


# 安装系统
## 1.制作苹果系统 安装U盘  
1.1 U盘 16G  格式化为 GUID    Mac日志格式 

1.2 执行命令 制作启动U盘   
`sudo /Users/wujunze/Desktop/Install\ macOS\ Catalina.app/Contents/Resources/createinstallmedia --volume /Volumes/MacInstall`

1.3 U 盘制作 OK 
![U 盘制作 OK](https://wujunze.com/blog-images/r/pic/20200510201150.png)

## 2. copy 引导文件 给 U盘用 
2.1 挂载 EFI 分区
```diskutil list```
```sudo diskutil mount disk4s1```
![diskutil list](https://wujunze.com/blog-images/r/pic/20200510201719.png)

2.2 把你主板对应的 EFI 文件 copy 到 EFI 分区
![EFI](https://wujunze.com/blog-images/r/pic/20200510201845.png)

2.3 此时的 U 盘 应该是这样的 
![](https://wujunze.com/blog-images/r/pic/20200510201910.png)

#  通过 EFI 引导 MacOS U盘镜像安装系统
选择 U盘里面的 UEFI 启动项  
![UEFI 启动项](https://wujunze.com/blog-images/r/pic/20200510202456.png)
![U盘引导 MacOS 安装](https://wujunze.com/blog-images/r/pic/20200510202748.png)

## 进入安装引导界面    按回车键
![安装界面](https://wujunze.com/blog-images/r/pic/20200510203052.png)

## Clover 引导 
![loading](https://wujunze.com/blog-images/r/pic/20200510203227.png)
进入安装界面 
![Mac 安装工具](https://wujunze.com/blog-images/r/pic/20200510203716.png)

格式化磁盘
![显示磁盘](https://wujunze.com/blog-images/r/pic/20200510203858.png)
![抹掉磁盘](https://wujunze.com/blog-images/r/pic/20200510204053.png)
![系统盘格式化成功](https://wujunze.com/blog-images/r/pic/20200510204828.png)
安装系统
![安装系统](https://wujunze.com/blog-images/r/pic/20200510205013.png)

安装 
![安装系统](https://wujunze.com/blog-images/r/pic/20200510205341.png)

安装完成
![选择语言](https://wujunze.com/blog-images/r/pic/20200510205422.png)

进入系统 
![进入系统](https://wujunze.com/blog-images/r/pic/20200510210019.png)

系统概览
![系统概览](https://wujunze.com/blog-images/r/pic/20200510210149.png)

存储空间
![disk info](https://wujunze.com/blog-images/r/pic/20200510210616.png)

## 通过 硬盘 EFI 引导系统 
重复第一步的 挂载 EFI 分区步骤  
然后**把U盘里面的 EFI 引导文件 copy 到硬盘的 EFI 分区**即可

去 APP store 下载一个 Xcode 
![APP store](https://wujunze.com/blog-images/r/pic/20200510211158.png)

## 恭喜你的 **Hackintosh** 已经安装完成了  


**折腾黑苹果是一个很有趣的过程 可以深入了解系统启动 系统引导 赶快动手折腾起来吧** 

**欢迎评论区留言交流 或者加入 Hackintosh 交流小组  加微信1017109588 暗号Hackintosh**
 
> 笔者才疏学浅,仓促成文,如有不当之处,还请大家斧正. 
 
 
