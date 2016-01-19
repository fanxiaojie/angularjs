#快速入门

###为什么要用AngularJS？

HTML非常适合于声明静态的文档，但是当我们试图使用它在web应用程序中声明动态视图时，它显得力不从心。AngularJS能为您的应用程序扩展HTML的词汇。由此产生的环境非常具有有表现力、可读性强、快速发展。

###替代选择

其他处理HTML的缺点的框架要么是抽象出HTML、CSS、和/或JavaScript，要么为操纵DOM提供一个必要的方式。它们都不能解决一个根本问题，即HTML不是为动态视图设计的。

###可扩展性

AngularJS是用来构建框架的工具集，很适全于你的应用程序开发。它完全可扩展，而且与别的库协作得很好。每个功能可以被修改或替代，以适合你的独一无二的开发工作流以及功能需要。继续阅读以弄懂为何。

###The Basics

index.html
```htm
<!doctype html>
<html ng-app>
  <head>
    <script src="https://ajax.googleapis.com/ajax/libs/angularjs/1.4.8/angular.min.js"></script>
  </head>
  <body>
    <div>
      <label>Name:</label>
      <input type="text" ng-model="yourName" placeholder="Enter a name here">
      <hr>
      <h1>Hello {{yourName}}!</h1>
    </div>
  </body>
</html>
```

###添加一些控件

####数据绑定

数据绑定是每当模型改变时更新视图的自动方法，当视图改变时，同样也会更新模型。这非常棒，因为从你需要担心的列表中它减去了DOM操纵。

####控件

控件是DOM元素后面的行为。AngularJS让你能够用一个干净可读的形式表达行为，不需要更新DOM的通常样板、注册回调或者观察模型变化。

####扁平的JavaScript

与别的框架不同，不需要为包装访问器方法中的模型，而继承私有类型。Angular模型是扁平的旧式JavaScript对象。这使你的代码容易读取、容易维护、可重用，还不需要样板。

index.html
```htm
<!doctype html>
<html ng-app="todoApp">
  <head>
    <script src="https://ajax.googleapis.com/ajax/libs/angularjs/1.4.8/angular.min.js"></script>
    <script src="todo.js"></script>
    <link rel="stylesheet" href="todo.css">
  </head>
  <body>
    <h2>Todo</h2>
    <div ng-controller="TodoListController as todoList">
      <span>{{todoList.remaining()}} of {{todoList.todos.length}} remaining</span>
      [ <a href="" ng-click="todoList.archive()">archive</a> ]
      <ul class="unstyled">
        <li ng-repeat="todo in todoList.todos">
          <input type="checkbox" ng-model="todo.done">
          <span class="done-{{todo.done}}">{{todo.text}}</span>
        </li>
      </ul>
      <form ng-submit="todoList.addTodo()">
        <input type="text" ng-model="todoList.todoText"  size="30"
               placeholder="add new todo here">
        <input class="btn-primary" type="submit" value="add">
      </form>
    </div>
  </body>
</html>
```

todo.js
```javascript
angular.module('todoApp', [])
  .controller('TodoListController', function() {
    var todoList = this;
    todoList.todos = [
      {text:'learn angular', done:true},
      {text:'build an angular app', done:false}];
 
    todoList.addTodo = function() {
      todoList.todos.push({text:todoList.todoText, done:false});
      todoList.todoText = '';
    };
 
    todoList.remaining = function() {
      var count = 0;
      angular.forEach(todoList.todos, function(todo) {
        count += todo.done ? 0 : 1;
      });
      return count;
    };
 
    todoList.archive = function() {
      var oldTodos = todoList.todos;
      todoList.todos = [];
      angular.forEach(oldTodos, function(todo) {
        if (!todo.done) todoList.todos.push(todo);
      });
    };
  });
```

todo.css

```css
.done-true {
  text-decoration: line-through;
  color: grey;
}
```

###后端连接

####深链接

一个深链接反应了用户在应用中的哪个位置，这很有用，所以用户可以把它存为书签以及电子邮件链接，以在应用内部定位它。往返旅行的应用程序会自动获得这个功能，但Ajax应用程序按其性质不会。AngularJS结合了深链接以及类似桌面应用程序的行为的优点。

####表单验证

客户端表单验证是完美的用户体验的一个重要的部分。AngularJS使你能够声明表单的有效性规则，而不需要书写JavaScript代码。从而事半功倍。

####服务器通信

