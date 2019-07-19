---
title: 使用chrome extension解决gitlab在线编辑时输入中文出现错乱的问题
date: 2017-11-12 20:25:26
categories: 工具
tags: [gitlab, chrome extension]
---

先上一张gif效果图：

![gitlab-chrome-input-bug](https://github.com/w3cmark/blog/blob/master/assets/gitlab-chrome-input-bug.gif?raw=true)

我们团队内部搭建了gitlab作为代码托管，目前的版本是`GitLab Community Edition 8.17.5 9a564a8`，在升级到这个版本时，发现在chrome下，在线编辑无法正常输入中文了，出现如上动图的错乱效果，而在firefox是正常的。

<!-- more -->

一开始怀疑是chrome更新版本后的问题，但是github的在线编辑是正常的，后来通过chrome开发者工具debug，发现是gitlab使用的在线编辑器有问题。无论gitlab还是github，这种在线编辑代码都是借助于一些web编辑器实现的，比如[aceeditor](https://ace.c9.io/)，而这里gitlab用的正是`aceeditor`，因此第一时间跑去aceeditor官网，嗯，人家的是正常的。所以gitlab出现这种问题的原因就是aceeditor编辑器的版本在新版chrome下的bug。

找到问题源头，解决办法也就有眉目了：

+ 直接更新`GitLab Community Edition`版本到最新版，看看官方是否更换了aceeditor编辑器的版本解决了这个问题。

+ 升级gitlab，可能会担心升级过程会不会有什么新问题，特别是大版本更新就更要慎重。既然这样，那就直接自己在现版本手动去更新aceeditor编辑器，理论上找到gitlab的安装，找到编辑器的相关文件，替换成新的版本。前提是你要摸索清gitlab的目录结构，完整替换编辑器版本。听起来有点麻烦，而且也有一定风险~

+ 这里介绍另一种思路，通过`chrome extension`在打开编辑页时替换编辑器。这种不是治本的方案，但感觉也是一种思路，刚好团队本身有一个在用从chrome插件，所以把解决方案放进现有的插件即可。这个方案可能适用性和参考意义不大，但这种思路说不定能给到你一些新的想法呢

下面详细说下第三种方案的操作细节：
（涉及到chrome extension的相关细节，这里就不详细描述，默认你拥有chrome extension的开发能力）

#### 下载最新版的aceeditor编辑器
这一步很简单，直接去官网下载即可[aceeditor](https://ace.c9.io/)，然后把编辑器的相关文件放到chrome插件下，比如：
```
|--src  //插件源码
|    |--js
|    |    |--lib
|    |    |    |--src-min-noconflict  //aceeditor
```
到时候打包插件时会一起打包的。

#### 加载编辑器文件

什么时候去加载新版的编辑器文件呢？因为我们只需要在gitlab编辑界面才用到，所以通过URL匹配来识别当前是编辑界面，然后再去加载aceeditor相关文件。另外还可以根据当前的编辑文件类型，来加载对应的编辑器文件，然后再通过`chrome.tabs.executeScript`来实现。
```javascript
chrome.tabs.onUpdated.addListener(function (tabId, changeInfo, tab) {
    // when complete
    if (changeInfo.status === 'complete') {

        if(/https:\/\/(你的系统域名)\/.+\/(edit|blob)\/.+/ig.test(tab.url)){
            var newurl = tab.url.replace('(你的系统域名)',''),
                r_file_suffix = /((?!(\.|\/))[\s\S])+\.([a-z]+)/ig;
            newurl.match(r_file_suffix);
            var file_suffix = RegExp.$3,
                jsFiles = [
                '/js/lib/jquery-2.1.3.min.js',
                '/js/lib/src-min-noconflict/ace.js'
                ];
            //根据文件类型
            switch(file_suffix){
                case 'js':
                jsFiles.push('/js/lib/src-min-noconflict/mode-javascript.js');
                break;
                case 'tmpl':
                jsFiles.push('/js/lib/src-min-noconflict/mode-html.js');
                break;
                case 'md':
                jsFiles.push('/js/lib/src-min-noconflict/mode-markdown.js');
                break;
                default :
                jsFiles.push('/js/lib/src-min-noconflict/mode-'+ file_suffix +'.js');
            }
            //替换编辑器以及保存等逻辑
            jsFiles.push('/js/fixgitlabinput.js');

            chrome.tabs.executeScript(tabId, {
                code: 'var injected = window.octotreeInjected; window.octotreeInjected = true; injected;',
                runAt: 'document_start'
            }, function (res) {

                var cssFiles = ['/css/fixgitlabedit.css'];

                eachTask([function (cb) {
                  return eachItem(cssFiles, inject('insertCSS'), cb);
                }, function (cb) {
                  return eachItem(jsFiles, inject('executeScript'), cb);
                }]);

                function inject(fn) {
                  return function (file, cb) {
                    chrome.tabs[fn](tabId, { file: file, runAt: 'document_start' }, cb);
                  };
                }
            });
        }
    }
});
```

#### 替换操作

加载到编辑器文件，那最后一步就是进行编辑器替换，以及新编辑器的初始化，最后还要在保存时把新编辑器的内容提交。
这些逻辑放进了单独js文件`fixgitlabinput.js`处理。
这里我选择通过插入一个新的按钮来进行新编辑器的切换：
```javascript
var fix_btn = '<span class="fix-btn" id="js-fix-btn">中文编辑</span>';
$('body').append(fix_btn);
```

当点击这个按钮时，会做两件事：
（1）根据当前地址，获取目前编辑文件的内容
（2）把获取到的文件内容用新的编辑器初始化，并隐藏旧版的编辑器，显示新版的编辑器

```javascript
var fix_btn = '<a href="#fixgitlabedit" class="fix-btn" id="js-fix-btn">中文编辑</a>';
$('body').append(fix_btn);

if(location.hash == '#fixgitlabedit'){
     //传入当前地址和文件类型
    fixeditor(curhref, file_mode);
    $('#js-fix-btn').hide();
}

function fixeditor(curhref, file_mode){
    //隐藏旧的编辑器
    $('#editor').hide();

    var fix_div = '<pre id="js-fix-editor" class="fix-editor"><pre>',
        new_editor,
        $old_editor_par = $('#editor').parent();
    // 插入新的编辑器
    $old_editor_par.append(fix_div);

    var $form_actions = $('form .form-actions');

    //隐藏旧的保存按钮
    $form_actions.find('.btn.commit-btn.js-commit-button.btn-create').hide();
    var new_submit_btn = '<button name="button" class="btn btn-create new-submit-btn">Commit Changes</button>';

    //插入新的保存按钮
    $form_actions.prepend(new_submit_btn);

    //保存操作
    $form_actions.on('click', '.new-submit-btn', function(e){
        if($(this).hasClass('disabled')){return;}
        e.preventDefault();
        $(this).addClass('disabled');
        //获取编辑器的内容，aceeditor提供的方法
        var new_val = new_editor.getValue();
        $('#file-content').val(new_val);
        $("form.form-horizontal.js-quick-submit.js-requires-input.js-edit-blob-form")[0].submit();
    })

    // 根据当前地址获取文件内容
    getHttpRequest(curhref, function(data){
        var r_editor_text = /\<pre class="js-edit-mode-pane" id="editor"\>([\s\S]*)\<\/pre\>/igm;
        data.match(r_editor_text);
        var editor_text = RegExp.$1;
        $old_editor_par.find('#js-fix-editor').html(editor_text);
        //获取编辑器的内容，aceeditor提供的方法
        new_editor = ace.edit("js-fix-editor");
        new_editor.session.setMode("ace/mode/"+file_mode);
    });
}

function getHttpRequest(href, callback){
    var xhr = new XMLHttpRequest();
    xhr.open("GET", href, true);
    xhr.onreadystatechange = function() {
      if (xhr.readyState == 4) {
        var data = xhr.responseText;
        typeof callback == 'function' && callback(data);
      }
    }
    xhr.send();
}
```

到这里，就可以正常输入中文并保存了，对于没有接触chrome插件开发的人来说可能比较麻烦，so，就当新想法的折腾呗~


