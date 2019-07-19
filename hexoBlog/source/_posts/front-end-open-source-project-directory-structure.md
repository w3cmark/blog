---
title: 前端开源项目目录结构
date: 2017-12-29 16:25:26
categories: 其它
tags: 总结
---

得益于各种自动化工具，如今前端开源项目的目录结构越来越复杂了，在看别人代码时可能一脸懵逼，在这里整理了常见的目录结构。

<!-- more -->

## 文字版如下：

### .circleci
> 存放CircleCI持续集成相关，目前只支持github和Bitbucket代码管理工具

### build
> webpack的相关配置，包括基本配置、开发环境配置、生产环境配置等

### config

> 针对开发环境，生产环境，测试环境的配置信息

### dist

> 存放编译打包后的文件

### examples

> 存放例子

### flow

> flow（JavaScript静态类型检查器，貌似Eslint）的 libdef 相关，用来存放引入一些第三方库或是自定义类型库

### mock

> 存放项目用于模拟的数据

### node_modules
> 安装依赖时自动生成的，用于存放项目依赖包

### src

> 项目源码

### static

> 静态资源，此目录下的文件不会被webpack处理。在webpack打包后，此目录下的文件会默认被直接复制到dist/static/中

### test

> 用于存放测试文件，比如unit单元测试、e2e测试等

### .babelrc

> babel配置文件

### .editorconfig

> 编辑器编码规范配置文件，用于告诉编辑器本项目的一些编码规范，比如用多少个空格

### .eslintignore

> ESLint语法检查忽略配置文件，用于配置忽略语法检查的目录文件

### .eslintrc

> ESLint规则配置文件，用于语法检查

### .flowconfig

> flow（一个 js 静态类型检测器）项目的配置文件

### .gitignore

> 用于告诉Git系统要忽略掉跟踪哪些文件

### .gitkeep

> 用于跟踪项目中的空文件（默认情况Git 不跟踪空文件夹）

### .gitattributes

> 以行为单位设置一个路径下所有文件的属性

### .postcssrc.js (postcss.config.js)

> postcss配置文件

### .travis.yml

> Travis CI（持续集成服务）的配置文件，指定了 Travis 的行为，Github 仓库里面，一旦代码仓库有新的 Commit，Travis 就会去找这个文件，执行里面的命令

### index.html

> 首页静态html，可能是主入口

### LICENSE

> 开源协议

### Makefile

> Make（主要用于C语言的项目构建工具，也有些前端开源项目会支持）构建规则

### package-lock.json

> 使用npm安装依赖时自动生成的确切依赖配置文件，用于后续安装或跨机器安装能够生成相同版本的依赖，即使安装的依赖有更新也不影响

### package.json

> npm 模块配置文件，用于定义了这个项目所需要的各种模块，以及项目的配置信息（比如名称、版本、许可证等元数据）

### yarn.lock

> 使用用Yarn CLI 增加／升级／删除依赖时自动生成的确切依赖配置文件，准确存储每个安装的依赖是哪个版本，用于后续安装或跨机器安装能够生成相同版本的依赖

### README.md

> 项目自述说明，用于概述本项目相关信息：是什么、做什么、有什么用、怎么用等

## 思维导图版如下：

![前端开源项目目录结构图](https://raw.githubusercontent.com/w3cmark/blog/master/assets/5bd13302e4b06fc64b2d8e0d.jpg)
