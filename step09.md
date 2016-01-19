#筛选器

在这一步中，你将学会如何创建你自己的自定义显示筛选器。

* 在上一步中，详情页要么显示“true”，要么显示“false”以指示某个手机功能是否存在。我们已经使用了一个自定义筛选器以将那些文本字符串转换成另一种字型：“true”变成?，“false”变成?。让我们看看筛选器代码看起来如何。

把工作空间重置到第九步

```
git checkout -f step-9
```
刷新你的浏览器或在线检查这一步：[Step 9 Live Demo](http://angular.github.io/angular-phonecat/step-9/app)


下面列出了第八步和第九步之间最重要的区别。你可以在[GitHub](https://github.com/angular/angular-phonecat/compare/step-8...step-9 "See diff on Github")上看到完整的差异。


## 自定义筛选器

为了创建一个新筛选器，你即将创建一个`phonecatFilters`模块，并用这个模块注册你的自定义滤镜：

**`app/js/filters.js`:**

```js
angular.module('phonecatFilters', []).filter('checkmark', function() {
  return function(input) {
    return input ? '\u2713' : '\u2718';
  };
});
```

我们的筛选器的名字是“checkmark”。`input`要么估值为`true`，要么估值为`false`，而且会返回我们选中用来代表true和false的两个unicode字符之一（`\u2713`->?代表true，`\u2718` -> ?代表false）。

现在我们的筛选器已经准备好了，我们需要注册`phonecatFilters`模块作为我们的主`phonecatApp`模块的依赖性。

**`app/js/app.js`:**

```js
...
angular.module('phonecatApp', ['ngRoute','phonecatControllers','phonecatFilters']);
...
```

## 模板

因为筛选器生存在`app/js/filters.js`文件夹中，我们需要在我们的布局模板中包含这个文件。

**`app/index.html`:**

```html
...
 <script src="js/controllers.js"></script>
 <script src="js/filters.js"></script>
...
```

在Angular模板中使用筛选器的句法如下所示：

    {{ expression | filter }}

让我们在手机详情模板中采用这个筛选器：

**`app/partials/phone-detail.html`:**

```html
...
    <dl>
      <dt>Infrared</dt>
      <dd>{{phone.connectivity.infrared | checkmark}}</dd>
      <dt>GPS</dt>
      <dd>{{phone.connectivity.gps | checkmark}}</dd>
    </dl>
...
```


## 测试

筛选器，就像任何别的组件，必须被测试，而且写这些测试很容易。

**`test/unit/filtersSpec.js`:**

```js
describe('filter', function() {

  beforeEach(module('phonecatFilters'));

  describe('checkmark', function() {

    it('should convert boolean values to unicode checkmark or cross',
        inject(function(checkmarkFilter) {
      expect(checkmarkFilter(true)).toBe('\u2713');
      expect(checkmarkFilter(false)).toBe('\u2718');
    }));
  });
});
```

我们必须在执行任何筛选器测试之前调用`beforeEach(module('phonecatFilters'))`。这种调用把我们的`phonecatFilter`模块载入到注入器，以测试运行。

注意我们将调用助手函数`inject(function(checkmarkFilter) { ... })`，从而获得访问我们想要测试的文件。参见[angular.mock.inject()](https://docs.angularjs.org/api/ngMock/function/angular.mock.inject)。

注意在注入的时候，后缀`Filter`会追加到你的筛选器名称中。参见[筛选器指南](https://docs.angularjs.org/guide/filter#using-filters-in-controllers-services-and-directives)?部分，在那里是概述。

你现在必须在Karma选项卡中看到以下的输出：

<pre>Chrome 22.0: Executed 4 of 4 SUCCESS (0.034 secs / 0.012 secs)</pre>

# 实验

* 让我们用一些[内建的Angular筛选器](https://docs.angularjs.org/api/ng/filter)来做实验，并把以下绑定添加到`index.html`：
  * `{{ "lower cap string" | uppercase }}`
  * `{{ {foo: "bar", baz: 23} | json }}`
  * `{{ 1304375948024 | date }}`
  * `{{ 1304375948024 | date:"MM/dd/yyyy @ h:mma" }}`

*  我们可以创建一个模块，带有一个输入元素，并把它与一个筛选绑定结合起来。向index.html添加以下代码：

  ```html
  <input ng-model="userInput"> Uppercased: {{ userInput | uppercase }}
  ```

# 总结

现在你已经学会了如何编写并测试一个自定义筛选器，前往[第十步 事件处理函数](step10.html)以学习我们可以如何用Angular继续丰富手机详情页面。
