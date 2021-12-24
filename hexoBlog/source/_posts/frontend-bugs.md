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

## npm install 时提示sha512错误

```sh
npm ERR! code EINTEGRITY
npm ERR! sha512-MKiLiV+I1AA596t9w1sQJ8jkiSr5+ZKi0WKrYGUn6d1Fx+Ij4tIj+m2WMQSGczs5jZVxV339chE8iwk6F64wjA== integrity checksum failed when using sha512: wanted sha512-MKiLiV+I1AA596t9w1sQJ8jkiSr5+ZKi0WKrYGUn6d1Fx+Ij4tIj+m2WMQSGczs5jZVxV339chE8iwk6F64wjA== but got sha512-WXI95kpJrxw4Nnx8vVI90PuUhrQjnNgghBl5tn54rUNKZYbxv+4ACxUzPVpJEtWxKmeDwnQrzjc0C2bYmRJVKg==. (65117 bytes)

npm ERR! A complete log of this run can be found in:
```
这是一个很奇怪的问题，同一个版本的包，不同人安装偶然会出现，出现问题后，删除`package-lock.json`再重新安装也无法解决（有时候一开始就报错，都没有生成`package-lock.json`文件）。

尝试 `npm cache clean --force` 清除npm缓存，这是网上搜索出来的方案，但试过无效。

**解决方案：**

```sh
# 关闭npm的https
npm config set Strict-ssl false
```
然后再重新安装就正常了