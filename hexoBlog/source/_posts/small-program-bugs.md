---
title: 小程序开发遇到的坑
date: 2017-12-24 20:25:26
categories: 小程序
tags: bugs
---

做了一小段时间的微信小程序开发，踩了不少坑，在这里做一下总结：

<!-- more -->

#### 1、在 `app.js` 的  `onShow` 和 `onHide` 时 `getCurrentPages()` 查询页面栈，可能获取到空数组

发现时间：20180517

系统环境：IOS 

> 如果有在这两个场景使用，建议对当前页面对象进行判断是否为`undefined`

#### 2、tabBar页面 `wx.getSystemInfo` 的 `windowHeight` 小于实际高度（大约少了一个tabBar的距离）

发现时间：20180517

系统环境：android部分机

#### 3、tabBar页面Page的`onTabItemTap`事件在首次切换不会触发，要在页面`onShow`后再切换才触发

发现时间：20180517

系统环境：iOS

> sdk 1.9.97有问题、更新到2.0.7正常

#### 4、button的border无法去掉？

小程序的`open-type`是`button`特有的属性，所以使用小程序原生`button`组件在所难免，比如：
```
<button open-type="share" class="btn">
    点击分享
</button>
```

但是原生的`button`可能不太适合业务，所以需要去掉默认样式，比如去掉背景色和边框：
```
.btn {
    background-color: transparent;
    border: 0;
}
```
咦！意不意外？背景是去掉了，边框还在。。。
原来原生`button`的边框是写在 [伪元素](https://developer.mozilla.org/zh-CN/docs/Web/CSS/::after) `after`的，所以，需要这样才能去掉：

```
.btn::after {
    border: 0;
}
```

#### 5、`canvas.draw `回调一直不执行，也没报错（sdk在1.7.1以上都不可以）

> [前往社区类似问题反馈](https://developers.weixin.qq.com/community/develop/doc/000a2452278da84dbc76b2bcf5b400)

如果你用了很多方式也排查不出原因，可以试下把绘画的`canvas`标签放到`page`页面级别的`html`，而不是放到自定义的组件`html`。貌似放到自定义组件，无法找到画布，导致绘画不成功（它没报错，但回调又不执行）。

回头看来[官方api](https://developers.weixin.qq.com/miniprogram/dev/api/canvas/wx.createCanvasContext.html)，原来`createCanvasContext`方法有第二个参数 `Object this`，

> 在自定义组件下，当前组件实例的this，表示在这个自定义组件下查找拥有 canvas-id 的 <canvas/> ，如果省略则不在任何自定义组件内查找

