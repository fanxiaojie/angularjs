#更多模板

在这一步中，你将实现手机详情视图，当用户在手机列表中点击了一款手机，就会显示这个视图。

* 当你在列表中点击了一款手机，将显示带手机专有信息的手机详情视图。

要想实现手机详情视力，我们将使用[$http](https://docs.angularjs.org/api/ng/service/$http)以取回我们的数据，然后具体化`phone-detail.html`视图模板。

把工作空间重置到第八步

```
git checkout -f step-8
```
刷新你的浏览器或在线检查这一步：[Step 8 Live Demo](http://angular.github.io/angular-phonecat/step-8/app)

下面列出了第七步和第八步之间最重要的区别。你可以在[GitHub](https://github.com/angular/angular-phonecat/compare/step-7...step-8 "See diff on Github")上看到完整的差异。

## 数据

除了`phones.json`，`app/phones/`目录还包括一个针对每款手机的JSON文件：

**`app/phones/nexus-s.json`:** (sample snippet)

```js
{
  "additionalFeatures": "Contour Display, Near Field Communications (NFC),...",
  "android": {
      "os": "Android 2.3",
      "ui": "Android"
  },
  ...
  "images": [
      "img/phones/nexus-s.0.jpg",
      "img/phones/nexus-s.1.jpg",
      "img/phones/nexus-s.2.jpg",
      "img/phones/nexus-s.3.jpg"
  ],
  "storage": {
      "flash": "16384MB",
      "ram": "512MB"
  }
}
```

每个文件用同样的数据结构描述了手机的多种属性。我们将在手机详情视图中展示这些数据。

## 控制器

我们将使用`$http`服务以扩展`PhoneDetailCtrl`，从而取回JSON文件。这以手机列表控制器中同样的方式起作用。

**`app/js/controllers.js`:**

```js
var phonecatControllers = angular.module('phonecatControllers',[]);

phonecatControllers.controller('PhoneDetailCtrl', ['$scope', '$routeParams', '$http',
  function($scope, $routeParams, $http) {
    $http.get('phones/' + $routeParams.phoneId + '.json').success(function(data) {
      $scope.phone = data;
    });
  }]);
```

要想为HTTP请求构造URL，我们使用从`$route`服务生成的当前路由中提取到的`$routeParames.phoneId`。

## 模板

该TBD占位符行已经被替换成列表，绑定包含了手机详情。注意我们使用Angular的`{{expression}}`标签以及`ngRepeat`的地方，用来从我们的模块把投射手机数据到视图中。

**`app/partials/phone-detail.html`:**

```html
<img ng-src="{{phone.images[0]}}" class="phone">
<h1>{{phone.name}}</h1>
<p>{{phone.description}}</p>
<ul class="phone-thumbs">
  <li ng-repeat="img in phone.images">
    <img ng-src="{{img}}">
  </li>
</ul>
<ul class="specs">
  <li>
    <span>Availability and Networks</span>
    <dl>
      <dt>Availability</dt>
      <dd ng-repeat="availability in phone.availability">{{availability}}</dd>
    </dl>
  </li>
    ...
  <li>
    <span>Additional Features</span>
    <dd>{{phone.additionalFeatures}}</dd>
  </li>
</ul>
```

<div style="display: none">
TODO!
<img  class="diagram" src="https://docs.angularjs.org/img/tutorial/tutorial_08-09_final.png">
</div>

## 测试

我们写了一个新的单元测试，类似于我们之前在第五步中为`PhoneListCtrl`控制器所写的步骤。

**`test/unit/controllersSpec.js`:**

```js

  beforeEach(module('phonecatApp'));

  ...

  describe('PhoneDetailCtrl', function(){
    var scope, $httpBackend, ctrl;

    beforeEach(inject(function(_$httpBackend_, $rootScope, $routeParams, $controller) {
      $httpBackend = _$httpBackend_;
      $httpBackend.expectGET('phones/xyz.json').respond({name:'phone xyz'});

      $routeParams.phoneId = 'xyz';
      scope = $rootScope.$new();
      ctrl = $controller('PhoneDetailCtrl', {$scope: scope});
    }));


    it('should fetch phone detail', function() {
      expect(scope.phone).toBeUndefined();
      $httpBackend.flush();

      expect(scope.phone).toEqual({name:'phone xyz'});
    });
  });
...
```

你现在可以看到后来在Karma标签中的输出：

<pre>Chrome 22.0: Executed 3 of 3 SUCCESS (0.039 secs / 0.012 secs)</pre>

我们还添加了一个新的端到端的测试，导航到Nexus S详情页面，并核查了页面上的标题是“Nexus S”。

**`test/e2e/scenarios.js`:**

```js
...
  describe('Phone detail view', function() {

    beforeEach(function() {
      browser.get('app/index.html#/phones/nexus-s');
    });


    it('should display nexus-s page', function() {
      expect(element(by.binding('phone.name')).getText()).toBe('Nexus S');
    });
  });
...
```

你可以再次运行`npm run protractor`以看到测试运行。

# 实验

* 使用[Protractor API](http://angular.github.io/protractor/#/api)，写一个测试核查到我们在Nexus S详情页面中显示了四个缩略图。

# 总结

现在手机详情视力已经到位了，继续前往[第九步 筛选器](step09.html)以学习如何编写你自己的自定义显示过滤器。
