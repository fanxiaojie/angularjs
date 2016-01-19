#模板连接和图像

在这一步中，你将为手机列表中的手机添加一个缩略图，并链接到想去的地方。在后续的步骤中，你将使用这个链接显示关于目录中的这款手机的额外的信息。

* 现在列表中的手机已经有链接和图像了。

把工作空间重置到第六步

```
git checkout -f step-6
```
刷新你的浏览器或在线检查这一步：[Step 6 Live Demo](http://angular.github.io/angular-phonecat/step-6/app)

下面列出了第五步和第六步之间的最重要的区别。你可以在[GitHub](https://github.com/angular/angular-phonecat/compare/step-5...step-6)里看到完整的差异。

## 数据

注意，`phones.json`文件包含了针对每款手机的独一无二的ID和图像URL。URL指向`app/img/phones`目录。

**`app/phones/phones.json`**（示例片段）：

```js
[
  {
    ...
    "id": "motorola-defy-with-motoblur",
    "imageUrl": "img/phones/motorola-defy-with-motoblur.0.jpg",
    "name": "Motorola DEFY\u2122 with MOTOBLUR\u2122",
    ...
  },
  ...
]
```


## 模板

**`app/index.html`:**

```html
...
        <ul class="phones">
          <li ng-repeat="phone in phones | filter:query | orderBy:orderProp" class="thumbnail">
            <a href="#/phones/{{phone.id}}" class="thumb"><img ng-src="{{phone.imageUrl}}"></a>
            <a href="#/phones/{{phone.id}}">{{phone.name}}</a>
            <p>{{phone.snippet}}</p>
          </li>
        </ul>
...
```

要想未来动态生成指向手机详情页的链接，我们在元素属性`href`的值上使用了广为人知的双花括号绑定。在第二步中，我们添加了`{{phone.name}}`绑定，作为元素内容。在这一步中，`{{phone.id}}`绑定用在元素属性上。

我们还在每条记录后面添加了手机图像，用一个带[ngSrc](https://docs.angularjs.org/api/ng/directive/ngSrc)指令的图像标签。这个指令防止浏览器字面理解Anglar标签`{{ expression }}`、向无效的URL`http://localhost:8000/app/{{phone.imageUrl}}`初始化一个请求，如果我们只在一个常规的元素属性`src`上绑定这个双花括号值（`<img src="{{phone.imageUrl}}">`），这样的事情真的会发生。使用`ngSrc`指令以防止浏览器对一个无效的位置发起http请求。

## 测试

**`test/e2e/scenarios.js`**:

```js
...
    it('should render phone specific links', function() {
      var query = element(by.model('query'));
      query.sendKeys('nexus');
      element.all(by.css('.phones li a')).first().click();
      browser.getLocationAbsUrl().then(function(url) {
        expect(url).toBe('/phones/nexus-s');
      });
    });
...
```

我们添加了一个新的端到端测试，以核查应用是否生成了正确的链接，正确的链接要链到手机视图中，我们将在下一步是实现这个视图。

现在你可以重新运行`npm run protractor`以查看测试运行。


# 实验

* 用扁平的旧元素属性`src`代替`ng-src`指令。使用一些工具，比如说Firebug，或者Chrome的Web Inspector，或者检查web服务器的访问日志，确保应用确实发起过了一个外部请求，指向`/app/%7B%7Bphone.imageUrl%7D%7D`（或者`/app/{{phone.imageUrl}}`）。

  有个话题是，在Angular已经有机会评估表达式、并注入有效地址之前，当浏览器读取`img`标签时，浏览器将向非法的图像地址发起一个请求。


# 总结

现在你已经添加了一个手机图像和链接，前往[第七步 路由与多视图](step07.html)以学习关于Angular布局模板、以及Angular如何使创建具有多个视图的应用变容易。
