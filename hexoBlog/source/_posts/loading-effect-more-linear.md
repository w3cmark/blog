---
title: 让loading效果更线性
date: 2017-12-20 20:25:26
categories: JavaScript
tags: 总结
---

为了提高用户体验，在做一些比较多资源的项目，我们经常会用资源预加载的方式，一打开页面会先看到一个loading动画效果或者loading进度条，再或者加载百分比，用来暗示用户，页面资源比较多，正在加载中啦，你再等等哈，不要走喔。。。

<!-- more -->

而这里的资源预加载，就是通过js去预先请求某些资源，以备页面展示时可以直接使用，比如图片预加载，我们可能会这样实现：

```javascript
var img = new Image();
img.onload = function () {
// 加载成功...
};
img.onerror = function () {
// 加载出错...
};
img.src = 'https://dummyimage.com/90x90/ddd/79c';
```

通过创建一个不可见的img来触发图片请求，上面演示的只是加载一张，一般情况做预加载都会有多资源才做啦，所以批量预加载只需要加入循环：

```javascript
var iFileData = [//预加载图片资源
        'https://dummyimage.com/90x90/ddd/79c',
        'https://dummyimage.com/100x100/ddd/79c',
        'https://dummyimage.com/200x200/ddd/79c',
        'https://dummyimage.com/300x300/ddd/79c',
        'https://dummyimage.com/400x400/ddd/79c',
        'https://dummyimage.com/500x500/ddd/79c'
    ],
	_total = iFileData.length,
	_loaded = 0;
for(var i = 0; i < _total; i++) {
    loadImage(iFileData);
}
loadImage = function(iData){
    var img = new Image();
    img.onload = img.onerror = function () {
        _loaded++;
        checkLoadComplete();
    };
    img.src = iData;
},
checkLoadComplete = function(){
    if(_loaded == _total) {
          console.log('加载完毕');
    }
};
```

上面可以实现简单的预加载了，但好像没有当前加载的进度喔
下面加上一个当前加载进度的百分比：

```javascript
checkLoadComplete = function(){
    var per = (_loaded/_total).toFixed(2);
    if(per > 1) {
        per = 1;
    }
    console.log('当前加载进度：' + per);
    if(_loaded == _total) {
          console.log('加载完毕');
    }
}
```

到这，资源预加载的功能基本就ok了，然后展示进度跟进上面的进度返回，转成数字百分比或者一个进度条就可以了。
另外，这个功能很常用，可以再做一下封装，方便使用，还可以加入其它资源的支持，比如多媒体文件？

完了？
但，这个不是本文的重点，本文的重点是`线性`。

### 改良，让加载更线性

上面的预加载有个问题，体验不好。比如，你要预加载10个资源，显示的百分比就是0%，10%，20%，30%...这样10个数字的跳动，极端点，当你只有一个资源（当然这里是极端考虑，像前文说的，一个资源，你估计不会用预加载了）时，进度就是0%~100%，一下就从0跳到100了。

还有，即使是10个资源，当用户第二次打开页面，因为资源有缓存，这个loading效果就会可能一闪而过，因为有缓存加载太快了，进度都看不清就没了，体验也不好（或者即使第一次，如果用户网速很好，也会出现一闪而过）。

所以，上面的预加载体验不够好，不稳定，导致动画效果不线性。

预想的线性是怎样的呢？比如进度百分比数字，我预想的是无论多少资源，第几次打开，网速如何，都是从0%，1%，2%，3%，4%，5%，6%....这样直到100的，只有这样，数字展示才不会大幅度跳动或者进度条的宽度才不会跳跃。

那怎么达到这样的数字累加效果呢？咦？累加？那可以用定时器啊：

```javascript
var _num = 0;
var _num_interval = setInterval(function(){
    _num++;
    if(_num > 99){
        clearInterval(_num_interval);
        _num = 100;
    }
    console.log(_num);
}, 50)
```

嗯，这样数字就不跳动啦，很线性！！从0~100，每隔50毫秒加1，到100就停止，so easy~

线性数字有了，但和我加载进度有毛关系？而且资源加载是会根据网速不同而不同，这50毫秒。。。假的进度，明显不行啊

第一个问题，线性数字和实际资源加载联系起来，这个还是很容易的。从第一个资源开始，就同时触发触发上面的计时器，当最后一个资源加载完而且计算到100了，就完成了预加载过程。

第二个问题，定时器的时间怎么和实际资源加载联系起来呢？定时器先根据资源数定一个初始值，加载过程通过把假的计算进度和真实的加载进度进行比较，实时调整定时器的时间，以达到尽可能的模拟实际进度：

```javascript
var _time = 50;
var linearLoad = function(){
    if(_num_interval){
        clearInterval(_num_interval);
    }
    _num_interval = setInterval(function(){
        _num++;
        if(_num > 99 && (_loaded == _total)){
            clearInterval(_num_interval);
            _num = 100;
            console.log(_num);
            // 加载完毕
            console.log('加载完毕');
            return;
        }else if(_num > 99){
            clearInterval(_num_interval);
            return;
        }
        console.log(_num);
        if(_time > 1){
            adjustSpeed(_time_range);
        }
    }, _time)
},
adjustSpeed = function(range){
    //调节数字变化速度
    var per = (_loaded/_total).toFixed(2);
    if(parseInt(per*100) > _num){
        //如果实际加载速度快，提高线性数字速度
        _time = _time - range;
        _time = _time < 1 ? 1 : _time;
        linearLoad();
    }else{
        //如果实际加载慢，降低线性数字速度
        _time = _time + range;
        linearLoad();
    }
}
```

逻辑细节请移步测试demo：http://www.w3cmark.com/staticdemo/loading/

### 效果预览

![loading.gif](https://raw.githubusercontent.com/w3cmark/blog/master/assets/loading.gif)

