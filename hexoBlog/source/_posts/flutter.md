---
title: Flutter 入门问题
date: 2019-12-01 09:25:26
categories: flutter
tags: [flutter, 前端]
---

本文收集了在入门flutter时，被哪些问题卡住了。

<!-- more -->

## 在执行flutter run时，卡在了Resolving dependencies...

出现这种问题查到到原因是网络问题（但是我尝试设全局代理也没成功），官方到[issues](https://github.com/flutter/flutter/issues/11856)也提到这个问题。

> 我的电脑系统是mac OS 10.14.6

+ 解决方法一：

手动去构建android的gradlew（尝试过，对我没效）

```sh
./flutter_project_name/android/gradlew

# After successful build.
# Try run the project again.
flutter run
```

+ 解决方法二：

修改 `./flutter_project_name/android/build.gradle`里的`repositories`（有两处），使用国内代理（对我有效）。

```
repositories {
    // google()
    // jcenter()
    maven { url 'https://maven.aliyun.com/repository/google' }
    maven { url 'https://maven.aliyun.com/repository/jcenter' }
    maven { url 'http://maven.aliyun.com/nexus/content/groups/public' }
}
```

## VScode 在dart后缀文件里，没有自动补全提示

## VScode 安装flutter环境后，dart 在活动进程里 CPU 90 ~ 100