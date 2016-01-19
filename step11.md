#REST和自定义服务

在这一步中，你将改变我们获取数据的方法。

* 我们定义了一个自定义服务，它代表了一个[RESTful][restful]客户端。利用该客户端，我们可以用更容易的方式制作一个向服务器索取数据的请求，不需要去处理底层?[$http](https://docs.angularjs.org/api/ng/service/$http) API、HTTP方法以及URL。

把工作空间重置到第十一步

```
git checkout -f step-11
```
刷新你的浏览器或在线检查这一步：[Step 8 Live Demo](http://angular.github.io/angular-phonecat/step-11/app)

下面列出了第十步和第十一步之间最重要的区别。你可以在[GitHub](https://github.com/angular/angular-phonecat/compare/step-10...step-11 "See diff on Github")上看到完整的差异。

## 依赖性

Angular在`ngResource`模块中提供了安静的功能，它是与核心Angular框架分开分布的。

我们正在使用[Bower][bower]以安装客户端依赖性。这一步更新的`bower.json`配置文件，以包含新的依赖性：

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
    "angular-resource": "1.4.x"
  }
}
```

新的依赖性`"angular-resource": "1.4.x"`告诉bower安装一个以angular为源的组件的版本，它与v1.4x版兼容。我们必须要求bower下载并安装这个依赖性。我们可以通过运行下面的指令来做到它：

```
npm install
```

<div class="alert alert-warning">
  **警告：**如果自从你上一次运行`npm install`以后，Angular又发布了一个新版本，则你用`bower install`可能遇到问题，因为你安装的angular.js的版本与它有冲突。如果你想通过它，则需要在运行`npm install`之前先删除你的`app/bower_components`文件夹。
</div>

<div class="alert alert-info">
  **注意：**如果你已经全局安装了bower，则你可以运行`bower install`，但是为了我们已经预配置的项目,`npm install`为我们运行了bower。
</div>

## 模板

我们的自定义源服务将被定义在`app/js/services.js`中，因此我们需要在我们的布局模板中包含这个文件。另外，我们还需要载入`angular-resouces.js`文件，它包含了[ngResource](https://docs.angularjs.org/api/ngResource)模块：

**`app/index.html`.**

```html
...
  <script src="bower_components/angular-resource/angular-resource.js"></script>
  <script src="js/services.js"></script>
...
```

## 服务

我们创建了自己的服务，以提供对服务器上的手机数据的访问：

**`app/js/services.js`.**

```js
var phonecatServices = angular.module('phonecatServices', ['ngResource']);

phonecatServices.factory('Phone', ['$resource',
  function($resource){
    return $resource('phones/:phoneId.json', {}, {
      query: {method:'GET', params:{phoneId:'phones'}, isArray:true}
    });
  }]);
```

我们使用模块API，利用工厂函数注册自定义的服务。我们传入服务的名称“Phone”以及工厂函数。工厂函数的结构近似于控制器，两者都可以声明依赖性，以通过函数参数注入。Phone服务在`$resource`服务上声明了一个依赖性。

[`$resource`](https://docs.angularjs.org/api/ngResource/service/$resource)服务使它更容易只用寥寥几行代码创建一个[RESTful][restful]客户端。这种客户端可以用在我们的应用中，代替底层[$http](https://docs.angularjs.org/api/ng/service/$http)服务。

**`app/js/app.js`.**

```js
...
angular.module('phonecatApp', ['ngRoute', 'phonecatControllers','phonecatFilters', 'phonecatServices']).
...
```

我们需要把`phonecatServices`模块依赖性添加到`phonecatApp`模块的需要数列中。

## 控制器

通过重构掉底层的[$http](https://docs.angularjs.org/api/ng/service/$http)服务，我们简化了我们的子控制器（`PhoneListCtrl`和`PhoneDetailCtrl`），用称为`Phone`的服务替代它。Angular的[`$resource`](https://docs.angularjs.org/api/ngResource/service/$resource)服务比`$http`更容易使用，用来与作为REST的源对外提供的数据源交互。现在我们更容易理解控制器中的这些代码是干什么的了。

**`app/js/controllers.js`.**

```js
var phonecatControllers = angular.module('phonecatControllers', []);

...

phonecatControllers.controller('PhoneListCtrl', ['$scope', 'Phone', function($scope, Phone) {
  $scope.phones = Phone.query();
  $scope.orderProp = 'age';
}]);

phonecatControllers.controller('PhoneDetailCtrl', ['$scope', '$routeParams', 'Phone', function($scope, $routeParams, Phone) {
  $scope.phone = Phone.get({phoneId: $routeParams.phoneId}, function(phone) {
    $scope.mainImageUrl = phone.images[0];
  });

  $scope.setImage = function(imageUrl) {
    $scope.mainImageUrl = imageUrl;
  }
}]);
```

注意我们把`PhoneList`内部替换成了什么：

```
$http.get('phones/phones.json').success(function(data) {
  $scope.phones = data;
});
```

换成：

```
$scope.phones = Phone.query();
```

我们通过这条简单语句来查询所有手机。

一个需要注意的重要事情是，在上面的代码中，在引用手机服务的方法的时候，我们没有传递任何回调函数。虽然它看起来就像结果是同步返回的，但其实根本不是。同步返回的是一个“future”——一个对象，当XHR响应返回的时候，将填入数据。因为Angular中的数据绑定，我们可以使用这个future并且把它绑定到我们的模板上。然后，当数据到达的时候，视图将自动更新。

有些时候，单凭future对象和数据绑定不足以满足我们所有的需求，在那种情况下，我们可以添加一个回调函数，以处理服务器响应。`PhoneDetailCtrl`控制器通过设置回调函数中的`mainImageUrl`来演示它。

## 测试

因为我们现在使用了[ngResource](https://docs.angularjs.org/api/ngResource)模块，为了用以angular为源更新Karma配置单文件，它是必要的，这样新测试才能通过。

**`test/karma.conf.js`:**

```js
    files : [
      'app/bower_components/angular/angular.js',
      'app/bower_components/angular-route/angular-route.js',
      'app/bower_components/angular-resource/angular-resource.js',
      'app/bower_components/angular-mocks/angular-mocks.js',
      'app/js/**/*.js',
      'test/unit/**/*.js'
    ],
```

我们已经修改了我们的单元测试，以验证我们的新服务会发起HTTP请求，并像预期那样处理它们。测试还检查了我们的控制器正确地与服务交互。

[$resource](https://docs.angularjs.org/api/ngResource/service/$resource)服务参增加了带有用来更新和删除源的方法的响应对象。如果我们打算使用标准的`toEqual`匹配器，我们的测试将失败，因为测试值不能与响应严格匹配。要想解决这个问题，我们使用了一个新定义的`toEqualData`[Jasmine matcher][jasmine匹配器]。当`toEqualData`匹配器对比两个对象的时候，它考虑对象属性属性而忽略对象方法。

**`test/unit/controllersSpec.js`:**

```js
describe('PhoneCat controllers', function() {

  beforeEach(function(){
    this.addMatchers({
      toEqualData: function(expected) {
        return angular.equals(this.actual, expected);
      }
    });
  });

  beforeEach(module('phonecatApp'));
  beforeEach(module('phonecatServices'));


  describe('PhoneListCtrl', function(){
    var scope, ctrl, $httpBackend;

    beforeEach(inject(function(_$httpBackend_, $rootScope, $controller) {
      $httpBackend = _$httpBackend_;
      $httpBackend.expectGET('phones/phones.json').
          respond([{name: 'Nexus S'}, {name: 'Motorola DROID'}]);

      scope = $rootScope.$new();
      ctrl = $controller('PhoneListCtrl', {$scope: scope});
    }));


    it('should create "phones" model with 2 phones fetched from xhr', function() {
      expect(scope.phones).toEqualData([]);
      $httpBackend.flush();

      expect(scope.phones).toEqualData(
          [{name: 'Nexus S'}, {name: 'Motorola DROID'}]);
    });


    it('should set the default value of orderProp model', function() {
      expect(scope.orderProp).toBe('age');
    });
  });


  describe('PhoneDetailCtrl', function(){
    var scope, $httpBackend, ctrl,
        xyzPhoneData = function() {
          return {
            name: 'phone xyz',
            images: ['image/url1.png', 'image/url2.png']
          }
        };


    beforeEach(inject(function(_$httpBackend_, $rootScope, $routeParams, $controller) {
      $httpBackend = _$httpBackend_;
      $httpBackend.expectGET('phones/xyz.json').respond(xyzPhoneData());

      $routeParams.phoneId = 'xyz';
      scope = $rootScope.$new();
      ctrl = $controller('PhoneDetailCtrl', {$scope: scope});
    }));


    it('should fetch phone detail', function() {
      expect(scope.phone).toEqualData({});
      $httpBackend.flush();

      expect(scope.phone).toEqualData(xyzPhoneData());
    });
  });
});
```

你现在可以在Karma选项卡中看到如下的输出：

<pre>Chrome 22.0: Executed 5 of 5 SUCCESS (0.038 secs / 0.01 secs)</pre>

# 总结

现在我们已经看到了如何建立一个自定义的服务，作为REST的客户端，我们已经准备好前往[第十二步 应用动画](step12.html)（最后一步）以学会如何用动画提高应用程序。

[restful]: http://en.wikipedia.org/wiki/Representational_State_Transfer
[jasmine-matchers]: http://jasmine.github.io/1.3/introduction.html#section-Matchers
[bower]: http://bower.io/
