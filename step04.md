#双路数据绑定

在这一步中，你将添加一个功能，让你的用户控制手机列表中的项目的排序。这个动态排序由创建一个新模块属性来实现，用迭代器接通它们，并且让数据绑定来完成剩余工作。

* 除了搜索框，应用显示了一个下拉菜单，允许用户 控制列出的手机的排序。

<div class="alert alert-info">

把工作空间重置到第四步

```
git checkout -f step-4
```
刷新你的浏览器或在线检查这一步：[Step 4 Live Demo](http://angular.github.io/angular-phonecat/step-4/app)

</div>

下面列出了第四步和第五步之间的最重要的区别。你可以在[GitHub](https://github.com/angular/angular-phonecat/compare/step-3...step-4)里看到完整的差异。

## 模板

**`app/index.html`:**

```html
  Search: <input ng-model="query">
  Sort by:
  <select ng-model="orderProp">
    <option value="name">Alphabetical</option>
    <option value="age">Newest</option>
  </select>


  <ul class="phones">
    <li ng-repeat="phone in phones | filter:query | orderBy:orderProp">
      <span>{{phone.name}}</span>
      <p>{{phone.snippet}}</p>
    </li>
  </ul>
```

我们制作以下对`index.html`模板的改变：

* 首先，我们添加了一个`<select>` html元素，命名为`orderProp`，因此我们的用户可以从两个提供的排序选择中选一个。

<img class="diagram" src="https://docs.angularjs.org/img/tutorial/tutorial_04.png">

* 然后，我们把`filter`筛选器连与[`orderBy`](https://docs.angularjs.org/api/ng/filter/orderBy)?筛选器连缀，以更进一步处理输入到迭代器的处理。`orderBy`是一个筛选器，取用一个输入数组，复制它，之后返回一个副本，重排序这个副本。

Angular在`select`元素以及`orderProp`模块之间创建了双路数据绑定。然后`orderProp`被用作针对`orderBy`筛选器的输入。

正如我们在这一节第三步中讨论的，关于数据绑定和迭代器，每当模块变化时（比如说因为用户通过选择下拉菜单改变了顺序），Angular的数据绑定将导致该视图自动更新。不臃肿的DOM操作代码是必要的！

## 控制器

**`app/js/controllers.js`:**

```js
var phonecatApp = angular.module('phonecatApp', []);

phonecatApp.controller('PhoneListCtrl', function ($scope) {
  $scope.phones = [
    {'name': 'Nexus S',
     'snippet': 'Fast just got faster with Nexus S.',
     'age': 1},
    {'name': 'Motorola XOOM? with Wi-Fi',
     'snippet': 'The Next, Next Generation tablet.',
     'age': 2},
    {'name': 'MOTOROLA XOOM?',
     'snippet': 'The Next, Next Generation tablet.',
     'age': 3}
  ];

  $scope.orderProp = 'age';
});
```

* 我们修改了`phones`模块——手机的数组——并把一个`age`属性添加到每个手机记录中。属性被用于根据年代排序手机。

* 我们给控制器添加了一行，把`orderProp`的默认值设置为`age`。如果我们还没有在这里设置一个默认值，`orderBy`筛选器会保持未初始化，直到我们的用户从下拉菜单中挑了一个选项。

  这是讲解双路数据绑定的好时候。注意，当应用在浏览器中载入的时候，下拉菜单中的“最新”被选中。这是因为我们在控制器中把`orderProp`设置为`'age'`。从我们的模块到UI的方向中的绑定工作也同样。现在，如果你选择了下拉菜单中的“Alphabetically（字母表排序）”，模块也将被更新，而且重排序了手机。这是数据绑定在反方向中所做的工作——从UI到模块。

## 测试

我们所做的变化将在单元测试和端到端测试中被验证。让我们先看一看单元测试。

**`test/unit/controllersSpec.js`:**

```js
describe('PhoneCat controllers', function() {

  describe('PhoneListCtrl', function(){
    var scope, ctrl;

    beforeEach(module('phonecatApp'));

    beforeEach(inject(function($controller) {
      scope = {};
      ctrl = $controller('PhoneListCtrl', {$scope:scope});
    }));

    it('should create "phones" model with 3 phones', function() {
      expect(scope.phones.length).toBe(3);
    });


    it('should set the default value of orderProp model', function() {
      expect(scope.orderProp).toBe('age');
    });
  });
});
```

单元测试现在核实了默认的排序属性设置。

我们使用Jasmins的API，把控制器架构抽出到`beforeEach`块，它由所有的父`describe`块中的测试共享。

现在你应该在Karma选项卡中看到了以下输出：

<pre>Chrome 22.0: Executed 2 of 2 SUCCESS (0.021 secs / 0.001 secs)</pre>

让我们把注意力带回到端到端测试。

**`test/e2e/scenarios.js`:**

```js
...
    it('should be possible to control phone order via the drop down select box', function() {

      var phoneNameColumn = element.all(by.repeater('phone in phones').column('phone.name'));
      var query = element(by.model('query'));

      function getNames() {
        return phoneNameColumn.map(function(elm) {
          return elm.getText();
        });
      }

      query.sendKeys('tablet'); //let's narrow the dataset to make the test assertions shorter

      expect(getNames()).toEqual([
        "Motorola XOOM\u2122 with Wi-Fi",
        "MOTOROLA XOOM\u2122"
      ]);

      element(by.model('orderProp')).element(by.css('option[value="name"]')).click();

      expect(getNames()).toEqual([
        "MOTOROLA XOOM\u2122",
        "Motorola XOOM\u2122 with Wi-Fi"
      ]);
    });...
```

端到端测试核实了选择框的排序机制正在正常工作。

现在你可以重新运行`npm run protractor`以查看测试运行。

# 实验

* 在`PhoneListCtrl`控制器中，移除设置`orderProp`值的状态，你将看到Angular给下拉列表临时地添加了一个新的空白（"unknown"）选项，而且排序将默认为无序/自然排序。

* 把一个`{{orderProp}}`绑定到`index.html`模板上，从而把它的当前值显示为文本。

* 在排序值前面添加一个`-`符号来逆转排序顺序：`<option value="-age">Oldest</option>`

# 总结

现在我们已经添加了列表排序，并测试了应用，前往[第五步 XHR和依赖注入](step05.html)以学习关于Angular服务，以及Angular如何使用依赖性注入。
