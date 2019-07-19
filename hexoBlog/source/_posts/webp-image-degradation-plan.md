---
title: Webp图片降级方案有哪些
date: 2018-01-02 19:25:26
categories: 其它
tags: webp
---

Webp已经开始慢慢流行，有些产品还要考虑到一些低版本浏览器的情况，所以做兼容还是少不了，本文来探讨一下，Webp图片降级方案有哪些。

<!-- more -->

### 前言

无论哪种方案，前提你都需要有两套格式的图片。可以通过各种工具进行批量转换。

### 方案一

`前端处理`

```html
<picture>
  <source srcset="img/awesomeWebPImage.webp" type="image/webp">
  <source srcset="img/creakyOldJPEG.jpg" type="image/jpeg"> 
  <img src="img/creakyOldJPEG.jpg" alt="Alt Text!">
</picture>
```
对于每个浏览器都可以用，而不只是支持`<picuture>`元素的浏览器。这是因为不支持`<picture>`的浏览器只会显示`img`指定的图片。

### 方案二

`前端处理`

> 这种方案的原理就是通过先检测当前浏览器是否支持`webp`格式来决定图片和图片背景采用哪种图片格式来展示。

+ 第一步，需要知道浏览器是否支持`webp`格式

```js
document.createElement('canvas').toDataURL('image/webp');
```
如果浏览器支持`webp`格式，返回的结果带有`data:image/webp;`：

```json
data:image/webp;base64,UklGRrgAAABXRUJQVlA4WAoAAAAQAAAAKwEAlQAAQUxQSBIAAAABBxARERCQJP7/H0X0P+1/QwBWUDgggAAAAHANAJ0BKiwBlgA+bTaZSaQjIqEgKACADYlpbuF2sRtACewD32ych77ZOQ99snIe+2TkPfbJyHvtk5D32ych77ZOQ99snIe+2TkPfbJyHvtk5D32ych77ZOQ99snIe+2TkPfbJyHvtk5D32ych77ZOQ99qwAAP7/1gAAAAAAAAAA
```
如果浏览器不支持`webp`格式，返回的结果不带有`data:image/webp;`：

```json
data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAASwAAACWCAYAAABkW7XSAAAAAXNSR0IArs4c6QAAAylJREFUeAHt0DEBAAAAwqD1T20IX4hAYcCAAQMGDBgwYMCAAQMGDBgwYMCAAQMGDBgwYMCAAQMGDBgwYMCAAQMGDBgwYMCAAQMGDBgwYMCAAQMGDBgwYMCAAQMGDBgwYMCAAQMGDBgwYMCAAQMGDBgwYMCAAQMGDBgwYMCAAQMGDBgwYMCAAQMGDBgwYMCAAQMGDBgwYMCAAQMGDBgwYMCAAQMGDBgwYMCAAQMGDBgwYMCAAQMGDBgwYMCAAQMGDBgwYMCAAQMGDBgwYMCAAQMGDBgwYMCAAQMGDBgwYMCAAQMGDBgwYMCAAQMGDBgwYMCAAQMGDBgwYMCAAQMGDBgwYMCAAQMGDBgwYMCAAQMGDBgwYMCAAQMGDBgwYMCAAQMGDBgwYMCAAQMGDBgwYMCAAQMGDBgwYMCAAQMGDBgwYMCAAQMGDBgwYMCAAQMGDBgwYMCAAQMGDBgwYMCAAQMGDBgwYMCAAQMGDBgwYMCAAQMGDBgwYMCAAQMGDBgwYMCAAQMGDBgwYMCAAQMGDBgwYMCAAQMGDBgwYMCAAQMGDBgwYMCAAQMGDBgwYMCAAQMGDBgwYMCAAQMGDBgwYMCAAQMGDBgwYMCAAQMGDBgwYMCAAQMGDBgwYMCAAQMGDBgwYMCAAQMGDBgwYMCAAQMGDBgwYMCAAQMGDBgwYMCAAQMGDBgwYMCAAQMGDBgwYMCAAQMGDBgwYMCAAQMGDBgwYMCAAQMGDBgwYMCAAQMGDBgwYMCAAQMGDBgwYMCAAQMGDBgwYMCAAQMGDBgwYMCAAQMGDBgwYMCAAQMGDBgwYMCAAQMGDBgwYMCAAQMGDBgwYMCAAQMGDBgwYMCAAQMGDBgwYMCAAQMGDBgwYMCAAQMGDBgwYMCAAQMGDBgwYMCAAQMGDBgwYMCAAQMGDBgwYMCAAQMGDBgwYMCAAQMGDBgwYMCAAQMGDBgwYMCAAQMGDBgwYMCAAQMGDBgwYMCAAQMGDBgwYMCAAQMGDBgwYMCAAQMGDBgwYMCAAQMGDBgwYMCAAQMGDBgwYMCAAQMGDBgwYMCAAQMGDBgwYMCAAQMGDBgwYOAdGL/UAAEPpnR6AAAAAElFTkSuQmCC
```

或者可以一开始，通过触发一张只有1像素的`webp`图片，监听它的`onload`事件，如果成功，说明浏览器支持`webp`格式，如果不支持，说明浏览器不支持啦。
不过这种首先你得准备这张图片，其次，你要每次都去请求这张图片。

+ 第二步，根据不同浏览器支持情况展示不同格式

在第一步，外面通过js知道浏览器支持情况，然后把两种格式的图片都放到img标签的自定义属性，根据不同情况取不同的图片地址显示出来即可。

```html
<img data-src="https://xxx.w3cmark.com/f7b07c8.jpg" data-webp="https://xxx.w3cmark.com/f7b07c8.webp">
```

至于样式，可以直接根据检测的结果在`body`标签加入一个特殊样式，然后把`webp`的背景图写在这个样式下即可。

### 方案三

`服务端处理`

前端统一请求图片地址，比如`https://xxx.w3cmark.com/f7b07c8.jpg`，服务端接受到客户端请求时，根据`request`头的`accept`字段来判断，返回哪种格式的图片。

客户端支持`webp`格式时，`request`头的`accept`字段会有`image/webp`，如下图：

![image](https://user-images.githubusercontent.com/7434649/48115040-5f830e00-e29c-11e8-848f-ca71b6aef367.png)

不支持时：

![image](https://user-images.githubusercontent.com/7434649/48115142-d15b5780-e29c-11e8-8390-927575f1d10a.png)

官方对`accept`字段的解释：

> The Accept request-header field can be used to specify certain media types which are acceptable for the response. Accept headers can be used to indicate that the request is specifically limited to a small set of desired types, as in the case of a request for an in-line image.
google翻译一下：

> Accept request-header字段可用于指定响应可接受的某些媒体类型。 接受标头可用于指示请求特别限于一小组所需类型，如在请求内嵌图像的情况下。

这种方案前端不用做特殊处理，但是服务端返回处理写死后，就不太灵活了，如果有某些场景就是想要访问非webp呢？
需要具体方案看业务的具体场景来用哈。

