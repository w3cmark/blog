---
title: Laraval-nova入门安装
date: 2017-12-26 19:25:26
categories: 其它
tags: laraval
---

最近在接触基于PHP框架laraval的一个nova项目，它 [官方文档](https://laravel-china.org/index.php/docs/nova/1.0/install/2186) 在介绍安装有些步骤不是很详细，所以记录一下整个安装流程：

<!-- more -->

1、需要确保安装mysql、php、composer环境

2、安装laraval框架，通过larval框架创建新project项目

```
# 安装laraval
composer global require "laravel/installer"
 
# 创建名字为blog项目
laravel new blog
 
 
# 如果提示laraval命令没找到，需要设置环境变量，比如mac下：
vim ~/.zshrc
# 添加以下path
export PATH="$HOME/.composer/vendor/bin:$PATH"
```

3、进入创建好的新project项目，修改composer.json文件

```
// 新加repositories字段
"repositories": [ 
    {
        "type": "path",
        "url": "./nova" // 下载源码解压后存放的相对路径
    }
],
 
// 修改require字段
"require": {
    …
    "laravel/nova": "*" // 新加
},
```

4、修改`composer.json`文件后，按顺序执行：

执行 `composer update`

执行 `php artisan nova:install`

5、本地数据库创建 

修改`.env`文件里面数据库相关信息，比如数据库名、用户名、密码

执行 `php artisan migrate`

（本人测试发现在mysql8.xx版本会出现连接报错，暂时没能解决，连接5.xx的mysql没问题）

但是又遇到`Unknown character set: 'utf8mb4'`报错，解决方案是修改项目里的 `/config/database.php `

![laraval_nova.png](https://raw.githubusercontent.com/w3cmark/blog/master/assets/laraval_nova.png) 

6、创建用户（用于访问时的登录）

`php artisan nova:user`

7、启动服务 `php artisan serve`

访问路径 `http://127.0.0.1:8000/nova/`

即可看到后台界面啦
