---
title: Vue开发遇到的问题积累
date: 2018-02-15 19:25:26
categories: bugs
tags: [vue, bug]
---

以下问题出现的场景都是基于vue2.x，如果是vue 1.x，可能就没有参考意义了。

<!-- more -->


## 绑定事件如何主动传当前元素

```
@click="onClick($event, others)"
```

## vue.config如何配置不同打包后的文件输出路径

在使用vue的cli 3.x后，webpack的打包配置都通过项目根目录的`vue.config.js`进行配置，而默认这个文件是不存在的，需要自己创建和书写配置。

假如现在有个需求，我想在不同的打包命令下，打包后的文件输出在不同的本地文件夹：
在`package.json`文件配置了两个命令
（1）当我执行`npm run build`时，进行文件打包，打包后的文件输出在当前项目目录的`dist`文件夹（vue cli默认的配置就是如此）
（2）当我执行`npm run publish`时，进行文件打包，打包后的文件输出在某个指定的本地目录的某个文件夹

方法：

> 通过修改打包命令进行传参数，让`vue.config.js`文件获取到参数来区分不同的打包操作，从而修改打包后的输出文件路径

`package.json`文件配置：

```
"scripts": {
    "build": "vue-cli-service build",
    "publish": "npm_config_publish=true npm run build'"
  },
```

`vue.config.js`文件配置：

```
module.exports = {
    baseUrl: '',
    // 打包输出文件目录
    outputDir: process.env.npm_config_publish ? '/Users/xxx/Documents/host/test/projectname' : 'dist' ,
}
```
以上的打包配置，在`npm run publish`时，打包后的文件就会存在你本地的`'/Users/xxx/Documents/host/test/projectname`里。

## Element-ui的table事件如何传自定义参数
例子代码片段（`row-click`为表格当某一行被点击时会触发该事件）：
```
<el-table
    ref="fieldsTable"
    @row-click="onRowClick"
>
</el-table>
```
而该事件默认有三个参数：`row, column, event`，参数的意义可以看[element-ui官方文档](http://element-cn.eleme.io/#/zh-CN/component/table)

问题：如果我的table是个数组循环渲染，需要传个当前table下标`index`
第一反应的方案是：
```
<template v-for="(item, index) in data">
    <el-table
        ref="fieldsTable"
        @row-click="onRowClick(row, column, event, index)"
    >
    </el-table>
</template>
```
这种写法虽然能够获取到`index`的值，但是，`row` `column` `event`这三个变量的值就变成`undefined`
> 因为如果在绑定的时，给`onRowClick`传入的三个参数，是当前上下文的三个新变量，而当前上下文并没有声明有这三个变量，所以是`undefined`，而传入的`undefined`就覆盖了`row-click`事件的三个默认参数。

我们可以改造一下写法：
```
<template v-for="(item, index) in data">
    <el-table
        ref="fieldsTable"
        @row-click="(row, column, event)=>{return onRowClick(row, column, event, index)}"
    >
    </el-table>
</template>
```
这样，在`onRowClick`就可以正常获取到四个参数的值。


