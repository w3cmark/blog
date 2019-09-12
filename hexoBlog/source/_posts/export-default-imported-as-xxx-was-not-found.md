---
title: Webpack项目使用alias报错export 'default' (imported as 'xxx') was not found in 'xxxx'
date: 2019-09-08 19:25:26
categories: Webpack
tags: [Webpack, alias, export, import, 前端]
---

## 背景

最近遇到一个Webpack项目报错很头疼：通常我们开发npm包的时候，都会需要经过本地项目测试的，为了调试方便，会通过`npm link`或者`alias`来引入本地开发好的包。

比如vue-cli 3.0项目，在`vue.config.js`配置alias引入`@ams-team/ams`这个包：

```js
module.exports = {
    configureWebpack: config => {
        config.resolve = {
            extensions: ['.js', '.vue', '.json'],
            alias: {
                '@ams-team/ams': resolve('../../packages/ams') // 一般包的package.json会通过 main: "lib/ams.js"来知道引入文件的
            }
        };
    },
};
```
这时候你在项目引入 `import ams from '@ams-team/ams'` 会得到下面报错：

```cli
export 'default' (imported as 'ams') was not found in '@ams-team/ams'
```
但是如果我从 npm 上下载自己的包是不报错的。

<!-- more -->

## 报错的根本原因

通过谷歌了一番，发现报错的根本原因是：`import` 和 `module.exports` 语法混用。

项目引入肯定是使用 `import` 语法的，如果出现混用，那就是打包的文件是使用 `module.exports` ？然后赶紧搜索了一下 `@ams-team/ams` 下的 `lib` 文件，发现果然有`module.exports`。。。
难道是 Webpack 没有转成 ES6 的问题？

经过查阅Webpack官方文档，发现Webpack会将项目打包成 `ES5` 的版本，而在新项目里用 `import` 里引入了带有 `module.exports` 语句的文件时就会导致上面的报错。

那如何解析 `npm` 下载后可以正常使用呢？

**猜测：**

我们在写 `webpack.config.js` 会发现有一项 `Exclude`，说是可以不对某些文件进行编译从而提高编译性能。难道Webpack对从 `npm` 下载下来的文件进行预先编译，将其转成 ES6，而本地引入的话没有预先编译。

为了验证我的猜测，做了以下测试：

1. 将 `ams/lib` 下所有文件拷到新项目的 `/src` 里，直接本地引入，同样报错。
2. 将 `ams/lib` 下所有文件拷贝到新项目的 `/node_modules/ams` 里，直接本地使用 `import '/node_modules/ams/ams.js'` 引入，成功！

通过测试，验证了 **Webpack 会对 `/node_modules` 下的文件进行预先编译，再引入到真实项目中，这样就没有 `module.exports` 的语句了** 这个猜测是对的

## 解决方案

我真的需要做本地调试，那怎么办呢？我总不能每次都拷贝进引入项目的`/node_modules/`吧？

其实不用，我们还是可以配置alias来实现引入包的源码：

```js
module.exports = {
    configureWebpack: config => {
        config.resolve = {
            extensions: ['.js', '.vue', '.json'],
            alias: {
                '@ams-team/ams': resolve('../../packages/ams/src') // 直接引用src源码
            }
        };
    },
};
```

但是这样可能会带来另外一个问题：因为引入源码，那就是每次都要把这个包进行编译，所以可能会涉及到编译速度和编译依赖问题，那就另外讨论了。