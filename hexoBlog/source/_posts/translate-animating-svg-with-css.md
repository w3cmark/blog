---
title: 【译】使用CSS开发SVG动画
date: 2018-06-29 19:25:26
categories: translate
tags: [css, svg]
---

> 本文采用意译
> 原文：[Animating SVG with CSS](https://blog.logrocket.com/animating-svg-with-css-83e8e27d739c/)

![image](https://i2.wp.com/blog.logrocket.com/wp-content/uploads/2019/04/1_-mL-Y_OA9L_GvrKpKxrvpA.png?w=1600&ssl=1)

<!-- more -->

动画是网页内很有趣的一部分。它们可以改善用户体验，因为它们可以提供视觉反馈，任务指引和为网站添加动感。这里有几种方法可以创建Web动画，比如JavaScript库，GIF和嵌入式视频。但SVG和CSS的简单组合很有吸引力，原因有几个。首先由代码实现而不是数千个图像帧组成的它们具有高性能，并且比庞大的GIF和视频具有更快的加载时间。此外，它们还可以创建许多简单的动画，而无需在网页加载中另一个JavaScript插件。最重要的是，SVG是基于矢量的，因此它们可以在不同屏幕尺寸上完美缩放，而不会产生失真。

现在，你可能想知道：为什么选择CSS？ 为什么不使用原生SVG动画规范[SMIL](https://developer.mozilla.org/zh-CN/docs/Web/SVG/SVG_animation_with_SMIL)进行动画制作？ 事实证明，SMIL的支持率正在下降。 Chrome（Chrome 45已经弃用）正朝着弃用SMIL的方向发展，转而采用CSS动画和Web Animations API。 所以，我们继续使用CSS动画......但它们是如何制作的？ 在本文中，我们将学习如何制作这些轻量级，可扩展的动画！

## 使用CSS制作SVG动画的常见例子

首先，让我们看一下在Web应用程序或登录页面中为什么需要SVG动画的实际例子。

### 图标（Icons）

SVG动画非常适合指引交互和状态变化的图标。 在引导用户进行下一个操作时，例如在上手指引中，它们也很有用。 常见用例包括加载，上传，菜单切换以及播放/暂停视频。

### 插图（Illustrations）

插图是另一个常见的用例。在产品中，它们可以用来演示如何在仪表板上从空白状态生成数据。 而动画表情符号和贴纸也是其他流行的用例。 此外，还有动画现场插图，可以在制作品牌登陆页面时增加立体感和乐趣。


## 如何准备SVG

现在，让我们深入了解细节。 你要做的第一件事就是准备一个SVG。 当你因为准备的SVG变得复杂而变成一个狂躁的动画专家时，再开始简化可能会很烦人，但是从简化的SVG代码开始会更容易。

### 简化SVG代码

创建SVG时，它有些额外的代码，通常是不必要的。 因此，优化它非常重要。 我喜欢使用[SVGO](https://github.com/svg/svgo)来减少文件大小并使用唯一ID保存路径（这对于防止同一页面上的多个SVG出现问题非常重要）。 SVGO是一个Node.js工具，有几种方法可以使用它，包括Sketch插件：SVGO Compressor。

### 创建有意的分组（如果需要）

在代码编辑器中打开SVG文件，并注意 `<g>` 元素。 这些元素是用于分组SVG元素。 如果要将一组元素设置为动画，请将它们包装在 `<g> </g>` 中，并使用 `class` 或 `ID` 命名它们。 如果你希望以相同方式设置多个形状（ID只能使用一次），请考虑将ID名称转换为class名称。 一旦你在形状上有了ID或类，你就可以用CSS来定位它们。 保存SVG时，暂时不会有任何明显的变化。

### 小心堆叠顺序（如果你要为另一个形状后面的形状设置动画）

这似乎是违反常识的，但最后列出的形状将粘贴在所有形状上。 因此，如果你希望在背景中显示形状，请确保它放在在SVG代码的顶部。 SVG形状从上到下依次“绘制”。


### 将SVG样式设置为首选的初始状态

SVG具有类似于CSS样式的表现属性，但是是直接在SVG标签上设置的。 一个常见的例子是填充颜色。 由于这些样式是在SVG标签上设置的，因此你可能认为它们在浏览器中具有很高权重。 但事实证明，你在外部设置的任何`CSS / Sass`都会正常地覆盖SVG样式，而不需要`！important`声明。 但是，你需要注意SVG上设置的内容，以便为页面加载期间显示的内容做好准备。 对于加载缓慢的页面，你可能会在通过CSS设置样式之前看到SVG闪现。 我建议你预留位置，以避免在页面加载过程中无样式闪现的SVG（[Sara Soueidan在这篇文章里很好地解释了无样式SVG的闪现（FOUSVG）](https://www.sarasoueidan.com/blog/svg-style-inheritance-and-fousvg/)）。

## 把CSS应用于SVG

现在你已经拥有了整洁的SVG代码，让我们开始介绍如何引入CSS。 关于如何将CSS应用于SVG，需要考虑一些因素。 有个限制是你无法使用外链的样式将样式应用于外链的SVG。


### 方法一：在HTML中嵌入SVG代码（我最喜欢的）

这使得SVG元素及其内容成为文档DOM树的一部分，因此它们受到文档CSS的影响。 这是我的最喜欢的方式，因为它使样式与标签分开。

在下面的其他方法中，你会看到它们是相互缠绕的。 如果你正在使用[Rails](https://ruby-china.github.io/rails-guides/getting_started.html)，那么有些插件可以自动将SVG嵌入到视图中。 因此，在你的代码中，你可以简单地引用外部SVG，然后在编译时嵌入它。 此方法的另一个好处是内联SVG意味着减少一个HTTP请求。 例子：

<iframe name="cp_embed_1" src="https://codepen.io/lukelogrocket/embed/NmLQdz?height=265&amp;theme-id=0&amp;default-tab=html%2Cresult&amp;user=lukelogrocket&amp;slug-hash=NmLQdz&amp;pen-title=SVG%20-%201&amp;name=cp_embed_1" scrolling="no" frameborder="0" height="265" allowtransparency="true" allowfullscreen="true" allowpaymentrequest="true" title="SVG - 1" class="cp_embed_iframe " style="width: 100%; overflow:hidden; display:block;" id="cp_embed_NmLQdz"></iframe>

### 方法2：在SVG中插入带有CSS的`<style>`标签

你可以在`<style>`标签中添加CSS样式，然后嵌套在`<svg>`标签内。例子：

<iframe name="cp_embed_2" src="https://codepen.io/lukelogrocket/embed/axaeWO?height=265&amp;theme-id=0&amp;default-tab=html%2Cresult&amp;user=lukelogrocket&amp;slug-hash=axaeWO&amp;pen-title=SVG%20-%202&amp;name=cp_embed_2" scrolling="no" frameborder="0" height="265" allowtransparency="true" allowfullscreen="true" allowpaymentrequest="true" title="SVG - 2" class="cp_embed_iframe " style="width: 100%; overflow:hidden; display:block;" id="cp_embed_axaeWO"></iframe>

### 方法3：使用外部链接在SVG中包含CSS

如果你希望保留SVG中引用的样式，但又不想把样式包含在SVG标签内，则可以使用`<？xml-stylesheet>`标签引入外部样式表。例子：

<iframe name="cp_embed_3" src="https://codepen.io/lukelogrocket/embed/qwMejZ?height=265&amp;theme-id=0&amp;default-tab=html%2Cresult&amp;user=lukelogrocket&amp;slug-hash=qwMejZ&amp;pen-title=SVG%20-%203&amp;name=cp_embed_3" scrolling="no" frameborder="0" height="265" allowtransparency="true" allowfullscreen="true" allowpaymentrequest="true" title="SVG - 3" class="cp_embed_iframe " style="width: 100%; overflow:hidden; display:block;" id="cp_embed_qwMejZ"></iframe>

### 方法4：在SVG中使用内联CSS样式

也可以使用内联样式属性`style`直接在元素上设置CSS。

<iframe name="cp_embed_4" src="https://codepen.io/lukelogrocket/embed/EJeqva?height=265&amp;theme-id=0&amp;default-tab=html%2Cresult&amp;user=lukelogrocket&amp;slug-hash=EJeqva&amp;pen-title=SVG%20-%204&amp;name=cp_embed_4" scrolling="no" frameborder="0" height="265" allowtransparency="true" allowfullscreen="true" allowpaymentrequest="true" title="SVG - 4" class="cp_embed_iframe " style="width: 100%; overflow:hidden; display:block;" id="cp_embed_EJeqva"></iframe>

## 哪些可以用CSS动画？

实际上很多！ 具有可随时间变化的值的CSS属性都可以使用CSS动画或CSS过渡进行动画处理（有关这些属性的完整列表，请参阅MDN Web Doc的[CSS动画属性列表](https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_animated_properties)）。 以下是一些Demo演示。

## Demos

我们将介绍两种主要类型的动画，它们根据提供的控制量而有所不同。 注意：我将在demo中使用`Sass`，当然它也适用于CSS。 另外，为了简单起见，我省略了浏览器前缀，可能你实际项目中需要添加这些前缀（稍后会详细介绍）。

### 过度属性

你可以在加载时触发的动画或通过状态更改（例如`hover`或`click`）时使用过渡属性。 `transition`属性允许属性值在指定的持续时间内平滑变化。 没有它，这种变化会在瞬间发生，从而产生一种唐突的现象。

#### `Transition` 属性

```css
transition: property duration timing-function delay;
```

值 | 描述
---|---
transition |	一次性设置所有四个的简写属性
transition-delay |	过度效果的延迟（以秒为单位）
transition-duration | 过度效果完成所需的秒数或毫秒数
transition-property | 指定过度效果作用的CSS属性的名称
transition-timing-function | 过渡效果的速度曲线

#### 悬停时变换的示例

<iframe name="cp_embed_5" src="https://codepen.io/hopearmstrong/embed/LrQyRG?height=265&amp;theme-id=0&amp;default-tab=html%2Cresult&amp;user=hopearmstrong&amp;slug-hash=LrQyRG&amp;pen-title=SVG%20Donut%20Animated%20on%20Hover%20with%20CSS%20%2F%20Sass&amp;name=cp_embed_5" scrolling="no" frameborder="0" height="265" allowtransparency="true" allowfullscreen="true" allowpaymentrequest="true" title="SVG Donut Animated on Hover with CSS / Sass" class="cp_embed_iframe " style="width: 100%; overflow:hidden; display:block;" id="cp_embed_LrQyRG"></iframe>

这款使用了过度效果的迷幻甜甜圈可以让变色更加好看自然！ `#donut-icing`元素的转换告诉填充使用`ease-out`淡出缓动函数在三秒内逐渐改变。 鼠标悬停状态会触发填充变为蓝色。 中间发生的是一种很酷的混色，有一点点紫色。

### 动画属性

过渡属性的限制是它不能很好地控制在时间线期间发生的变化。 对于从A点到B点简单的动画来说更好。为了进一步控制，请使用`Animation`属性。 这些属性可以单独使用，但我这里演示的是简写动画属性。

#### `Animation` 属性

```
animation: name duration timing-function delay iteration-count direction fill-mode play-state;
```

值 | 描述
---|---
animation-name | 动画关键帧的名称。当您阅读下面的关键帧部分时，请留意这一点。
animation-duration | 动画的长度，以秒或毫秒为单位。注意：请必须指定`animation-duration`属性。否则持续时间默认为0，永远不会播放。
animation-timing-function | 动画的速度曲线。例如：`linear`, `ease`, `ease-in`, `ease-out`, `ease-in-out`, `step-start`, `step-end`, `steps(int,start|end)`, `cubic-bezier(n,n,n,n)`, `initial`, `inherit`
animation-delay | 以秒（s）或毫秒（ms）为单位，它是动画开始之前的延迟时间。注意：如果给定负值，它将立即播放，就像它已经播放了一段时间一样。
animation-iteration-count | 应播放动画的次数。例如：任何数字
animation-direction | 以反向或交替循环播放动画。例如：`normal`, `reverse`, `alternate`, `alternate-reverse`, `initial`, `inherit`
animation-fill-mode | 动画在执行之前和之后如何将样式应用于其目标，比如`forwards`、`backwards`、`both`
animation-play-state | 指定动画是正在运行还是暂停状态
initial | 默认值
inherit | 从父级继承

#### 关键帧（Keyframes）

这是令人兴奋的地方，这就是在时序控制方面将动画与过渡属性区分开来的原因。 使用`@keyframes` 声明告诉它如何在中间步骤进行更改。 要使用关键帧，请添加一个`@keyframes` 声明，其名称与所需的`animation-name`属性相匹配。 使用关键帧选择器指定应在何时进行更改的动画时间轴上的百分比。

下面是一个显示百分比选择器的示例：

```sass
@keyframes name-goes-here
  0%
    width: 100px
  25%
    width: 120px
  50%, 75%
    width: 130px
  100%
    width: 110px
```

如果要仅为开头和结尾创建关键帧，可以这样做：

```sass
@keyframes name-goes-here
  from
    width: 100px
  to
    width: 120px
```

虽然关键帧可以在你将它们放入样式表的任何位置运行，但它们通常位于动画属性下方，可以轻松找到它们的引用。


### 变换（Transforms）

元素可以在二维或三维空间中动画化。 在这里，我将展示一些2D变换的例子。 要了解有关3D变换的更多信息，[请查看noob的3D变换指南](https://blog.logrocket.com/the-noobs-guide-to-3d-transforms-with-css-7370aafd9edf/)。

#### 旋转（Rotating）

<iframe name="cp_embed_6" src="https://codepen.io/hopearmstrong/embed/ZZYgMe?height=265&amp;theme-id=0&amp;default-tab=html%2Cresult&amp;user=hopearmstrong&amp;slug-hash=ZZYgMe&amp;pen-title=Rotating%20Spinner%20SVG%20Icon%20Animated%20with%20CSS%20%2F%20Sass&amp;name=cp_embed_6" scrolling="no" frameborder="0" height="265" allowtransparency="true" allowfullscreen="true" allowpaymentrequest="true" title="Rotating Spinner SVG Icon Animated with CSS / Sass" class="cp_embed_iframe " style="width: 100%; overflow:hidden; display:block;" id="cp_embed_ZZYgMe"></iframe>


这是一个使用旋转变换的加载图标。想知道它是如何制作的吗？它从一个看起来像带有黑暗象限环的SVG开始的。

#### HTML

<iframe name="cp_embed_7" src="https://codepen.io/lukelogrocket/embed/rbZXra?height=265&amp;theme-id=0&amp;default-tab=html%2Cresult&amp;user=lukelogrocket&amp;slug-hash=rbZXra&amp;pen-title=SVG%20-%205&amp;name=cp_embed_7" scrolling="no" frameborder="0" height="265" allowtransparency="true" allowfullscreen="true" allowpaymentrequest="true" title="SVG - 5" class="cp_embed_iframe " style="width: 100%; overflow:hidden; display:block;" id="cp_embed_rbZXra"></iframe>

在Sass中，以SVG标签的ID为目标。 然后给它定义动画和过渡。 动画引用`@keyframes`的名称，其中`transform：rotate`设置为从0度到360度（完整旋转一圈）。 这就是让这加载图标变得动起来所需要的一切！

#### Sass

```sass
#loading
  animation: loading-spinner 1s linear infinite

@keyframes loading-spinner
  from
    transform: rotate(0deg)
  to
    transform: rotate(360deg)
```

想要动画效果更平滑顺畅吗？ SVG支持渐变，因此你可以使用相同的Sass实现更平滑的效果，但需要使用具有渐变圆环的SVG（请参阅下面定义为`#spinner-gradient-a`）。

<iframe name="cp_embed_8" src="https://codepen.io/hopearmstrong/embed/yrymwg?height=265&amp;theme-id=0&amp;default-tab=html%2Cresult&amp;user=hopearmstrong&amp;slug-hash=yrymwg&amp;pen-title=Rotating%20Loading%20SVG%20Icon%20with%20Gradient%20Animated%20with%20CSS%20%2F%20Sass&amp;name=cp_embed_8" scrolling="no" frameborder="0" height="265" allowtransparency="true" allowfullscreen="true" allowpaymentrequest="true" title="Rotating Loading SVG Icon with Gradient Animated with CSS / Sass" class="cp_embed_iframe " style="width: 100%; overflow:hidden; display:block;" id="cp_embed_yrymwg"></iframe>

现在，让我们使用`transform：scale`来创建一个变形条加载图标。

<iframe name="cp_embed_9" src="https://codepen.io/hopearmstrong/embed/VNYoNq?height=265&amp;theme-id=0&amp;default-tab=css%2Cresult&amp;user=hopearmstrong&amp;slug-hash=VNYoNq&amp;pen-title=SVG%20Loading%20Bars%20Animated%20with%20CSS%20%2F%20Sass&amp;name=cp_embed_9" scrolling="no" frameborder="0" height="265" allowtransparency="true" allowfullscreen="true" allowpaymentrequest="true" title="SVG Loading Bars Animated with CSS / Sass" class="cp_embed_iframe " style="width: 100%; overflow:hidden; display:block;" id="cp_embed_VNYoNq"></iframe>

SVG由三个大小相等的矩形组成，均匀分布。给SVG和三个`<rect>`元素都添加了ID，因此可以使用Sass轻松定位它们。

Sass将动画应用于每个矩形。 关键帧告诉矩形图在时间轴中的四个位置沿Y轴改变比例：开始时，四分之一，中途，然后是四分之三。动画中的第一个数字表示动画时间长度，而第二个表示设置延迟。 由于我希望这些矩形在不同时间变形，我为每个条形添加了不同的延迟。

```sass
#loading-bar
  &-left
    animation: loading-bar-morph 1s linear .1s infinite
    transform-origin: center
  &-middle
    animation: loading-bar-morph 1s linear .2s infinite
    transform-origin: center
  &-right
    animation: loading-bar-morph 1s linear .4s infinite
    transform-origin: center

@keyframes loading-bar-morph 
  0%
    transform: scaleY(1)
  25%
    transform: scaleY(0.3)
  50%
    transform: scaleY(0.7)
  75%
    transform: scaleY(0.15)
```

#### 变换原点

请注意，`transform-origin：center`表示从条形中心开始缩放; 否则，它会从顶部向下扩展，看起来就像是钻杆钻入地面。 测试一下，你会明白我的意思。 这是一个值得学习的重要课程：默认情况下，SVG原点位于左上角的（0,0）点。 如果你习惯使用HTML元素，这是一个关键的区别，HTML元素的默认变换原点始终为（50％，50％）。

### Fancier techniques

**线描动画**

<iframe name="cp_embed_11" src="https://codepen.io/hopearmstrong/embed/BENBzj?height=265&amp;theme-id=0&amp;default-tab=html%2Cresult&amp;user=hopearmstrong&amp;slug-hash=BENBzj&amp;pen-title=Line%20Drawing%20Animation%20(SVG%20and%20CSS%20%2F%20Sass)%20&amp;name=cp_embed_11" scrolling="no" frameborder="0" height="265" allowtransparency="true" allowfullscreen="true" allowpaymentrequest="true" title="Line Drawing Animation (SVG and CSS / Sass) " class="cp_embed_iframe " style="width: 100%; overflow:hidden; display:block;" id="cp_embed_BENBzj"></iframe>


这种漂亮的效果使你的SVG看起来就像被画出来一样。要得到上面的效果需要一个画线的SVG标签，因为它依赖于笔画。我将引导你了解如何用一条线完成它，然后你就知道如何完成剩下的工作。


首先，使用`stroke-dasharray`对行进行虚线描边。数值表示以px为单位的线的长度。

```sass
#line
  stroke-dasharray: 497
```

然后添加`stroke-dashoffset`指定dash模式到路径开始的距离。让它尽可能得长使它看起来像一条实线。这是动画的最终帧的外观。

```sass
#line
  stroke-dasharray: 497
  stroke-dashoffset: 497
```

现在它已经准备好可以动起来了。 添加为`stroke-dashoffset`设置动画的关键帧，使其从完全偏移（无可用笔划）变为0px偏移（实心笔划）。 注意动画属性中的`forwards`。 这是`animation-fill-mode`的其中一种取值，它告诉动画一旦播放就会保持最终结束状态。 没有它，动画将播放然后返回其第一个“帧”作为其最终的结束点。

```sass
#line
  stroke-dasharray: 497
  stroke-dashoffset: 497
  animation: draw 1400ms ease-in-out 4ms forwards

@keyframes draw
  from
    stroke-dashoffset: 1000
  to
    stroke-dashoffset: 0
```

#### 插图动画

下面这个带着笑脸的心形，在鼠标悬停时会触发一些动画。 心形的大小变化为110％，而上面笑脸的眼睛变小，嘴巴变大并出现红晕，心形出现脉冲效果动画。 对于脉冲效果，我使用了[Animista](http://animista.net/)的Heartbeat动画。 Animista是开发CSS动画效果的绝佳资源，你可以重复使用和迭代。

<iframe name="cp_embed_12" src="https://codepen.io/hopearmstrong/embed/eoNYQY?height=265&amp;theme-id=0&amp;default-tab=css%2Cresult&amp;user=hopearmstrong&amp;slug-hash=eoNYQY&amp;pen-title=Beating%20Heart%20SVG%20Animated%20on%20Hover%20with%20CSS%20%2F%20Sass&amp;name=cp_embed_12" scrolling="no" frameborder="0" height="265" allowtransparency="true" allowfullscreen="true" allowpaymentrequest="true" title="Beating Heart SVG Animated on Hover with CSS / Sass" class="cp_embed_iframe " style="width: 100%; overflow:hidden; display:block;" id="cp_embed_eoNYQY"></iframe>

```sass
#smiley-love
  #smiley
    &-blush
      display: none
  a
    display: inline-block
    &:hover
      #smiley
        transform: scale(1.1)
        transform-origin: center
        -webkit-animation: heartbeat 1.5s ease-in-out infinite both
        animation: heartbeat 1.5s ease-in-out infinite both
        &-blush
          display: inherit
        &-eye-left
          transform-origin: center
          transform: scale(.7) translate(-8px)
        &-eye-right
          transform-origin: center
          transform: scale(.7) translate(8px)
        &-mouth
          transform: translateY(-22px) scale(1.6)
          transform-origin: center

/* ----------------------------------------------
 * animation heartbeat
 * Generated by Animista on 2019-3-24 18:51:13
 * w: http://animista.net, t: @cssanimista
 * ---------------------------------------------- */

@-webkit-keyframes heartbeat
  from
    -webkit-transform: scale(1)
            transform: scale(1)
    -webkit-transform-origin: center center
            transform-origin: center center
    -webkit-animation-timing-function: ease-out
            animation-timing-function: ease-out
  10%
    -webkit-transform: scale(0.91)
            transform: scale(0.91)
    -webkit-animation-timing-function: ease-in
            animation-timing-function: ease-in
  17%
    -webkit-transform: scale(0.98)
            transform: scale(0.98)
    -webkit-animation-timing-function: ease-out
            animation-timing-function: ease-out
  33%
    -webkit-transform: scale(0.87)
            transform: scale(0.87)
    -webkit-animation-timing-function: ease-in
            animation-timing-function: ease-in
  45%
    -webkit-transform: scale(1)
            transform: scale(1)
    -webkit-animation-timing-function: ease-out
            animation-timing-function: ease-out
@keyframes heartbeat
  from
    -webkit-transform: scale(1)
            transform: scale(1)
    -webkit-transform-origin: center center
            transform-origin: center center
    -webkit-animation-timing-function: ease-out
            animation-timing-function: ease-out
  10%
    -webkit-transform: scale(0.91)
            transform: scale(0.91)
    -webkit-animation-timing-function: ease-in
            animation-timing-function: ease-in
  17%
    -webkit-transform: scale(0.98)
            transform: scale(0.98)
    -webkit-animation-timing-function: ease-out
            animation-timing-function: ease-out
  33%
    -webkit-transform: scale(0.87)
            transform: scale(0.87)
    -webkit-animation-timing-function: ease-in
            animation-timing-function: ease-in
  45%
    -webkit-transform: scale(1)
            transform: scale(1)
    -webkit-animation-timing-function: ease-out
            animation-timing-function: ease-out
```

对于下面这个冰棒图，我通过使用`transform：translate`来改变它们的位置。 为了让它们消失，我设置了透明度变化。 现在它看起来像是炎热夏日里的冰棒在融化！

<iframe name="cp_embed_13" src="https://codepen.io/hopearmstrong/embed/qwdBwR?height=265&amp;theme-id=0&amp;default-tab=html%2Cresult&amp;user=hopearmstrong&amp;slug-hash=qwdBwR&amp;pen-title=Melting%20Popsicle%20SVG%20Animated%20with%20CSS%20%2F%20Sass&amp;name=cp_embed_13" scrolling="no" frameborder="0" height="265" allowtransparency="true" allowfullscreen="true" allowpaymentrequest="true" title="Melting Popsicle SVG Animated with CSS / Sass" class="cp_embed_iframe " style="width: 100%; overflow:hidden; display:block;" id="cp_embed_qwdBwR"></iframe>

#### 插件

你可以使用前面提到的CSS / Sass自己动手，或者使用像[Animate.CSS](https://daneden.github.io/animate.css/)这样的插件来快速开发。 它包含常用动画的现成的样式类，例如淡入淡出，幻灯片，摇动等等。 如果您想探索JavaScript的方式，我听说过Greensock的[GSAP](https://greensock.com/gsap)是很棒的东西，它有一个强大的插件叫做MorphSVGPlugin，可以让你将SVG形状变形为另一种形状。

### 跨浏览器兼容性

许多CSS动画在不同的浏览器都得到了很好的支持，但仍有一些事情需要注意。 比如：

#### 浏览器前缀
您可以在[shouldiprefix.com](http://shouldiprefix.com/)检查确认是否需要加上针对特定浏览器的前缀。在撰写本文时，建议你都加上`-webkit-animation`和`@ -webkit-keyframes`前缀。


#### 浏览器测试

请记住，尽管有大部分浏览器支持，但你可能会遇到一些渲染差异。 例如，如果你想支持旧版本的Firefox（v.42及更低版本），请留意有关`transform-origin`的兼容问题。 虽然Firefox最新版现在已经修复，但有一段时间不接受任何关键字（例如`center`）或变换原点的百分比。 因此，如果你遇到渲染问题，请尝试使用px。 你可以查看 `cx` 和 `cy` 属性来计算原点中心。 要在多个浏览器和设备上查找渲染差异，可以在[BrowserStack](https://www.browserstack.com/)上测试你的动画以查找任何奇怪的问题。Chrome DevTools有一个动画选项卡，有助于深入了解动画状态。 它允许你通过时间轴可视化来查看页面上动画执行情况，也可以慢动作重放动画，以及修改动画。

![Chrome DevTools](https://storage.googleapis.com/blog-images-backup/0*YQLulhc9UsTKXK0L)

### 总结

现在你已经了解了使用CSS为SVG制作动画的几种不同方法，希望你能够为自己的网页创建动画！而只需几行CSS代码就能将静态SVG变得动起来，这是很有趣的。 一旦掌握了一些技巧，就可以更轻松地处理更复杂的动画。 一些在线编辑器比如[CodePen](https://codepen.io/)等有很大有趣的动画，可以给你带来无穷无尽的灵感！




