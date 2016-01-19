#静态模板

为了演示Angular如何增强静态HTML，你可以创建一个纯**静态**HTML网页，然后仔细观察我们可以如何把这些HTML代码变成一个模板，从而Angular可以用来动态显示同样的结果、以任何数据集显示结果。

在这一步中，你将在一个HTML页面中添加关于两款手机的基本的信息。

- 网页现在包含了一个列表，带有两款手机的信息。

把工作空间重置到第一步

```
git checkout -f step-1
```

刷新你的浏览器或在线检查这一步：[Step 1 Live Demo](http://angular.github.io/angular-phonecat/step-1/app)


下面列出了第零步和第一步之间的最重要的区别。你可以在[GitHub](https://github.com/angular/angular-phonecat/compare/step-0...step-1)里看到完整的差异。

**app/index.html:**

```html
  <ul>
    <li>
      <span>Nexus S</span>
      <p>
        Fast just got faster with Nexus S.
      </p>
    </li>
    <li>
      <span>Motorola XOOM? with Wi-Fi</span>
      <p>
        The Next, Next Generation tablet.
      </p>
    </li>
  </ul>
```

# 实验

* 尝试向`index.html`添加更多的静态HTML。比如：

```
<p>Total number of phones: 2</p>
```

# 总结

额外对你的应用使用静态HTML以显示这个列表。现在，让我们前往[第二步 Angular模板](step02.html)以学习如何使用AngularJS以动态生成同一个列表。
