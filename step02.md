#Angular模板

现在是时候用AngularJS制作动态网页了。我们将添加一个测试，验证用于控制器的代码，我们将添加这个控制器。

为应用程序构造代码有很多方式。针对Angular应用，我们鼓励使用[模块-视图-控制器(MVC)设计模式](http://en.wikipedia.org/wiki/Model–View–Controller)以解耦代码、分离关注点。考虑到这一点，我们使用小的Angular以及JavaScript为我们的应用添加模块、视图和控制器组件。

- 现在下面的数据中动态生成了三款手机的列表：

<div class="alert alert-info">

把工作空间重置到第二步

```
git checkout -f step-2
```
刷新你的浏览器或在线检查这一步：[Step 2 Live Demo](http://angular.github.io/angular-phonecat/step-2/app)

</div>

下面列出了第一步和第二步之间的最重要的区别。你可以在[GitHub](https://github.com/angular/angular-phonecat/compare/step-1...step-2)里看到完整的差异。

## 视图和模板

在Angular中，**视图**是模块透过HTML**模板**的映射。这意味着每当模块有变化时，Angular会刷新适当的绑定点，随之更新视图。

以下面代码为模板，Angular结构化了视图组件：

**`app/index.html`:**

```html
<html ng-app="phonecatApp">
<head>
  ...
  <script src="bower_components/angular/angular.js"></script>
  <script src="js/controllers.js"></script>
</head>
<body ng-controller="PhoneListCtrl">

  <ul>
    <li ng-repeat="phone in phones">
      <span>{{phone.name}}</span>
      <p>{{phone.snippet}}</p>
    </li>
  </ul>

</body>
</html>
```

我们用[ngRepeat指令](https://docs.angularjs.org/api/ng/directive/ngRepeat)和两个[Angular表达式](https://docs.angularjs.org/guide/expression)替代硬编码的手机列表：

* 在`<li>`元素标签上的元素属性`ng-repeat="phone in phones"`是一个Angular转发器指令。该转发器告诉Angular为列表中的每款使用元素标签`<li>`作为模板的手机创建一个`<li>`元素。
* 用花括号包围的表达式（`{{phone.name}}`和`{{phone.snippet}}`）将被替换成表达式的值。

我们已经添加了一个新指令，称为`ng-controller`，它给元素标签&lt;body&gt;附加了一个`PhoneListCtrl`**控制器**。在这个点上：

* 在花括号中的表达式（`{{phone.name}}`和`{{phone.snippet}}`）表示绑定，在我们的应用程序模块中参引它们，它们被设置在我们的`PhoneListCtrl`控制器上。

<div class="alert alert-info">
注意：我们已经指定了一个[Angular模块](https://docs.angularjs.org/api/ng/type/angular.Module)以载入使用`ng-app="phonecatApp"`，在那里，`phonecatApp`是我们的模块名。该模块将包含`PhoneListCtrl`。
</div>

<img class="diagram" src="https://docs.angularjs.org/img/tutorial/tutorial_02.png">

## 模块和控制器

数据**模块**（一个简单的手机数列，以对象字面记号法表达）现在在`PhoneListCtrl`**控制器**中实例化了。该**控制器**只是一个构造器函数，需要一个`$scope`参数：

**`app/js/controllers.js`:**

```js

var phonecatApp = angular.module('phonecatApp', []);

phonecatApp.controller('PhoneListCtrl', function ($scope) {
  $scope.phones = [
    {'name': 'Nexus S',
     'snippet': 'Fast just got faster with Nexus S.'},
    {'name': 'Motorola XOOM? with Wi-Fi',
     'snippet': 'The Next, Next Generation tablet.'},
    {'name': 'MOTOROLA XOOM?',
     'snippet': 'The Next, Next Generation tablet.'}
  ];
});

```

在这里，我们声明了一个控制器，称为`PhoneListCtrl`，并把它注册到一个AngularJS模块`PhonecatApp`中。注意，我们的`ng-app`指令（在元素标签`<html>`上）现在指定了`phonecatApp`模块名作为载入的模块，在引导应用Angular应用程序时载入该模块。

虽然控制器没有做太多的事情，但是它扮演了一个至关重要的角色。通过为我们的上下文提供数据模块，控制器允许我们在模块和视图之间建立数据绑定。我们在展示、数据和逻辑组件之间添加点状虚线，如下所示：

* 该[ngController指令](https://docs.angularjs.org/api/ng/directive/ngController)，定位在`<body>`元素标签上，引用了我们的控制器的名称，`PhoneListCtrl`（放置在JavaScript文件`controllers.js`上）。
* `PhoneListCtrl`控件在`$scope`上附加了手机数据，把它注入到我们的控制器函数中。该**作用域**是**根作用域**的原型化的后代，在定义应用程序的时候创建了该根作用域。该控制器作用域可以在元素标签`<body ng-controller="PhoneListCtrl">`内部的所有绑定位置上可用。

### 作用域

一个作用域的概念在Angular中是至关重要的。作用域可以被视为胶合剂，允许模板、模块和控制器一起工作。Angular使用作用域，以及模板、数据模块和控制器中包含的信息，以保持模块和视图分离，但是同步。任何对模块的改变会影响视图；任何在视图中发生的改变反应在模块中。

要想学习更多关于Angular作用域的知识，请参阅[angular作用域文档](https://docs.angularjs.org/api/ng/type/$rootScope.Scope)。

## 测试

从视图中分离控制器的“Angular方法”，使测试代码变得容易，就像是它在被开发那样。如果你的控制器在全局命名空间中可用，则我们可以用一个模拟的`scope`对象简单把它实例化：

**`test/e2e/scenarios.js`:**

```js
describe('PhoneListCtrl', function(){

  it('should create "phones" model with 3 phones', function() {
    var scope = {},
        ctrl = new PhoneListCtrl(scope);

    expect(scope.phones.length).toBe(3);
  });

});
```

测试实例化的`PhoneListCtrl`并在包含三个记录的作用域上核查手机数列属性。这个示例演示了为Angular中的代码创建一个单元测试是多么容易。因为测试是软件开发的如此至关重要的部分，我们让在Angular中创建测试变得容易，从而可以鼓励开发员编写它们。

### 测试非全局控制器

在实践中，你应该不想让你的控制器函数在全局命名空间内。取而代之的是，你可以看到我们已经利用一个`phonecatApp`模块上的匿名构造器函数注册了控制器。

在这种情况下，Angular提供了一个服务，`$controller`，它可以以名称接收你的控制器。这里有使用`$controller`同样的测试：

**`test/unit/controllersSpec.js`:**

```js
describe('PhoneListCtrl', function(){

  beforeEach(module('phonecatApp'));

  it('should create "phones" model with 3 phones', inject(function($controller) {
    var scope = {},
        ctrl = $controller('PhoneListCtrl', {$scope:scope});

    expect(scope.phones.length).toBe(3);
  }));

});
```

* 在每个测试开始之前，我们会告诉Angular要载入`phonecatApp`模块。
* 我们要求Angular把该`$controller`服务`inject`到我们的测试函数中。
* 我们使用`$controller`以创建一个`PhoneListCtrl`的实例。
* 利用这个实例，我们在包含三个记录的作用域上核查了手机数列属性。

### 编写并运行测试

Angular喜欢使用Jasmine的行为-驱动开发（BCC）的句法。虽然Angular没有要求你使用Jasmine，但是在这个教程中，我们用Jasmine v1.3编写所有的测试。你可以在[Jasmine官方首页][jasmine]和[Jasmine文档][jasmine-docs]中学习Jasmine。

angular-seed项目是预处理的，以使用[Karma][karma]运行单元测试，但是你将需要确保已经安装了Karma和它的必要的插件。你可以通过运行`rpm install`来做到这。

要想运行测试，请运行`rpm test`，然后观察文件有什么改变。

* Karma将自动开始一个Chrome和Firefox浏览器的新实例。只需要忽略它们，让它们在后台运行。Karma将为测试执行使用这些浏览器。
* 如果你已经在你的机器上安装了这些浏览器中的一个，确保在运行测试之前更新Karma的配置文件。本地配置文件在`test/karma.conf.js`，然后更新`browsers`属性。

  例如，如果你只安装了Chrome：
  <pre>
    ...
    browsers: ['Chrome'],
    ...
  </pre>

* 你将在终端看到以下或者类似的输出：

  <pre>
    info: Karma server started at http://localhost:9876/
    info (launcher): Starting  browser "Chrome"
    info (Chrome 22.0): Connected on socket id tPUm9DXcLHtZTKbAEO-n
    Chrome 22.0: Executed 1 of 1 SUCCESS (0.093 secs / 0.004 secs)
  </pre>

  耶！测试通过了！或者没有通过……

* 要想重新运行测试，只需要改变任何源或者test.js文件。Karma将注意到这些改变，并将为你重新运行测试。现在是不是甜？

<div class="alert alert-info">
确保你没有把Karma打开的浏览器最小化了。在一些操作系统中，分配到一个最小化的浏览器上的内存是有限的，导致你的karma测试运行变得极其缓慢。
</div>

# 实验

* 添加对`index.html`的另一个绑定。例如：

  ```html
  <p>Total number of phones: {{phones.length}}</p>
  ```

* 在控制器中创建一个新模块属性，然后从模板中把它绑定到模块上。例如：

  ```
  $scope.name = "World";
  ```

  然后向`index.html`添加一个新的绑定：

  ```
  <p>Hello, {{name}}!</p>
  ```

  刷新你的浏览器，核实它是否说了"Hello, World!"。

* 为`./test/unit/controllersSpec.js`中的控制器更新单元测试，以反映以前的变化。例如添加：

  ```
  expect(scope.name).toBe('World');
  ```

* 在`index.html`中创建一个重复器，它结构化了一个简单的表格：

  ```
  <table>
    <tr><th>row number</th></tr>
    <tr ng-repeat="i in [0, 1, 2, 3, 4, 5, 6, 7]"><td>{{i}}</td></tr>
  </table>
  ```

  现在，让这个基于1的列表的`i`在绑定中增值1。

  ```
  <table>
    <tr><th>row number</th></tr>
    <tr ng-repeat="i in [0, 1, 2, 3, 4, 5, 6, 7]"><td>{{i+1}}</td></tr>
  </table>
  ```

  另需指出：尝试并使一个8x8的表格使用一个额外的`ng-repeat`。

* 通过把`expect(scope.phones.length).toBe(3)`变成`toBe(4)`，使单元测试失败。

# 总结

现在你有了一个动态的应用，功能分离开模块、视力和控制器组件，而且你测试了它们。现在，让我们前往[第三步 筛选迭代器](step03.html)以学习如何为应用添加全文搜索。

<ul doc-tutorial-nav="2"></ul>

[jasmine]: http://jasmine.github.io/
[jasmine-docs]: http://jasmine.github.io/1.3/introduction.html
[karma]: http://karma-runner.github.io/
