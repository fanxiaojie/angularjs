 #事件处理函数
 
 在这一步中，你将添加一个可点击的手机图像交换器，指向手机详情页面。

* 手机详情视图显示了当前手机的一张大图像以及若干张小的缩略图。如果我们可以通过在想要的缩略图中点吉，从而把大图像与任何小缩略图作替换，这会很棒。让我们看一看我们可以如何用Angular做到这。

把工作空间重置到第十步

```
git checkout -f step-10
```
刷新你的浏览器或在线检查这一步：[Step 10 Live Demo](http://angular.github.io/angular-phonecat/step-10/app)

下面列出了第九步和第十步之间最重要的区别。你可以在[GitHub](https://github.com/angular/angular-phonecat/compare/step-9...step-10 "See diff on Github")上看到完整的差异。


## 控制器

**`app/js/controllers.js`:**

```js
...
var phonecatControllers = angular.module('phonecatControllers',[]);

phonecatControllers.controller('PhoneDetailCtrl', ['$scope', '$routeParams', '$http',
  function($scope, $routeParams, $http) {
    $http.get('phones/' + $routeParams.phoneId + '.json').success(function(data) {
      $scope.phone = data;
      $scope.mainImageUrl = data.images[0];
    });

    $scope.setImage = function(imageUrl) {
      $scope.mainImageUrl = imageUrl;
    };
  }]);
```

在`PhoneDetailCtrl`控制器中，我们创建了`mainImageUrl`模块属性，并把它的默认值设置为第一个手机图像URL。

我们还创建了一个`setImage`事件处理函数，它将改变`mainImageUrl`的值。

## 模板

**`app/partials/phone-detail.html`:**

```html
<img ng-src="{{mainImageUrl}}" class="phone">

...

<ul class="phone-thumbs">
  <li ng-repeat="img in phone.images">
    <img ng-src="{{img}}" ng-click="setImage(img)">
  </li>
</ul>
...
```

我们把大图像的`ngSrc`指令绑定到`mainImageUrl`属性上。

我们还将利用缩略图注册一个[`ngClick`](https://docs.angularjs.org/api/ng/directive/ngClick)处理函数。当用户在缩图略之一上点击时，处理函数将使用`setImage`事件处理函数以改变`mainImageUrl`属性的值，把它变成缩略图的URL。

<div style="display: none">
TODO!
<img  class="diagram" src="https://docs.angularjs.org/img/tutorial/tutorial_10-11_final.png">
</div>

## 测试

要想验证这个功能，我们添加了两个端到端测试。一个验证了主图像被默认设置为每一个手机图像。另一个测试了在一些缩略图上的点击，并验证了相应的主图像改变。

**`test/e2e/scenarios.js`:**

```js
...
  describe('Phone detail view', function() {

...

    it('should display the first phone image as the main phone image', function() {
      expect(element(by.css('img.phone')).getAttribute('src')).toMatch(/img\/phones\/nexus-s.0.jpg/);
    });


    it('should swap main image if a thumbnail image is clicked on', function() {
      element(by.css('.phone-thumbs li:nth-child(3) img')).click();
      expect(element(by.css('img.phone')).getAttribute('src')).toMatch(/img\/phones\/nexus-s.2.jpg/);

      element(by.css('.phone-thumbs li:nth-child(1) img')).click();
      expect(element(by.css('img.phone')).getAttribute('src')).toMatch(/img\/phones\/nexus-s.0.jpg/);
    });
  });
```

现在你可以再次运行`rpn run protractor`以看到测试运行。

你还必须重构你的单元测试之一，因为又有一个`mainImageUrl`模块属性添加到了`PhoneDetailCtrl`控制器上了。下面，我们创建了函数`xyzPhoneData`，该函数会返回相应的带有`image`元素属性的json，从而使测试通过。

**`test/unit/controllersSpec.js`:**

```js
...
  beforeEach(module('phonecatApp'));

...

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
      expect(scope.phone).toBeUndefined();
      $httpBackend.flush();

      expect(scope.phone).toEqual(xyzPhoneData());
    });
  });
```

你的单元测试现在应该通过了。

# 实验

* 让我们给`PhoneDetailCtrl`添加一个新的控制器方法：

  ```
  $scope.hello = function(name) {
      alert('Hello ' + (name || 'world') + '!');
  }
  ```
  再添加

  ```
  <button ng-click="hello('Elmo')">Hello</button>
  ```

  to the `phone-detail.html` template.

<div style="display: none">
TODO!
  控制器方法在控制器/作用域之间继承，因此你可以在`phone-list.html`模板内使用同样的片段。

* 把`hello`方法从`PhoneCatCtrl`移到`PhoneListCtrl`，而且你将看到在`index.html`中声明的按钮将停止工作，与此同时在`phone-list.html`模板中声明的那个按钮依然在运作。
</div>


# 总结

随着手机图像交换器到位，我们准备前往[第十一步 REST和自定义服务](step11.html)以学习取得数据的一个更好方法。
