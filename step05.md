#XHR和依赖性注入

在硬编码的数据集中有三款手机的数据，建立一个应用程序足够了！让我们使用Angular内建的[服务](https://docs.angularjs.org/guide/services)之一，[$http](https://docs.angularjs.org/api/ng/service/$http)从服务器上取得更大的数据集我们将使用Angular的[依赖性注入（DI）](https://docs.angularjs.org/guide/di)来为`PhoneListCtrl`控制器提供服务。

* 现在有一个20个电话的列表，从服务器载入。

把工作空间重置到第五步

```
git checkout -f step-5
```
刷新你的浏览器或在线检查这一步：[Step 5 Live Demo](http://angular.github.io/angular-phonecat/step-5/app)

下面列出了第四步和第五步之间的最重要的区别。你可以在[GitHub](https://github.com/angular/angular-phonecat/compare/step-4...step-5)里看到完整的差异。

## 数据
在你的项目中，`app/phones/phones.json`文件是一个数据集，包含了一个更大的手机列表，以JSON格式存储。

遵照以下文件示例：

```js
[
 {
  "age": 13,
  "id": "motorola-defy-with-motoblur",
  "name": "Motorola DEFY\u2122 with MOTOBLUR\u2122",
  "snippet": "Are you ready for everything life throws your way?"
  ...
 },
...
]
```


## 控制器

我们将在控制器中使用Angular的[$http](https://docs.angularjs.org/api/ng/service/$http)服务向你的Web服务器发出HTTP请求，取回`app/phones/phones.json`文件中的数据。`$http`是几个用Web应用中来处理常见的操作的内建[Angular服务](https://docs.angularjs.org/guide/services)之一。Angular在你需要的地方为你注入了这些服务。

Angular的[DI子系统](https://docs.angularjs.org/guide/di)负责管理这些服务。依赖性注入有用助于你的web应用既结构完好（例如，分离表现层、数据和控制三者）以及松弛的耦合（不能由组件自身解决的组件之间的依赖性问题，由DI子系统解决）。

**`app/js/controllers.js:`**

```js
var phonecatApp = angular.module('phonecatApp', []);

phonecatApp.controller('PhoneListCtrl', function ($scope, $http) {
  $http.get('phones/phones.json').success(function(data) {
    $scope.phones = data;
  });

  $scope.orderProp = 'age';
});
```

`$http`向你的Web服务器发出一个HTTP GET请求，要求`phones/phones.json`（该url相对于我们的`index.html`文件）。服务器在json文件中提供该数据，以响应该请求。（响应可能是由后端服务器动态生成的。但是在浏览器和我们的应用看来，它们没什么不同。为了简单起见，我们在本教程中使用了一个json文件。）

该`$http`服务返回了一个[promise对象](https://docs.angularjs.org/api/ng/service/$q)?，带有`success`方法。我们调用这个方法以处理异步响应，并假定该作用域的手机数据由该控制器控制，作为一个模块，称为`phones`。注意Angular侦测了该json响应，并为我们解析了它。

要想在Angular中使用一个服务，你只要声明你所需要的依赖性的名字，作为控制器的构造函数的参数，如下所示：

```
phonecatApp.controller('PhoneListCtrl', function ($scope, $http) {...}
```

在构造控制器时，Angular的依赖性注入器会把这些服务注入到你的控制器中。这些依赖性控制器还负责创建该服务可能需要的任何传递依赖性（一个服务通常会依赖于其它服务）。

注意，参数的名称非常重要，因为注入器会用这些名称去查阅依赖性。

<img  class="diagram" src="https://docs.angularjs.org/img/tutorial/tutorial_05.png">

### `$`前缀名称约定

你可以创建你自己的服务，而且实际上我们将在[第十一步](step11.html)做这个。作为一个命名约定，Angular的内建服务，作用域方法以及一些别的Angular API在命名前面使用一个`$`前缀。

Angular提供的服务的命名空间有`$`前缀。要想避免冲突，最好避免把你的服务和模块命名成带有`$`前缀。

如果你检查一个作用域，你可能还会注意到一些属性以`$$`开头。这些属性被视为是私有属性，不能访问或者修改。

### 在极简化上的一个注记

因为Angular从参数的名称调用控制器的依赖性到控制器构造器的函数，如果你打算为`PhoneListCtrl`控制器[缩小](http://goo.gl/SAnnsm)JavaScript代码，所有的函数参数都会被压缩，而且依赖性注入器将不能正确的识别服务。

我们可以克服这个问题，通过用依赖性的名称注释这个函数，作为字符串提供，它不会被压缩。提供这种注入注释有两种方法：

* 在控制器函数中创建一个`$inject`属性，它可携带一个字符串数组。在数组中的每个字符串都是要注入到对应的参数上的服务的名称。我们可以在自己的示例中这样写：

  ```js
      function PhoneListCtrl($scope, $http) {...}
      PhoneListCtrl.$inject = ['$scope', '$http'];
      phonecatApp.controller('PhoneListCtrl', PhoneListCtrl);
  ```

* 在那里使用一个内联注释，并非是只提供这个函数，你还提供了一个数组。这个数组包含了一系列服务名称，后跟着函数本身。

  ```js
      function PhoneListCtrl($scope, $http) {...}
      phonecatApp.controller('PhoneListCtrl', ['$scope', '$http', PhoneListCtrl]);
  ```

两种方法都能与Angular注入的任何函数完美协作，因此要选用哪种方法完全取决于你的项目的编程风格。

如果使用第二种方法，在注册控制器时，通常以匿名函数的形式提供内联的构造器函数。

```js
    phonecatApp.controller('PhoneListCtrl', ['$scope', '$http', function($scope, $http) {...}]);
```

从此刻开始，我们将在本教程中使用内联方法。考虑到这一点，让我们把注释加到`PhoneListCtrl`上：

**`app/js/controllers.js:`**

```js
var phonecatApp = angular.module('phonecatApp', []);

phonecatApp.controller('PhoneListCtrl', ['$scope', '$http',
  function ($scope, $http) {
    $http.get('phones/phones.json').success(function(data) {
      $scope.phones = data;
    });

    $scope.orderProp = 'age';
  }]);
```

## 测试

**`test/unit/controllersSpec.js`:**

因为我们开始使用依赖性注入，而且我们的控制器包含了依赖性，在我们的测试中构造控制器就变得有点复杂了。我们可以使用`new`操作符，并提供带有某种假的`$http`实现的构造器。然而，Angular提供了一个模拟`$http`服务，我们可以用在单元测试中。我们通过调用一个称为`$httpBackend`服务上的方法，为服务器请求配置了“假的”响应。

```js
describe('PhoneCat controllers', function() {

  describe('PhoneListCtrl', function(){
    var scope, ctrl, $httpBackend;

    // 在每次测试之前载入我们的应用模块定义
    beforeEach(module('phonecatApp'));

    // 注入器会忽略前面和后面的下划线（例如_$httpBackend_）。
    // 这允许我们注入一个服务，然后把它附加到同名变量上，以避免名称冲突
    beforeEach(inject(function(_$httpBackend_, $rootScope, $controller) {
      $httpBackend = _$httpBackend_;
      $httpBackend.expectGET('phones/phones.json').
          respond([{name: 'Nexus S'}, {name: 'Motorola DROID'}]);

      scope = $rootScope.$new();
      ctrl = $controller('PhoneListCtrl', {$scope: scope});
    }));
```

注意：因为我们在测试环境中载入了Jasmine以及`angular-mocks.js`，我们得到了两个辅助方法[module](https://code.angularjs.org/1.1.0/docs/api/angular.mock.module)和[inject](https://code.angularjs.org/1.1.0/docs/api/angular.mock.inject)，用来访问和配置注入器。

我们在测试环境中创建控制器，如下所示：

* 我们使用`inject`辅助方法，向Jasmine的`beforeEach`函数注入[$rootScope](https://code.angularjs.org/1.1.0/docs/api/ng.$rootScope)、[$controller](https://code.angularjs.org/1.1.0/docs/api/ng.$controller)和[$httpBackend](https://code.angularjs.org/1.1.0/docs/api/ng.$httpBackend)服务的实例，这些实例来自于一个注入器，在每一个测试内部都会被重新创建这个注入器。这保证了每次测试都从一个众所周知的起点开始，每次测试与其它测试相互独立。
* 通过调用`$rootScope.$new()`来为我们的控制器创建一个新的作用域。
* 调用了已注入的`$controller`函数，以参数的形式传入`PhoneListCtrl`控制器的名称和创建范围。

因为我们的代码现在使用`$http`服务以取回我们的控制器中的手机列表数据，在我们创建`PhoneListCtrl`子作用域之前，我们需要告诉测试套件等待一个后面的请求，来自控制器。我们可以这样做：

* 请求把`$httpBackend`服务注入到我们的`beforeEach`函数中。这是一个在产品环境中的服务的模拟版本，可以响应各种XHR和JSONP请求。该服务的模拟版本允许你编写测试，不需要处理原生的API和与它相关的全局状态——本来这两者都会使测试变成一个噩梦。

* 使用`$httpBackend.expectGET`方法规定`$httpBackend`服务等待之后的HTTP请求，并告诉它如何响应它。注意，直到我们调用`$httpBackend.flush`方法，才会返回响应。

现在我们作了断言以核实在响应到达之前，作用域上不存在手机模块：

```js
    it('should create "phones" model with 2 phones fetched from xhr', function() {
      expect(scope.phones).toBeUndefined();
      $httpBackend.flush();

      expect(scope.phones).toEqual([{name: 'Nexus S'},
                                   {name: 'Motorola DROID'}]);
    });
```

* 通过调用`$httpBackend.flush()`，我们清空了浏览器中的请求队列。这导致`$http`服务返回的promise对象由规范的应答来处理。可以在[模拟$httpBackend](https://docs.angularjs.org/api/ngMock/service/$httpBackend)文档中了解为什么必须“清空HTTP请求”的完整解释。

* 我们制作了断言，核实作用域上已经有手机模块了。

最后，我们核实已经正确设置了`orderProp`的默认值。

```js
    it('should set the default value of orderProp model', function() {
      expect(scope.orderProp).toBe('age');
    });
```

现在在Karma标签卡中，你应该看到以下的输出：

<pre>Chrome 22.0: Executed 2 of 2 SUCCESS (0.028 secs / 0.007 secs)</pre>

# 实验

* 在`index.html`的底部，添加一个`<pre>{{phones | filter:query | orderBy:orderProp | json}}</pre>`绑定以查看以json格式显示的手机列表。
* 在`PhoneListCtrl`控制器中，通过限制手机的数量为列表的前五个来预处理http响应。在`$http`回调中使用以下的代码：

```
$scope.phones = data.splice(0, 5);
```

# 总结

现在你已经知道了使用Angular服务是多么容易（幸亏Angular的依赖性注入），前往[第六步 模板连接和图像](step06.html)，在那里你将添加一些手机的缩略图以及一些链接。