AngularJS提供了内建的建在在XHR的顶层的服务，以及多种多样的使用第三方库的其它后端。通过处理异步返回的数据，Promise进一步简化了您的代码。在这个示例中，我们使用AngularFire库以把一个[Firebase](https://www.firebase.com/)后端接通到一个简单的Angular应用上。

index.html

```html
<!doctype html>
<html ng-app="project">
  <head>
    <script src="https://ajax.googleapis.com/ajax/libs/angularjs/1.4.8/angular.min.js"></script>
    <script src="https://ajax.googleapis.com/ajax/libs/angularjs/1.4.8/angular-resource.min.js">
    </script>
    <script src="https://ajax.googleapis.com/ajax/libs/angularjs/1.4.8/angular-route.min.js">
   </script>
    <script src="https://cdn.firebase.com/js/client/2.0.4/firebase.js"></script>
    <script src="https://cdn.firebase.com/libs/angularfire/0.9.0/angularfire.min.js"></script>
    <link rel="stylesheet" href="bootstrap.css">
    <script src="project.js"></script>
  </head>
  <body>
    <h2>JavaScript Projects</h2>
    <div ng-view></div>
  </body>
</html>
```

bootstrap.css

```css
// Uncomment this in Plunker or JSFiddle: @import '//netdna.bootstrapcdn.com/twitter-bootstrap/2.0.4/css/bootstrap-combined.min.css';
```

project.js

```javascript
angular.module('project', ['ngRoute', 'firebase'])
 
.value('fbURL', 'https://ng-projects-list.firebaseio.com/')
.service('fbRef', function(fbURL) {
  return new Firebase(fbURL)
})
.service('fbAuth', function($q, $firebase, $firebaseAuth, fbRef) {
  var auth;
  return function () {
      if (auth) return $q.when(auth);
      var authObj = $firebaseAuth(fbRef);
      if (authObj.$getAuth()) {
        return $q.when(auth = authObj.$getAuth());
      }
      var deferred = $q.defer();
      authObj.$authAnonymously().then(function(authData) {
          auth = authData;
          deferred.resolve(authData);
      });
      return deferred.promise;
  }
})
 
.service('Projects', function($q, $firebase, fbRef, fbAuth) {
  var self = this;
  this.fetch = function () {
    if (this.projects) return $q.when(this.projects);
    return fbAuth().then(function(auth) {
      var deferred = $q.defer();
      var ref = fbRef.child('projects-fresh/' + auth.auth.uid);
      var $projects = $firebase(ref);
      ref.on('value', function(snapshot) {
        if (snapshot.val() === null) {
          $projects.$set(window.projectsArray);
        }
        self.projects = $projects.$asArray();
        deferred.resolve(self.projects);
      });
 
      //Remove projects list when no longer needed.
      ref.onDisconnect().remove();
      return deferred.promise;
    });
  };
})
 
.config(function($routeProvider) {
  var resolveProjects = {
    projects: function (Projects) {
      return Projects.fetch();
    }
  };
 
  $routeProvider
    .when('/', {
      controller:'ProjectListController as projectList',
      templateUrl:'list.html',
      resolve: resolveProjects
    })
    .when('/edit/:projectId', {
      controller:'EditProjectController as editProject',
      templateUrl:'detail.html',
      resolve: resolveProjects
    })
    .when('/new', {
      controller:'NewProjectController as editProject',
      templateUrl:'detail.html',
      resolve: resolveProjects
    })
    .otherwise({
      redirectTo:'/'
    });
})
 
.controller('ProjectListController', function(projects) {
  var projectList = this;
  projectList.projects = projects;
})
 
.controller('NewProjectController', function($location, projects) {
  var editProject = this;
  editProject.save = function() {
      projects.$add(editProject.project).then(function(data) {
          $location.path('/');
      });
  };
})
 
.controller('EditProjectController',
  function($location, $routeParams, projects) {
    var editProject = this;
    var projectId = $routeParams.projectId,
        projectIndex;
 
    editProject.projects = projects;
    projectIndex = editProject.projects.$indexFor(projectId);
    editProject.project = editProject.projects[projectIndex];
 
    editProject.destroy = function() {
        editProject.projects.$remove(editProject.project).then(function(data) {
            $location.path('/');
        });
    };
 
    editProject.save = function() {
        editProject.projects.$save(editProject.project).then(function(data) {
           $location.path('/');
        });
    };
});
```

list.html
```htm
<input type="text" ng-model="projectList.search" class="search-query" id="projects_search"
       placeholder="Search">
<table>
  <thead>
  <tr>
    <th>Project</th>
    <th>Description</th>
    <th><a href="#/new"><i class="icon-plus-sign"></i></a></th>
  </tr>
  </thead>
  <tbody>
  <tr ng-repeat="project in projectList.projects | filter:projectList.search | orderBy:'name'">
    <td><a ng-href="{{project.site}}" target="_blank">{{project.name}}</a></td>
    <td>{{project.description}}</td>
    <td>
      <a ng-href="#/edit/{{project.$id}}"><i class="icon-pencil"></i></a>
    </td>
  </tr>
  </tbody>
</table>
```

detail.html
```htm
<form name="myForm">
  <div class="control-group" ng-class="{error: myForm.name.$invalid && !myForm.name.$pristine}">
    <label>Name</label>
    <input type="text" name="name" ng-model="editProject.project.name" required>
    <span ng-show="myForm.name.$error.required && !myForm.name.$pristine" class="help-inline">
        Required {{myForm.name.$pristine}}</span>
  </div>
 
  <div class="control-group" ng-class="{error: myForm.site.$invalid && !myForm.site.$pristine}">
    <label>Website</label>
    <input type="url" name="site" ng-model="editProject.project.site" required>
    <span ng-show="myForm.site.$error.required && !myForm.site.$pristine" class="help-inline">
        Required</span>
    <span ng-show="myForm.site.$error.url" class="help-inline">
        Not a URL</span>
  </div>
 
  <label>Description</label>
  <textarea name="description" ng-model="editProject.project.description"></textarea>
 
  <br>
  <a href="#/" class="btn">Cancel</a>
  <button ng-click="editProject.save()" ng-disabled="myForm.$invalid"
          class="btn btn-primary">Save</button>
  <button ng-click="editProject.destroy()"
          ng-show="editProject.project.$id" class="btn btn-danger">Delete</button>
</form>
```

###创建组件

####指令

指令是一个独有而且强大的功能，只在Angular中可用。指令使你能够发明新的HTML句法、专针对于你的应用程序。

####可重用的组件

我们使用指令以创建可重复使用的组件。组件允许你隐藏复杂的DOM结构、CSS以及行为。这使你能够专注于应用程序要做什么，或者单独的应用程序看起来如何。

####本地化

严肃的应用程序的一个重要组成部分是本地化。AngularJS的本地探知筛选器以及阻塞指令使你能够建立屏蔽，使你的应用程序在所有的地方都可用。

index.html
```htm
<!doctype html>
<html ng-app="app">
  <head>
    <script src="https://ajax.googleapis.com/ajax/libs/angularjs/1.4.8/angular.min.js"></script>
    <script src="components.js"></script>
    <script src="app.js"></script>
    <link rel="stylesheet" href="bootstrap.css">
  </head>
  <body>
    <tabs>
      <pane title="Localization">
        Date: {{ '2012-04-01' | date:'fullDate' }} <br>
        Currency: {{ 123456 | currency }} <br>
        Number: {{ 98765.4321 | number }} <br>
      </pane>
      <pane title="Pluralization">
        <div ng-controller="BeerCounter">
          <div ng-repeat="beerCount in beers">
            <ng-pluralize count="beerCount" when="beerForms"></ng-pluralize>
          </div>
        </div>
      </pane>
    </tabs>
  </body>
</html>
```
components.js
```javascript
angular.module('components', [])
 
  .directive('tabs', function() {
    return {
      restrict: 'E',
      transclude: true,
      scope: {},
      controller: function($scope, $element) {
        var panes = $scope.panes = [];
 
        $scope.select = function(pane) {
          angular.forEach(panes, function(pane) {
            pane.selected = false;
          });
          pane.selected = true;
        }
 
        this.addPane = function(pane) {
          if (panes.length == 0) $scope.select(pane);
          panes.push(pane);
        }
      },
      template:
        '<div class="tabbable">' +
          '<ul class="nav nav-tabs">' +
            '<li ng-repeat="pane in panes" ng-class="{active:pane.selected}">'+
              '<a href="" ng-click="select(pane)">{{pane.title}}</a>' +
            '</li>' +
          '</ul>' +
          '<div class="tab-content" ng-transclude></div>' +
        '</div>',
      replace: true
    };
  })
 
  .directive('pane', function() {
    return {
      require: '^tabs',
      restrict: 'E',
      transclude: true,
      scope: { title: '@' },
      link: function(scope, element, attrs, tabsController) {
        tabsController.addPane(scope);
      },
      template:
        '<div class="tab-pane" ng-class="{active: selected}" ng-transclude>' +
        '</div>',
      replace: true
    };
  })
```

app.js
```javascript
angular.module('app', ['components'])
 
.controller('BeerCounter', function($scope, $locale) {
  $scope.beers = [0, 1, 2, 3, 4, 5, 6];
  if ($locale.id == 'en-us') {
    $scope.beerForms = {
      0: 'no beers',
      one: '{} beer',
      other: '{} beers'
    };
  } else {
    $scope.beerForms = {
      0: '?iadne pivo',
      one: '{} pivo',
      few: '{} pivá',
      other: '{} pív'
    };
  }
});
```

bootstrap.css
```css
// Uncomment this in Plunker or JSFiddle: @import '//netdna.bootstrapcdn.com/twitter-bootstrap/2.0.4/css/bootstrap-combined.min.css';
```

###可测性内置

####可注入

在AngularJS依赖性注入允许你声明式地描述你的应用程序是如何连线的。这意味着你的应用程序不需要main()方法，该方法通常是一个难以维护的大堆杂。依赖性的注入也是AngularJS的一个核心。这意味着任何不适合你的需要的组件可以轻松替换掉。

####可测试

AngularJS设计为从根基开始都是可测试的。它鼓励行为与视图分离、用预绑定来模拟、充分利用依赖性注入。它还配备了端到端的场景分流道，它通过理解AngularJS的内部运作机制消除了测试片层分享。
