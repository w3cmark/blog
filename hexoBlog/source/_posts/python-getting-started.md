---
title: Python入门积累
date: 2018-01-20 21:05:26
categories: Python
tags: python
---

最近在学习Python，本文用来记录学习过程中觉得值得记录的知识点，以备复习。

<!-- more -->

### 用处

+ web网站和各种网络服务

+ 系统工具和脚本

+ 作为“胶水”语言把其他语言开发的模块包装起来方便使用

### 优缺点

+ 解析型语言，执行速度比 c（编译成机器码）/java（编译成字节码） 都慢

+ 源码不能加密

+ 代码量少，开发速度快

### 注意事项

+ 版本兼容问题，2.7和3.3代码存在不兼容的情况

+ 如果中文字符串在Python环境下遇到 UnicodeDecodeError，这是因为.py文件保存的格式有问题。可以在第一行添加注释

```python
# -*- coding: utf-8 -*-
```


