#应用动画

在这最后一步中，我们将通过在我们之前创建的模板代码的顶部添加CSS和JavaScript动画丰富我们的手机分类网站应用。

* 我们现在使用`ngAnimate`模拟以启用动画，以贯穿这个应用。
* 我们还使用常用的`ng`指令以自动触发使动画接入的钩子。
* 发现一个应用之后，动画将在标准DOM操作之间运行，该标准DOM操作在给定的时间内发布在元素上（例如，在[`ngRepeat`](https://docs.angularjs.org/api/ng/directive/ngRepeat)上插入和移除节点，或在[`ngClass`](https://docs.angularjs.org/api/ng/directive/ngClass)上添加和移除类）。

把工作空间重置到第十二步

```
git checkout -f step-12
```
刷新你的浏览器或在线检查这一步：[Step 12 Live Demo](http://angular.github.io/angular-phonecat/step-12/app)

下面列出了第十一步和第十二步之间最重要的区别。你可以在[GitHub](http://angular.github.io/angular-phonecat/step-12/app "See diff on Github")上看到完整的差异。

## 依赖性

Angular在`ngAnimate`模块中提供动画功能，它与核心Angular框架分开发布。另外，我们将在项目中使用`jquery`以实现额外的JavaScript动画。

我们正在使用[Bower][bower]以安装客户侧依赖性。这一步更新了`bower.json`配置文件，以包含新的依赖性：

```
{
  "name": "angular-seed",
  "description": "A starter project for AngularJS",
  "version": "0.0.0",
  "homepage": "https://github.com/angular/angular-seed",
  "license": "MIT",
  "private": true,
  "dependencies": {
    "angular": "1.4.x",
    "angular-mocks": "1.4.x",
    "jquery": "~2.1.1",
    "bootstrap": "~3.1.1",
    "angular-route": "1.4.x",
    "angular-resource": "1.4.x",
    "angular-animate": "1.4.x"
  }
}
```

* `"angular-animate": "1.4.x"`告诉bower安装一个angular-animate组件的版本，与v1.4x版兼容。
* `"jquery": "~2.1.1"`告诉bower安装jQuery的v2.1.1版。注意这不是一个Angular库，它是标准jQuery库。我们可以使用bower来安装一个大作用域的第三方库。

我们必须要求bower以下载并安装依赖性运行以下指令实现它：

```
npm install
```

<div class="alert alert-warning">
  **警告：**如果在你上一次运行`npm install`之后已经发布了Angular的一个新版本，然后因为需要安装的angular.js版本之间的冲突，你可能在运行`bower install`指令时遇到问题。如果你遇到了这种问题，只需要在运行`npm install`之前先删除你的`app/bower_components`文件夹。
</div>

<div class="alert alert-info">
  **注意：**如果你已经全局安全了bower，你可以运行`bower install`，但是对于这个项目，我们已经预配置了运行`npm install`来运行bower。
</div>

## 动画如何与`ngAnimate`协作

要想知道动画如何与AngularJS协作，请先阅读?[AngularJS动画指南](https://docs.angularjs.org/guide/animations)。

## 模板

在HTML模板代码内部需要修改，以链接asset文件，它定义了动画以及`angular-animate.js`文件。该动画模块，即[`ngAnimate`](https://docs.angularjs.org/api/ngAnimate)，被定义在`angular-animate.js`内部，并包含了必要的代码，以使你的应用程序变得动感。

这里是在索引文件中需要修改的地方：

**`app/index.html`.**

```html
...
  <!-- for CSS Transitions and/or Keyframe Animations -->
  <link rel="stylesheet" href="css/animations.css">

  ...

  <!-- jQuery is used for JavaScript animations (include this before angular.js) -->
  <script src="bower_components/jquery/dist/jquery.js"></script>

  ...

  <!-- required module to enable animation support in AngularJS -->
  <script src="bower_components/angular-animate/angular-animate.js"></script>

  <!-- for JavaScript Animations -->
  <script src="js/animations.js"></script>

...
```

<div class="alert alert-error">
  **重要：**确保在使用Augular 1.4的时候，使用jQuery v2.1版或更新的版本；官方不支持jQuery v1.x版。确保在所有的AngularJS脚本之前载入jQuery，否则AugularJS不能侦测jQuery，而且动画将不会如预期那样起作用。
</div>

可以在CSS代码（`animations.css`）内中创建动画，也可以在JavaScript代码（`animations.js`）内部创建动画。但是在开始之前，让我们创建一个新模块，它使用ngAnimate模块，作为依赖性，就像我们之前用`ngResource`所作的。

## 模块和动画

**`app/js/animations.js`.**

```js
angular.module('phonecatAnimations', ['ngAnimate']);
  // ...
  // this module will later be used to define animations
  // ...
```

现在让我们把这个模块附加到我们的应用程序模块上……

**`app/js/app.js`.**

```js
// ...
angular.module('phonecatApp', [
  'ngRoute',

  'phonecatAnimations',
  'phonecatControllers',
  'phonecatFilters',
  'phonecatServices',
]);
// ...
```

现在，手机分类模块已经有动感了。让我们制作更多更多动画吧！

## 用CSS过渡动画让ngRepeat动起来。

我们将从这一步开始，把CSS过渡动画添加到出现在`phone-list.html`网页上的`ngRepeat`指令。首先让我们把一个额外的CSS类添加到我们的重复元素上，因此我们可以把它与我们的CSS动画代码连接。

**`app/partials/phone-list.html`.**

```html
<!--
  让我们改变重复器HTML，以包含一个新的CSS类，之后我们将用它实现动画：
-->
<ul class="phones">
  <li ng-repeat="phone in phones | filter:query | orderBy:orderProp"
      class="thumbnail phone-listing">
    <a href="#/phones/{{phone.id}}" class="thumb"><img ng-src="{{phone.imageUrl}}"></a>
    <a href="#/phones/{{phone.id}}">{{phone.name}}</a>
    <p>{{phone.snippet}}</p>
  </li>
</ul>

```

注意我们将如何添加`phone-listing`CSS类？这是我们让动画起作用，在HTML代码中所需要做的。

以下是实际的CSS过渡动画代码：

**`app/css/animations.css`**

```css
.phone-listing.ng-enter,
.phone-listing.ng-leave,
.phone-listing.ng-move {
  -webkit-transition: 0.5s linear all;
  -moz-transition: 0.5s linear all;
  -o-transition: 0.5s linear all;
  transition: 0.5s linear all;
}

.phone-listing.ng-enter,
.phone-listing.ng-move {
  opacity: 0;
  height: 0;
  overflow: hidden;
}

.phone-listing.ng-move.ng-move-active,
.phone-listing.ng-enter.ng-enter-active {
  opacity: 1;
  height: 120px;
}

.phone-listing.ng-leave {
  opacity: 1;
  overflow: hidden;
}

.phone-listing.ng-leave.ng-leave-active {
  opacity: 0;
  height: 0;
  padding-top: 0;
  padding-bottom: 0;
}
```

如你所见，我们的`phone-listing` CSS类与动画钩子相结合，当列表中插入项目或移除项目时，就会出现动画钩子。

  * 当列表中添加了一款新手机并呈现在网页上时，元素上应用了`ng-enter`类。
  * 当项目绕着列表移动时，元素上应用了`ng-move`类。
  * 当项目从列表中移除时，元素上应用了`ng-leave`类。

添加或删除手机列表项目取决于传递给元素属性`ng-repeat`的数据。比如，如果过滤器数据改变了，项目动画地加入或退出重复列表。

有些很重要的事情需要注意，当动画发生时，元素上添加了CSS类的两个集合：

  1. “开始”类代表动画开始时的样式。
  2. “激活”类代表动画结束时的样式。

开始类的名称是被激发的事件（就像`enter`、`move`或`leave`）的名称带上前缀`ng-`。所以一个`enter`事件将导致一个称为`ng-enter`类。

激活类名与开始类名相同，但是带了一个后缀`-active`。这两类CSS命名公约允许开发员精心制作一个动画，自始至终。

在我们上面的示例中，当元素添加到列表中时，该元素从**0**高度伸展到**120像素**高度；在从列表中移除之前，又收缩到**0像素**。同时还发生了一个渐现和渐消的效果。这里都是由CSS过渡动画处理的，CSS过渡动画声明在上面示例代码的顶部。

虽然大多数现代浏览器能很好地支持[CSS过渡](http://caniuse.com/#feat=css-transitions)和[CSS动画](http://caniuse.com/#feat=css-animation)。但是如果你想让动画与老旧的浏览器后向兼容，请考虑使用基于JavaScript的动画，将在下面详细讲解它。

## 用CSS关键帧动画让`ngView`动起来

接下来，让我们为在[`ngView`](https://docs.angularjs.org/api/ngRoute/directive/ngView)内部、路由之间的过渡添加一个动画。

首先，让我们给HTML添加一个新的CSS类，就像我们在上面的示例中所作的。这一次，不是使用`ng-repeat`元素，而是把它添加到包含了`ng-view`指令的元素上。为了做到这，我们需要对HTML代码做一些小的改变，从而我们可以对我们的动画，在视图改变之间的动画有更多的控制。

**`app/index.html`.**

```html
<div class="view-container">
  <div ng-view class="view-frame"></div>
</div>
```

利用这个改变，`ng-view`指令被嵌套在一个带有`view-container`CSS类的父元素内部。这个类添加了一个`position: relative`样式，因此动画过程中，`ng-view`的定位相对于这个父元素。

在这里，让我们为过渡动画添加CSS，添加到`animations.css`文件上：

**`app/css/animations.css`.**

```css
.view-container {
  position: relative;
}

.view-frame.ng-enter, .view-frame.ng-leave {
  background: white;
  position: absolute;
  top: 0;
  left: 0;
  right: 0;
}

.view-frame.ng-enter {
  -webkit-animation: 0.5s fade-in;
  -moz-animation: 0.5s fade-in;
  -o-animation: 0.5s fade-in;
  animation: 0.5s fade-in;
  z-index: 100;
}

.view-frame.ng-leave {
  -webkit-animation: 0.5s fade-out;
  -moz-animation: 0.5s fade-out;
  -o-animation: 0.5s fade-out;
  animation: 0.5s fade-out;
  z-index:99;
}

@keyframes fade-in {
  from { opacity: 0; }
  to { opacity: 1; }
}
@-moz-keyframes fade-in {
  from { opacity: 0; }
  to { opacity: 1; }
}
@-webkit-keyframes fade-in {
  from { opacity: 0; }
  to { opacity: 1; }
}

@keyframes fade-out {
  from { opacity: 1; }
  to { opacity: 0; }
}
@-moz-keyframes fade-out {
  from { opacity: 1; }
  to { opacity: 0; }
}
@-webkit-keyframes fade-out {
  from { opacity: 1; }
  to { opacity: 0; }
}

/* 别忘了供应商的前缀！ */
```

  没什么惊人的！仅仅是两页之间的一个简单的渐显和渐消效果。这里唯不寻常的东西是，在页面之间实现软切换动画的时候，我们在前一页（具有`ng-leave`类的页面）的上方使用绝对定位来定位下一页（通过 `ng-enter`来指定）。因此前一页即将被删除时，它是渐消淡出的，与此同时新页渐显现在它上面。

一旦离开动画结束，元素会被移除；一旦进入动画结束 ，元素上的`ng-enter`和`ng-enter-active` CSS类会被移除，导致它用它的默认CSS代码重新呈现、重新定位（因此没有一旦动画结束就没有绝对定位了）。这动作起来非常流畅，因此页面在路由变化时流动自然，不会有任何跳动感。

应用的CSS类（开始和结束类）与`ng-repeat`很相像。每当一个新页面载入到`ng-view`指令中时，将创建它自己的一个副本，下载模板并追加内容。这确保了所有的视图都包含在一个单独的HTML元素中，该元素允许简单的动画控制。

要想了解更多关于CSS动画的信息，请参阅[Web 平台文档](http://docs.webplatform.org/wiki/css/properties/animations)。


## 用JavaScript让`ngClass`动起来

让我们向应用程序添加另一个动画。切换到`phone-detail.html`网页，我们看到已经有一个很棒的缩略图交换器。通过点击在网页列列中的缩略图，资料手机图像就变了。但是我们可以如何在改变它的同时添加动画呢？

让我们先考虑一下。基本上，当你在一个资料图上点击时，你正在改变图像的状态，以反映新选中的缩略图。在HTML中指定状态改变的最佳方法是使用样式类。和以前很相像，我们使用的CSS样式类以指定指定一个动画，当CSS类本身变化时动画将发生。

当选中了一个新的手机缩略图时，状态改变了，`.active`CSS类添加到匹配的资料图像上，并播放了动画。

让我们开始，在`phone-detail.html`网贾上微调HTML代码。注意我们已经改变了显示大图像的方式：

**`app/partials/phone-detail.html`.**

```html
<!-- We're only changing the top of the file -->
<div class="phone-images">
  <img ng-src="{{img}}"
       class="phone"
       ng-repeat="img in phone.images"
       ng-class="{active:mainImageUrl==img}">
</div>

<h1>{{phone.name}}</h1>

<p>{{phone.description}}</p>

<ul class="phone-thumbs">
  <li ng-repeat="img in phone.images">
    <img ng-src="{{img}}" ng-mouseenter="setImage(img)">
  </li>
</ul>
```

就像缩略图，我们使用迭代器来显示**所有的**资料图像作为一个列表，然而我们没有变动任何迭代相关的动画。而是，我们在`ng-class`指令上保持关注，因为每当`active`类变成true，则它将应用到元素上，将呈现为可见。否则，资料图像将隐藏。在我们的案例中，总是有一个元素具有`active`类，因此，任何时候总会有一款手机的资料图像在屏幕上可见。

当元素上添加了激活类的时候，先添加了`active-add`类和`adtive-add-active`类，以指示Angular引发一个动画。当元素上移除了激活类的时候，元素上应用了`active-remove`类和`active-remove-active`，它们反过来又会触发别的动画。

要想确保手机图像在页面第一次加载时正确地显示，我们还要微调详情页的CSS样式：

**`app/css/app.css`**
```css
.phone-images {
  background-color: white;
  width: 450px;
  height: 450px;
  overflow: hidden;
  position: relative;
  float: left;
}

...

img.phone {
  float: left;
  margin-right: 3em;
  margin-bottom: 2em;
  background-color: white;
  padding: 2em;
  height: 400px;
  width: 400px;
  display: none;
}

img.phone:first-child {
  display: block;
  }
```


你可能认为我们将创建另一个CSS可用的动画。虽然我们可以那么做，但是还是让我们抓住机会学习如何用`abnimate`模块方法创建JavaScript可用的动画吧。

**`app/js/animations.js`.**

```js
var phonecatAnimations = angular.module('phonecatAnimations', ['ngAnimate']);

phonecatAnimations.animation('.phone', function() {

  var animateUp = function(element, className, done) {
    if(className != 'active') {
      return;
    }
    element.css({
      position: 'absolute',
      top: 500,
      left: 0,
      display: 'block'
    });

    jQuery(element).animate({
      top: 0
    }, done);

    return function(cancel) {
      if(cancel) {
        element.stop();
      }
    };
  }

  var animateDown = function(element, className, done) {
    if(className != 'active') {
      return;
    }
    element.css({
      position: 'absolute',
      left: 0,
      top: 0
    });

    jQuery(element).animate({
      top: -500
    }, done);

    return function(cancel) {
      if(cancel) {
        element.stop();
      }
    };
  }

  return {
    addClass: animateUp,
    removeClass: animateDown
  };
});
```

注意，我们正在使用[jQuery](http://jquery.com/)以实现这个动画。jQuery并不要求JavaScript动画与AngularJS协作，但是我们将使用它，因为编写你自己的JavaScript动画库超过了这个教程的范围。想要了解更多关于`jQuery.animate`的信息，请参阅[jQuery文档](http://api.jquery.com/animate/)。

包含我们注册过的类的元素，无论元素上添加了一个类还是移除了一个类，都会调用`addClass`回调函数和`removeClass`回调函数；在本案例中，注册过的类是`.phone`。当元素上添加了`.active`类（通过`ng-class`指令），将引发`addClass`JavaScript回调函数，该回调函数带有一个参数`element`。最后传入的参数是`done`回调函数。`done`回调函数的目的是，通过调用该函数，当JavaScript动画结束时，可以让Angular知道。

`removeClass`回调函数以同样的方式起作用，但是是在一个类从元素上移除时触发它。

在JavaScript回调函数中，你通过操纵DOM创建了该动画。在上面的代码中，这就是`element.css()`和`element.animate()`所做的事情。回调函数用`500px`的偏移定位了下一个元素，把前一个项目和新的项目往上移`500px`，使两个项目一起动起来。这导致了一个仿传送带的动画。当`animate`函数完成它的工作，它会调用 `done`。

注意`addClass`和`removeClass`两者都返回了一个函数。这是一个**可选的**函数，当动画被取消时（同一个元素上发生了别的动画）时或者动画完成时，会调用这个函数。向这个函数传递一个布尔参数，该参数让开发者知道动画是否被取消了。当动画完成时，这个函数可以用来做一些扫尾工作。


# 总结

现在你学会了！我们在相对短的时间里创建了一个web应用。在[完结篇](step13.html)中我们将讨论接下来何去何从。

[bower]: http://bower.io/
