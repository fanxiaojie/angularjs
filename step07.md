#路由与多视图

在这一步中，你将学会如何通过使用被称为'ngRoute'的Angular模块添加路由，创建一个布局模板，以及如何绑定一个具有多视图的应用。

* 当你导航到`app/index.html`上时，你将跳车到`app/index.html/#/phones`，而且手机列表出现在浏览器中。
* 当你在手机链接上点击时，url变成特定的手机，出现了手机详情页。

把工作空间重置到第七步

```
git checkout -f step-7
```

刷新你的浏览器或在线检查这一步：[Step 7 Live Demo](http://angular.github.io/angular-phonecat/step-7/app)

下面列出了第六步和第七步之间的区别。你可以在[GitHub](https://github.com/angular/angular-phonecat/compare/step-6...step-7)里看到完整的差异。

## 依赖性

这一步中添加路由功能是由 `ngRoute`模块中的angular提供的，它与核心的Angular框架分离分布。

我们使用[Bower][bower]以安装客户端依赖性。这一步更新了`bower.json`配置文件，以包含新的依赖性：

```json
{
  "name": "angular-phonecat",
  "description": "A starter project for AngularJS",
  "version": "0.0.0",
  "homepage": "https://github.com/angular/angular-phonecat",
  "license": "MIT",
  "private": true,
  "dependencies": {
    "angular": "1.4.x",
    "angular-mocks": "1.4.x",
    "jquery": "~2.1.1",
    "bootstrap": "~3.1.1",
    "angular-route": "1.4.x"
  }
}
```

新的依赖性`"angular-route": "1.4.x"`告诉bower要安装与v1.4x兼容的angular-router组件版本。我们将告诉bower以下载并安装该依赖性。

如果你已经全局安装了bower，则你可以只对该项目运行`bower install`，我们已经预配置了npm，从而为我们运行bower安装：

```
npm install
```

## 多个视图、路由和布局模板

我们的应用渐渐地完善，变得越来越复杂。在第七步之前，应用向我们用户提供了单一视图（手机的列表），而且所有的模板代码都位于`index.html`文件中。构建应用的下一步是添加一个视图，这个视图将显示我们的列表中每款设备的详细信息。

要想添加详情视图，我们可以扩展`index.html`以包含两套视图的模板代码，但是那将很快变得混乱。因此我们不用这种方法，而是把`index.html`变成“布局模板”。这是一个模板，常用于我们应用中的所有视图。然后别的“局部布局模板”根据当前的“路由”包含到这个布局模板中，从而形成一个完整视图展示给用户。

通过[$routeProvider](https://docs.angularjs.org/api/ngRoute/provider/$routeProvider)来声明Angular中的应用程序路由，它是[$route服务](https://docs.angularjs.org/api/ngRoute/service/$route)的提供者。这个服务使接通控制器、视图模板以及浏览器中的当前位置变得容易。利用这个功能，我们可以实现[深链接](http://en.wikipedia.org/wiki/Deep_linking)，深链接让我们可以使用浏览器的历史（回退和前进导航）以及书签。

### 一条关于DI、注入器和提供者的提醒

如你[已注意到的](https://docs.angularjs.org/tutorial/step_05)，[依赖性注入](https://docs.angularjs.org/guide/di)（DI）是AngularJS的核心，所以对它的工作原理略知一二是很重要的。

在应用程序引导中，Angular创建了一个注入器，注入器用来寻找并注入你的应用所需要的所有的服务。注入器本身对`$http`或`$route`服务是做什么的一无所知。实际上，注入器甚至不知道这些服务是否存在，除非用适当的模板定义对它进行配置。

注入器只在以下步骤中出场：

 * 载入你在你的应用中指定的模块定义。
 * 注册所有的在模块定义中定义的提供者。
 * 当被要求做这的时候，注入一个指定的函数以及一些必要的依赖性（服务），它通过它们的提供者来惰性实例化。

提供者是提供（创建）服务实例并且对外提供配置API的对象，API可以用来控制一个服务的创建和运行时行为。对于`$route`来说，`$routeProvider`对外提供API，API允许你定义针对你的应用程序的路由。

<div class="alert alert-warning">
**注意：**只能够把提供者注入到`config`函数中。因此你不能够把`$routeProvider`注入到`PhoneListCtrl`中。
</div>

Angular模块解决了从应用程序中移除全局状态的问题，并提供配置注入器的方法。相对于AMD或require.js模块，Angular模块并不试图解决脚本载入次序问题或者懒惰式脚本取得问题。这些目标是完全独立的，两个模块系统可以并立存在，并实现他们的目标。

要想加深你对Angular上的DI的理解，请参看[理解依赖性注入](https://github.com/angular/angular.js/wiki/Understanding-Dependency-Injection)。

## 模板

`$route`服务常与[ngView](https://docs.angularjs.org/api/ngRoute/directive/ngView)指令结合使用。`ngView`指令的角色是在布局模板中包含用于当前路由的视图模板。这使它完美恰合我们的`index.html`模板。

<div class="alert alert-info">
**注意：**从AngularJS v1.2版开始，`ngRoute`在它自己的模块中，必须通过载入额外的`angular-route.js`文件来载入它，我们通过上面的Bower来下载`angular-route.js`文件。
</div>

**`app/index.html`:**

```html
<!doctype html>
<html lang="en" ng-app="phonecatApp">
<head>
...
  <script src="bower_components/angular/angular.js"></script>
  <script src="bower_components/angular-route/angular-route.js"></script>
  <script src="js/app.js"></script>
  <script src="js/controllers.js"></script>
</head>
<body>

  <div ng-view></div>

</body>
</html>
```

我们已经在我们的索引文件添加了两个新的`<script>`标记，从而把外部JavaScript文件载入到我们的应用程序中：

- `angular-route.js` : 定义Angular `ngRoute`模块，`ngRoute`模块向我们提供了路由。
- `app.js` : 现在这个文件控住了我们的应用程序的根模块。

注意：我们删除了`index.html`模板中的大部分代码，把它替换成一行代码，包含了一个带有元素属性`ng-view`的div。我们已经移除的这个代码被放到了`phone-list.html`模板中：

**`app/partials/phone-list.html`:**

```html
<div class="container-fluid">
  <div class="row">
    <div class="col-md-2">
      <!--Sidebar content-->

      Search: <input ng-model="query">
      Sort by:
      <select ng-model="orderProp">
        <option value="name">Alphabetical</option>
        <option value="age">Newest</option>
      </select>

    </div>
    <div class="col-md-10">
      <!--Body content-->

      <ul class="phones">
        <li ng-repeat="phone in phones | filter:query | orderBy:orderProp" class="thumbnail">
          <a href="#/phones/{{phone.id}}" class="thumb"><img ng-src="{{phone.imageUrl}}"></a>
          <a href="#/phones/{{phone.id}}">{{phone.name}}</a>
          <p>{{phone.snippet}}</p>
        </li>
      </ul>

    </div>
  </div>
</div>
```

<div style="display:none">
TODO!
<img  class="diagram" src="https://docs.angularjs.org/img/tutorial/tutorial_07_final.png">
</div>

我们还为手机详情视图添加了一个占位符模板：

**`app/partials/phone-detail.html`:**

```html
TBD: detail view for <span>{{phoneId}}</span>
```

注意，我们正在使用的`phoneId`表达式将在`PhoneDetailCtrl`控制器中定义。

## 应用模块

要想增强应用的组织，我们动用了Angular的`ngRoute`模块，我们已经把控制器移到它们自己的模块`phonecatControllers`中（如下所示）。

我们给`index.html`添加`angular-route.js`，并在`controllers.js`中创建一个新的`phonecatControllers`模块。然而，要想使用它们的代码，我们需要做的不止于此。我们还需要添加模块，作为我们的应用的依赖性。通过把两个应用作为`phonecatApp`的依赖性列表，我们可以使用这些指令以及它们提供的服务。


**`app/js/app.js`:**

```js
var phonecatApp = angular.module('phonecatApp', [
  'ngRoute',
  'phonecatControllers'
]);

...
```

注意第二个参数传递到`angular.module`,`['ngRoute','phonecatControllers']`。这个数组列出了`phonecatApp`所依赖的模块。

```js
...

phonecatApp.config(['$routeProvider',
  function($routeProvider) {
    $routeProvider.
      when('/phones', {
        templateUrl: 'partials/phone-list.html',
        controller: 'PhoneListCtrl'
      }).
      when('/phones/:phoneId', {
        templateUrl: 'partials/phone-detail.html',
        controller: 'PhoneDetailCtrl'
      }).
      otherwise({
        redirectTo: '/phones'
      });
  }]);
```

使用`phonecatApp.config()`方法，我们请求了`$routeProvider`，它会被注入到我们的配置函数中，并使用?[`$routeProvider.when()`](https://docs.angularjs.org/api/ngRoute/provider/$routeProvider#when)方法以定义我们的路由。

我们的应用程序路由定义如下：

* `when('/phones')`：当URL映射段为`/phones`的时候。将展示这个手机列表视图。要想构造这个视图，Angular将使用`phone-list.html`模板，以及`PhoneListCtrl`控制器。
* `when('/phones/:phoneId')`：当URL映射段匹配`/phones/:phoneId`的时候（其中`:phoneId`是URL的变量部分），将展示手机详情视图。要想构造手机详情视图，Angular将使用`phone-detail.html`模板以及`PhoneDetailCtrl`控制器。
* `otherwise({redirectTo: '/phones'})`：当浏览器的地址不匹配我们别的路由的时候，触发一个重定向到`/phones`。

我们再次使用我们在上一步中构造的`PhoneListCtrl`控制器，并为手机详情视图向`app/js/controllers.js`文件添加了一个新的、空的`PhoneDetailCtrl`控制器。

注意在第二个路由声明中`:phoneId`参数的使用。`$route`服务使用route声明`'/phones/:phoneId'`作为匹配当前URL的模板。所有用`:`记号法定义的变量都会提取出来，放到?[`$routeParams`](https://docs.angularjs.org/api/ngRoute/service/$routeParams)对象上。


## 控制器

**`app/js/controllers.js`:**

```js
var phonecatControllers = angular.module('phonecatControllers', []);

phonecatControllers.controller('PhoneListCtrl', ['$scope', '$http',
  function ($scope, $http) {
    $http.get('phones/phones.json').success(function(data) {
      $scope.phones = data;
    });

    $scope.orderProp = 'age';
  }]);

phonecatControllers.controller('PhoneDetailCtrl', ['$scope', '$routeParams',
  function($scope, $routeParams) {
    $scope.phoneId = $routeParams.phoneId;
  }]);
```

再次提醒注意，我们创建了一个新的模块，称为`phonecatControllers`。对于小型的AngularJS应用，通常针对所有的控制器只创建一个模板，如果控制器只有为数不多的几个。随着你的应用程序扩大，常常要把你的代码重构到额外的模块中。为了更大的应用，你可能将会想要为你的应用的所有的主要功能创建独立的模块。

因为我们的应用比较小，我们将把我们所有的控制器添加到`phonecatControllers`模块中。

## 测试

要想自动核查所有东西都正确连通了，我们编写了一个端到端的测试，导航到不同的URL上，并核查是否呈现了正确的视图。

```js
...
   it('should redirect index.html to index.html#/phones', function() {
    browser.get('app/index.html');
    browser.getLocationAbsUrl().then(function(url) {
        expect(url).toEqual('/phones');
      });
  });

  describe('Phone list view', function() {
    beforeEach(function() {
      browser.get('app/index.html#/phones');
    });
...

  describe('Phone detail view', function() {

    beforeEach(function() {
      browser.get('app/index.html#/phones/nexus-s');
    });


    it('should display placeholder page with phoneId', function() {
      expect(element(by.binding('phoneId')).getText()).toBe('nexus-s');
    });
  });
```

你现在可以再次运行`npm run protractor`来查看测试的运行。

# 实验

* 尝试添加一个绑定到`index.html`的`{{orderProp}}`，而且你将看到什么事也没有发生，哪怕你正在手机列表视图中。这是因为`orderProp`模块只有在`PhoneListCtrl`管理的作用域内是可见的，`PhoneListCtrl`与`<div ng-view>`元素关联。如果你在`phone-list.html`模板上添加同样的绑定，绑定将如你的预期运作起来。

<div style="display: none">
* 在`PhoneCatCtrl`中，创建一个带有`this.hero='Zoro'`的新模块，称为"hero"。在`PhoneListCtrl`中，让我们用`this.hero='Batman'`来遮蔽它。在`PhoneDetailCtrl`中，我们将使用`this.hero = "Captain Proton"`。然后 把`<p>hero = {{hero}}</p>`添加到全部三个模板`index.html`、`phone-list.html`和`phone-detail.html`上。打开应用，你将看到作用域继承以及模板属性遮蔽做了一些奇观。
</div>

# 总结

随着路由设置成功以及手机列表视力的实现，我们已经准备好前往[第八步  更多模板](step08.html)，以实现手机详情视图。

[bower]: http://bower.io
