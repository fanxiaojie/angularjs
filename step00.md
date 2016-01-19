#引导程序

在教程的这一步骤中，你将熟悉AngularJS phonecat应用程序的最重要的源代码文件。你还将学会如何开启开发服务器与angular-seed捆绑，并在浏览器中运行应用程序。

在你继续之前，请确定你已经设置好了你的开发环境，并安装了所有必要的依赖性，如[教程起步](getstart.html)所述。

在`angular-phonecat`目录中，运行以下命令：

```
git checkout -f step-0
```

这会把你的工作空间重置到教程应用的步骤0。

你必须为教程中每个后面的步骤重复这个操作，并改变这个数字，改成你正在学习的步骤对应的数字。这将导致你在你的工作目录中做的任何改变都丢失。

如果你还没有已经完成，你需要通过运行以下指令以安装依赖性：

```
npm install
```

要想查看运行在浏览器上的应用，打开一个单独的终端/命令行选项卡或者窗口，然后运行`npm start`以启动Web服务器。现在，为应用打开一个浏览器窗口并导航到<a href="http://localhost:8000/app/" target="_blank" title="Open app on localhost">`http://localhost:8000/app/`</a>。

注意，如果你在检查step-0之前已经运行了主控分支应用，此时你可能会在你的浏览器窗口中看到应用的缓存的主控版本。只需要点击刷新以重载这个页面。

你现在可以在你的浏览器中看到网页。它并不令人兴趣，但是足够了。

应用中显示了“Nothing here yet!”的HTML网页，由如下所示的HTML代码构成。代码包含了一些关键Angular元素，正是我们所需要学习的。

**`app/index.html`:**

```html
<!doctype html>
<html lang="en" ng-app>
<head>
  <meta charset="utf-8">
  <title>My HTML File</title>
  <link rel="stylesheet" href="bower_components/bootstrap/dist/css/bootstrap.css">
  <link rel="stylesheet" href="css/app.css">
  <script src="bower_components/angular/angular.js"></script>
</head>
<body>

  <p>Nothing here {{'yet' + '!'}}</p>

</body>
</html>
```

## 代码做了什么？

**`ng-app`指令：**

```
<html ng-app>
```

元素属性`ng-app`代表了一个Angular指令，命名为`ngApp`（Anguar为它的自定义元素属性使用了`spinal-case`，为实现这些元素的对应指令使用了`camelCase`）。该指令用来标记Angular必须考虑作为我们的应用的根元素的html元素。这给应用程序开发员自由余地，以告诉Angular是整个html网页还是只有部分网页必须被视为Angular应用程序。

**AngularJS脚本元素标记：**

```
<script src="bower_components/angular/angular.js">
```

这段代码下载了注册了一个回调函数的`angular`脚本，当包含的HTML网页下载完时，浏览器将执行这个回调函数。在执行这个回调函数时，Angular将查找[ngApp](https://docs.angularjs.org/api/ng/directive/ngApp)指令。如果Angular找到了这个指令，它将用应用程序DOM的根节点引导这个应用程序，在应用程序的DOM的元素上定义了`ngApp`指令。

**与表达式的双花括号绑定：**

```
Nothing here {{'yet' + '!'}}
```

该行演示了Angular的模板功能的两个核心功能：

  * 一个绑定，用双花括号表达`{{ }}`
  * 在该绑定中用到的简单表达式`'yet' + '!'`

这个绑定告诉了Angular，它必须估值一个表达式，并把结果插入到DOM的绑定位置。与我们在下一步中看到的一次性插入相比，一个绑定将导致高效的持续的更新，每当表达式估值的结果改变时就会有所更新。

[Angular表达式](https://docs.angularjs.org/guide/expression)是一个类似JavaScript的代码片段，根据当前模块作用域的上下文中的Angular估值，而不是根据在全局context (`window`)作用域中的Angular估值。

不出所料，一旦Angular处理完模板，html将包含文本："Nothing here yet!"。

## 引导AngularJS应用

引导的AngularJS应用自动使用了`ngApp`指令，十分容易而且适用于很多案例。在高级案例中，比如说当使用脚本载入器时，你可以使用[imperative / manual way](https://docs.angularjs.org/guide/bootstrap)?以引导该应用。

将在应用引导过程中发生三件重要的事情：

1. 创建依赖性注入将使用?[注入器](https://docs.angularjs.org/api/auto/service/$injector)。
2. 然后注入器创建了?[根作用域](https://docs.angularjs.org/api/ng/service/$rootScope)，它将成为我们的应用的模块的上下文。
3. 然后Angular将从`ngApp`根结点开始编译该DOM，处理任何指令，以及沿着这条路找到的绑定。

一旦应用程序引导完毕，它将监听接下来的可能改变模块的浏览器事件（比如说鼠标点击、键按下或者接下来的HTTP响应）。一旦发生了这样的事件，Angular会发现它是否导致了任何模块的改变，如果发现了改变，Angular将通过更新所有受影响的绑定，在视图中反映它们。

应用程序的结构当前十分简单。模板只包含了一行指令，以及一个静态绑定，而且我们的模块是空的。很快就会有所改变了！

<img class="diagram" src="https://docs.angularjs.org/img/tutorial/tutorial_00.png">

## 我的工作目录中的文件有什么作用？

你的工作目录中的大多数文件来自[angular种子项目][angular-seed]，它们用来引导新的Angular项目。种子项目是预配置的，以安装该angular框架（通过`bower`到`app/bower_components/`目录）和用于开发一个典型的web应用的工具（通过`npm`）。

对于本教程，我们对angular种子作了以下修改：

* 移除示例应用
* 把手机图像添加到`app/img/phones/`
* 把手机数据文件（JSON）添加到`app/phones`
* 在`bower.json`文件中添加[Bootstrap](http://getbootstrap.com)上的依赖性。

# 实验

* 尝试给`index.html`添加一个新的表达式，它会做算术：

```
<p>1 + 2 = {{ 1 + 2 }}</p>
```

# 总结

现在让我们前往[第一步 静态模板](step01.html)并给web应用添加一些内容。

<ul doc-tutorial-nav="0"></ul>

[angular-seed]: https://github.com/angular/angular-seed
