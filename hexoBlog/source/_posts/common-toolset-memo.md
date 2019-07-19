---
title: 常用工具集备忘
date: 2018-01-15 19:25:26
categories: 工具
tags: tools
---

前端开发过程中用到的各种各样的工具，经常会出现千奇百怪的问题，记录下来，下次再出现时可以快速定位解决。

<!-- more -->

### nrm——NPM源管理

[nrm官方>>](https://github.com/Pana/nrm)

```
# 安装
npm install -g nrm
```

```
Usage: nrm [options] [command]

  Commands:

    ls                           List all the registries
    use <registry>               Change registry to registry
    add <registry> <url> [home]  Add one custom registry
    del <registry>               Delete one custom registry
    home <registry> [browser]    Open the homepage of registry with optional browser
    test [registry]              Show the response time for one or all registries
    help                         Print this help

  Options:

    -h, --help     output usage information
    -V, --version  output the version number
```

---
### npm 报错（`npm ERR! cb() never called!`）

出现环境：OS X/macOS
官方issue：https://github.com/npm/npm/issues/17839

这个报错无论是在官方的issue还是Stack Overflow，都有人反馈，解决思路主要有两种：
（1）来自Stack Overflow的解决方案：`npm cache verify` 和 `npm cache clean`（网上搜索还有就是安装node的助手，但是我实践是不行的，因为在安装 `npm install -g n` 本身也会报这个错）
（2）来自官方的issue评论的解决方案：删除node相关的安装，然后重装node（但看起来操作比较复杂，又要删这，又要删哪的）

**我的最终解决方案：**
去[官方下载](https://nodejs.org/zh-cn/download/)对应的安装包（尝试过`brew install node`安装，但是报错：`Error: Checksum mismatch`，而且我一开始不是通过brew安装的，所以放弃折腾了），我直接选的是当前长期支持版的最新版，直接安装覆盖即可。
![image](https://user-images.githubusercontent.com/7434649/52831068-23b6da80-310e-11e9-9f69-b9bb1b55b9b5.png)

---

### GitHub Pages怎么绑定自己的域名

第一步：申请一个属于自己的域名

第二步：在域名提供商，进入域名解析设置，以我的[http://w3cmark.github.io](http://w3cmark.github.io)为例
（1）先添加一个CNAME，主机记录写`@`，后面记录值写上你的`w3cmark.github.io`
（2）再添加一个CNAME，主机记录写`www`，后面记录值也是`w3cmark.github.io`
这样别人用`www`和不用`www`都能访问你的网站（其实`www`的方式，会先解析成`http://w3cmark.github.io`，然后根据CNAME再变成`http://wwww.3cmark.com`，即中间是经过一次转换的）。
![image](https://user-images.githubusercontent.com/7434649/48395361-04e12a80-e752-11e8-9047-13d3dbebd6ca.png)

第三步：在GitHub Pages项目的Setting-->GitHub Pages-->Custom domain（自定义域名）去直接填写
你自己的域名，如`www.w3cmark.com`，前面不需要`http://`，然后点击旁边的save按钮保存即可。（也可以不需要带`www`，如`w3cmark.com`。两者的区别是，如果带`www`，访问时不管是否带有www，浏览器地址栏访问的都是`www.w3cmark.com`，反之亦然。）
![image](https://user-images.githubusercontent.com/7434649/48397959-660cfc00-e75a-11e8-8b3b-3912d13c0f4b.png)
![image](https://user-images.githubusercontent.com/7434649/48398366-85585900-e75b-11e8-851f-d47577aa1078.png)

> 上面介绍的是CNAME别名记录，也可以使用A记录，后面的记录值是写GitHub Pages里面的ip地址，但有时候IP地址会更改，导致最后解析不正确，所以还是推荐用CNAME别名记录要好些，不建议用IP。

上面三步做完，一般等几分钟，解析生效，就可以用你自己域名访问下试试啦～

---

### GitHub Pages绑定自己的域名后，怎么支持https

> 2018.05.01官方支持https [Custom domains on GitHub Pages gain support for HTTPS](https://blog.github.com/2018-05-01-github-pages-custom-domains-https/)

如果你是采用CNAME别名记录的方式，开启https很简单，只需要在在GitHub Pages项目的Setting去勾选即可（如果你是用ip做解析的，需要更新一下ip，具体可以看GitHub官方博客）
![image](https://user-images.githubusercontent.com/7434649/48398048-aa000100-e75a-11e8-83e2-134af48fc028.png)

---

### Whistle使用

```
# 同一个域名根据不同的path指向不同的ip 
/api.w3cmark.com\/(?!column)(.*)/  http://127.0.0.1:9529/$1
^api.w3cmark.com/column/*** host://10.199.133.253/column/$1
```

---

### MAC 命令相关

```
# 修改hosts
sudo vi /etc/hosts

# 打开当前路径的finder
open .

# 全局命令存放路径
/usr/local/bin

# 全局命令代码存放路径
/usr/local/lib
```

