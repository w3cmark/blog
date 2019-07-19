---
title: 前端开发踩过的坑
date: 2018-02-05 19:25:00
categories: bugs
tags: [cors, bug]
---

本文用于总结在前端开发中遇到的一些比较杂的坑。比如CORS跨域等等。

<!-- more -->

## CORS跨域方案

接到一个前端跨域请求（GET和POST）相关的需求
一调试，咦！浏览器提示跨域报错了～
简单！让服务端童鞋加上跨域请求头，开启CORS：

```
Access-Control-Allow-Credentials: true
Access-Control-Allow-Headers: x-requested-with,content-type,x-xsrf-token
Access-Control-Allow-Methods: GET,POST,OPTIONS
Access-Control-Allow-Origin: http://w3cmark.com
```
⚠️这里有个彩蛋，服务端加跨域请求头要小心，冒号前面如果有空格，会出现浏览器兼容问题，比如火狐浏览器看到的请求没有跨域请求头；再比如在chrome下，可以正常看到有跨域请求头，但如果使用whistle做代理，跨域请求头就丢失了。

以下是错误示范（冒号前面多了个空格）：
```
Access-Control-Allow-Credentials : true
Access-Control-Allow-Headers : x-requested-with,content-type,x-xsrf-token
Access-Control-Allow-Methods : GET,POST,OPTIONS
Access-Control-Allow-Origin : http://w3cmark.com
```

服务端设置搞定！前端再次请求，请求响应头带上上面的返回，GET请求顺利过关。。。
如果接口需要登录，前端设置请求`withCredentials: true`，请求就带上cookie，顺利过关。。。
再来一个POST请求
咦！浏览器多发了一个OPTIONS请求，一查，原来是preflight request，[预校验请求](https://developer.mozilla.org/zh-CN/docs/Glossary/Preflight_request)

> 这里的请求被分成两种：简单请求和复杂请求，只有复杂请求才会发OPTIONS请求，详情介绍移步：https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Access_control_CORS

----------说了那么多坑终于来了---------------

因为服务端对请求做登录校验（包括OPTIONS请求），校验通过后，才会给请求加跨域头返回，而OPTIONS请求是无法携带cookie的
来自SO的截图：
![image](https://user-images.githubusercontent.com/7434649/54797787-99274500-4c90-11e9-876a-0e8b9e93d597.png)
所以服务端无法接收到cookie，一直认为未登录，导致发OPTIONS请求时浏览器直接报跨域了。

**解决方案，服务端不需要对OPTIONS请求做cookie校验的操作**

