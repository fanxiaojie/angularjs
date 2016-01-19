#筛选迭代器

我们在上一步中为开发应用打基础做了很多工作，现在我们将做一些简单的事情；我们将添加全文搜索（是的，它很简单！）。我们还将编写一个端到端测试，因为一个好的端到端测试可以帮上大忙。它监视着你的应用，并在发生回归时迅速报告。

* 现在应用有了一个搜索框。注意页面上的手机列表的变化取决于用户在搜索框中打了什么字。

<div class="alert alert-info">

把工作空间重置到第三步

```
git checkout -f step-3
```
刷新你的浏览器或在线检查这一步：[Step 3 Live Demo](http://angular.github.io/angular-phonecat/step-3/app)

</div>

下面列出了第二步和第三步之间最重要的区别。你可以在[GitHub](https://github.com/angular/angular-phonecat/compare/step-2...step-3)里看到完整的差异。

## 控制器

我们对控制器不作修改。

## 模板

**app/index.html:**

```html
  <div class="container-fluid">
    <div class="row">
      <div class="col-md-2">
        <!--Sidebar content-->

        Search: <input ng-model="query">

      </div>
      <div class="col-md-10">
        <!--Body content-->

        <ul class="phones">
          <li ng-repeat="phone in phones | filter:query">
            {{phone.name}}
            <p>{{phone.snippet}}</p>
          </li>
        </ul>

      </div>
    </div>
  </div>
```

我们添加了一个标准HTML`<input>`元素标记，并使用Angular的[filter](https://docs.angularjs.org/api/ng/filter/filter)函数来处理[repeat指令](https://docs.angularjs.org/api/ng/directive/ngRepeat)的输入。

这使用户输入搜索条件，并在手机列表中快速看到搜索结果。新的代码演示如下：

* 数据绑定：这是Angular的一个核心功能。当网页载入时，Angular把输入框的名称绑定到数据模块的同名的变量上，并保持两者同步。

 在代码中，用户打字到输入框的数据（命名为**`query`**）很快可以作为一个筛选器输入到列表迭代器（`phone in phones | filter:`**`query`**）中。在改变数据模块的时候，导致迭代器的输入发生变化，迭代器有效地更新了DOM，以反映模块的当前状态。

<img  class="diagram" src="https://docs.angularjs.org/img/tutorial/tutorial_03.png">

* 使用`filter`筛选器：[filter](https://docs.angularjs.org/api/ng/filter/filter)函数使用了`query`值发创建一个新的数列，只包含匹配`query`的记录。

  `ngRepeat`自动更新了视力，以响应`filter`筛选器返回的手机数字的变化。该处理对开发者来说是完全透明的。

## 测试

在第二步中，我们学会了如何编写并运行单元测试。对于测试我们的用JavaScript编写的应用程序的控制器和其它组件，单元测试是完美的，但是测试DOM操作或测试我们的应用程序的接通不太方便。针对这些，一个端到端的测试是一个更好的选择。

该搜索功能完全是通过模板和数据绑定来实现的，我们将编写我们第一个端到端的测试，以验证该功能起了什么作用。

**`test/e2e/scenarios.js`:**

```js
describe('PhoneCat App', function() {

  describe('Phone list view', function() {

    beforeEach(function() {
      browser.get('app/index.html');
    });


    it('should filter the phone list as a user types into the search box', function() {

      var phoneList = element.all(by.repeater('phone in phones'));
      var query = element(by.model('query'));

      expect(phoneList.count()).toBe(3);

      query.sendKeys('nexus');
      expect(phoneList.count()).toBe(1);

      query.clear();
      query.sendKeys('motorola');
      expect(phoneList.count()).toBe(2);
    });
  });
});
```

这个测试验证了搜索框以及迭代器是否正确地接通了。注意，在Angular中，编写端到端测试是如此地容易。虽然这个示例只针对一个简单的测试，但是它确实很容易测试任何功能化的、可读的、端到端的测试。

### 利用Protractor运行端到端的测试

甚至虽然测试的句法看起来很像我们的用Jasmine编写的控制器单元测试，但是端到端测试使用Protractor的API。在<http://angular.github.io/protractor/#/api>可以读到Protractor的API。

与Karma很像的是针对单元测试的测试运行者，我们使用Protractor以运行端到端测试。用`npm run protractor`来尝试它。端到端测试很慢，所以与单元测试不同，在运行测试之后Protractor将退出，不会自动在每次文件更改时重新运行测试套装。要想重新运行测试套装，需要再次执行`npm run protractor`。

<div class="alert alert-info">
  注意，你必须确保你的应用通过一个web服务器提供服务，从而用Protractor测试。你可以使用`npm start`来做到这。你还需要确保你在运行`npm run protractor`之前已经安装了Protractor，并更新了web驱动器。You can do this by issuing `npm install` and `npm run update-webdriver` into your terminal.
</div>

# 实验

### 显示当前查询

通过添加一个绑定到`index.html`模板的`{{query}}`来显示`query`模块当前的值，并看到当你在输入框中打字时，它如何变化。

### 在标题中显示查询

让我们看到我们可以取得`query`模板的当前值，模块出现在HTML网页的标题上。

* 把一个端到端测试添加到`describe`块中，`test/e2e/scenarios.js`看起来将如这：

  ```js
    describe('PhoneCat App', function() {

      describe('Phone list view', function() {
    
        beforeEach(function() {
          browser.get('app/index.html');
        });
    
        var phoneList = element.all(by.repeater('phone in phones'));
        var query = element(by.model('query'));
    
        it('should filter the phone list as a user types into the search box', function() {
          expect(phoneList.count()).toBe(3);
    
          query.sendKeys('nexus');
          expect(phoneList.count()).toBe(1);
    
          query.clear();
          query.sendKeys('motorola');
          expect(phoneList.count()).toBe(2);
        });
    
        it('should display the current filter value in the title bar', function() {
          query.clear();
          expect(browser.getTitle()).toMatch(/Google Phone Gallery:\s*$/);
    
          query.sendKeys('nexus');
          expect(browser.getTitle()).toMatch(/Google Phone Gallery: nexus$/);
        });
      });
    });
  ```

  运行protractor（`npm run protractor`），看到测试失败了。


* 你可能认为你只需要用以下方式向标题标签添加`{{query}}`：
  
  ```
  <title>Google Phone Gallery: {{query}}</title>
  ```

  然而，当你重载入这个网页的时候，你不会看到想要的结果。这是因为“查询”模块驻留在作用域内，由`ng-controller="PhoneListCtrl"`指令在body元素上定义。

  ```
  <body ng-controller="PhoneListCtrl">
  ```

  如果你想要从`<title>`元素上绑定查询模块，你必须把`ngController`声明移动到HTML元素上，因为它是body元素和title元素常用的父元素。

  ```
  <html ng-app="phonecatApp" ng-controller="PhoneListCtrl">
  ```

  确保**从body元素中移除**`ng-controller`声明。

* 重新运行`rpm run protractor`，看到现在测试已经看通过了。

* 在title元素内部使用双花工作得很好，与此同时，你可能会注意到页面加载的一瞬间它们确实显示给用户了。一个更好的解决方案是使用[ngBind指令](https://docs.angularjs.org/api/ng/directive/ngBind)或[ngBindTemplate指令](https://docs.angularjs.org/api/ng/directive/ngBindTemplate)，当页面加载时用户能看到它们。

  ```
  <title ng-bind-template="Google Phone Gallery: {{query}}">Google Phone Gallery</title>
  ```

# 总结

我们现在已经把全文搜索添加上去了，还包含了一个用来验证搜索是否起作用的测试！现在让我们前往[第四步 双路数据绑定](step04.html)以学会如何向手机应用添加排序功能。
