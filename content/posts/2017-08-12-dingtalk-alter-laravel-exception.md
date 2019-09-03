---
title: "使用钉钉通知你的 Laravel 框架系统异常"
date: 2017-08-12
draft: false
tags: ["PHP", "Laravel", "Monitor"]
categories: ["PHP", "Laravel"]
featured_image: https://wujunze.com/blog-images/r/p/001/laravel.png
---


看了Hanson兄弟的[倍洽](https://laravel-china.org/articles/10158/use-bearychat-to-notify-you-of-your-system-exceptions)通知异常 

我们团队主要用钉钉，就想找个 Laravel 的钉钉通知异常的包，最好是安装后开箱即用的。

<!-- more -->

在Google和GitHub上找了有3分钟还是没有找到能开箱即用的，于是自己**动手丰衣足食**。

花了20分钟"写"(chao)了一个 Laravel 钉钉通知异常的包  **[wujunze/dingtalk-exception](https://github.com/wujunze/dingtalk-exception)**

## "食"用方法

### 安装

```shell
composer require wujunze/dingtalk-exception
```

### 发布配置文件
```shell
php artisan vendor:publish --provider="DingNotice\DingNoticeServiceProvider
```

### 配置Handler

**app/Exceptions/Handler.php**

```php
use Wujunze\DingTalkException\DingTalkExceptionHelper;

class Handler extends ExceptionHandler
{
  // ...
  
    public function report(Exception $exception)
    {
        DingTalkExceptionHelper::notify($exception);
        parent::report($exception);
    }

}
```
**记得配置你的 config/ding.php**

## 效果如下
![file](https://wujunze.com/blog-images/r/p/001/laravel-dingtalk.png)

GitHub仓库: https://github.com/wujunze/dingtalk-exception  

## Inspire And Thanks

[cblink/bearychat-exception](https://github.com/cblink/bearychat-exception)   
[wowiwj/ding-notice ](https://github.com/wowiwj/ding-notice)

> 笔者才疏学浅,仓促成文,如有不当之处,还请大家斧正.

### Thanks  