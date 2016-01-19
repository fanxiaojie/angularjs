# PhoneCat教程应用程序

AngularJS最好的入方法是跟着教程操作，它带领你经历了一个AngularJS网页应用程序的构建。你将建立的这个应用是一个目录，显示了一个安卓设备的列表，让你能够筛选列表，以只察看你感兴趣的设备，然后查看任何设备的详情。

![运行在浏览器上的演示应用](img/tutorial/catalog_screen.png)

跟随着这个教程以看到Angular如何让浏览器变得更聪明——不需要使用原生的扩展或者插件：

* 查看如何使用客户端数据绑定的示例，以建立数据的动态视图，它会响应用户的操作立即改变自己。
* 查看Angular如何在与你的数据同步的同时，保持你的视图不变，不需要DOM操纵。
* 学一个更好的、更容易的方法，以测试你的网页应用，利用Karma以及Protractor。
* 学会如何使用依赖性注入和服务，以制作常见的网页任务，比如说更容易地在应用中获得数据。

当你看完该教程时你将能够：

* 创建工作在现代浏览器中的动态的应用程序。
* 使用数据绑定以把你的数据模块连接到你的视图中。
* 利用Karma创建并运行单元测试。
* 利用Protractor创建并运行端到端测试。
* 从模板中移出应用逻辑，移到控件中。
* 使用Angular服务从服务器端获得数据。
* 使用ngAnimate把动画应用到你的应用程序中。
* 识别资源以学习更多关于AngularJS。

本教程将指导你完成建立一个应用程序的整个过程，包括编写并运行单元测试和端到端测试。每一步骤的末尾的实验向你提供了学习更多关于AngularJS的建议，以及你正在建立的应用程序的建议。

你可以在几个时内看完整个教程，或者你可能会想愉快地花一天时间真正深入挖掘它。如果你寻求更短的AngularJS的入门，请仔细阅读[起步](https://docs.angularjs.org/misc/started)文档。

# 起步

本页的剩余部分解释了你可以如何设置你的本地机器用于开发。如果你只是想阅读教程，则你可以直接查看第一步：[第一步](step00.html)。

# 操作代码

你可以在你自己的电脑上跟随着这个教程、摆弄代码。用这种方法，你可以得到真正书写AngularJS代码的亲手实践，还使用了推荐的测试工具。

该教程取决于为源代码管理器使用了哪个版本的[Git][git]。除了安装并运行几段git代码，你不需要知道关于Git的任何东西，只要跟着这个教程。

### 安装Git

你可以从<http://git-scm.com/download>下载并安装Git。一旦安装好了，你应该能够访问到`git`命令行工具。你将需要用到的主要命令是：

- `git clone ...` : 把一个远程的知识库克隆到你的本地机器上
- `git checkout ...` : 检查一个特定的分支或一个代码的标记版本以破解

### 下载angular-phonecat

运行以下命令以克隆放置在GitHub上的 [angular-phonecat repository][angular-phonecat]：

```
git clone --depth=14 https://github.com/angular/angular-phonecat.git
```

该命令在你当前的目录中创建了`angular-phonecat`目录。

<div class="alert alert-info">
该`--depth=14`的选项仅仅是告诉Git只拉下来最后的14次提交。这样使下载更小更快。
</div>

把你当前的目录变成`angular-phonecat`。

```
cd angular-phonecat
```

从现在开始，本教程指令，假定你从`angular-phonecat`目录上运行所有的命令。


### 安装Node.js

如果你想运行预配置的本地web服务器以及测试工具，则你还需要[Node.js v0.10.27+][node]。

你可以针对你的操作系统从<http://nodejs.org/download/>下载一个Node.js安装包。

运行以下的命令行，检查你已经安装的Node.js的版本：

```
node --version
```

在基于Debian的发行版中，与别的实用工具有一个名称冲突，它称为`node`。建议的解决方案是再安装`nodejs-legacy` apt 安装包，它会把`node`重命名为`nodejs`。

```
apt-get install nodejs-legacy npm
nodejs --version
npm --version
```

<div class="alert alert-info">
如果你需要在你的本地环境中运行Node.js的不同版本，请考虑安装<a href="https://github.com/creationix/nvm" title="Node Version Manager Github Repo link">Node版本管理器（nvm）</a>。
</div>

一旦你已经在你的机器上安装了Node.js，你可以依靠运行以下代码下载该工具。

```
npm install
```

这个命令读取了angular-phonecat的`package.json`文件，并把以下工具下载到`node_modules`目录中：

- [Bower][bower] - 客户端代码包管理器
- [Http-Server][http-server] - 简单的本地静态web服务器
- [Karma][karma] - 单元测试运行器
- [Protractor][protractor] - 端到端测试运行器

运行`npm install`还将自动使用bower以把该Angular框架下载到`app/bower_component`目录。

<div class="alert alert-info">
注意angular-phonecat项目被设置为通过npm脚本安装并运行这些实用工具。这意味着要想跟随这个教程，你并非一定要让实用工具中的一个全局安装在你的系统中。参见下面的**安装助手工具**以了解更多信息。
</div>

该项目用一些npm助手脚本预配置，以使它容易运行你在开发时需要用到的常见的任务：

- `npm start` : 启动一个本地开发Web服务器
- `npm test` : 启动Karma单元测试运行器
- `npm run protractor` : 运行Protractor端到端（E2E）测试
- `npm run update-webdriver` : 安装Protractor所需要的驱动程序

### 安装助手工具（可选的）

Bower、Http-Server、Karma和Protractor模块也都中可执行的，它们可以全局安装，也可从终端/命令提示符中直接运行。跟随着这个教程，你不需要安装它，但是如果你决定你确实想要直接运行它们，你可以使用`sudo npm install -g ...`来全局安装这些模块。

作为实例，要想安装可执行的Bower命令行，你只需要输入以下指令：

```
sudo npm install -g bower
```

*(Omit the sudo if running on Windows)*

然后你可以直接运行该bower工具了，如下：

```
bower install
```

### 运行开发Web服务器

虽然Angular应用程序是纯客户端代码，而且能够直接从文件系统中，在web浏览器中打开它们，但是最好从一个HTTP web服务器中供应它们。特别是，为了安全原因，如果网页直接从文件系统中加载，很多现代浏览器不允许JavaScript发起服务器请求。

为了在开发期间托管应用程序，用一个简单的静态的web服务器配置angular-phonecat项目。运行以下指令以开启web服务器。

```
npm start
```

这将创建一个本地web服务器，鉴听你的本地机器上的端口8000。现在你可以在这个地址上浏览该应用程序了：

```
http://localhost:8000/app/index.html
```

<div class="alert alert-info">
要想在不同的IP地址或端口上供应该web应用程序，可以编辑package.json内部的“start”脚本。你可以使用`-a`以设置地址，使用`-p`以设置端口。
</div>

### 运行单元测试

我们使用单元测试以确保我们的应用程序中的JavaScript代码正确运行。单元测试关注于应用程序的小型的隔离部分。单元测试保存在`test/unit`目录中。

angular-phonecat项目被配置为使用[Karma][Karma]以针对本应用程序运行该单元测试。运行以下指令以开始Karma。

```
npm test
```

这将开始Karma单元测试运行器。Karma将读取在`test/karma.conf.js`中的配置文件。  这个配置文件告诉Karma要：

- 打开一个Chrome浏览器，把它连接到Karma。
- 在该浏览器中执行所有的单元测试
- 报告在终端/命令行窗口中的那些测试的结果
- 观察所有项目的JavaScript文件，每当有变化时重新运行测试

最后让它一直在后台运行，因为北会给你即时的回调，关于当你在操作代码时，你的改变是否通过了单元测试的回调。

### 运行端到端测试

我们使用端到端测试以确保应用程序作为一个整体运行。端到端测试被设计为测试整个应用客户端应用程序，特别是测试视图是否正确显示并有正确的行为。它在浏览器中运行，通过模拟真实用户与真实应用程序的交互。

端到端测试保存在`test/e2e`目录中。

该angular-phonecat项目被配置为使用[Protractor][protractor]以针对应用程序运行端到端测试。Protractor依赖于一组允许它与浏览器交互的驱动程序。你可以通过运行以下代码以安装这些驱动程序：

```
npm run update-webdriver
```

**你只需要运行它一次。**

因为Protactor通过与正在运行的应用程序交互来起作用，我们需要开启我们的web服务器：

```
npm start
```

然后在一个单独的终端/命令行窗口中，通过运行以下指令，我们可以针对该应用程序运行Protractor测试脚本：

```
npm run protractor
```

Protractor将读取在`test/protractor-conf.js`中的配置文件。该配置文件要求Protractor做：

- 打开一个Chrome浏览器，把它连接到应用程序上
- 在浏览器中执行所有的端到端测试
- 报告在终端/命令行窗口中的那些测试结果
- 关闭浏览器并退出

最好在每当你对HTML视图作了改变的时候运行端到端测试，或者当你想检查该应用程序作为一个整体是否正确执行时，运行端到端测试。通常在把一个新的改变提交到远程知识库之前运行端到端测试。

现在你已经测试好了你的本地机器，让我们开始这个教程吧：[第一步 引导程序](stop00.html)。

[git]: http://git-scm.com/
[node]: http://nodejs.org/
[angular-phonecat]: https://github.com/angular/angular-phonecat
[protractor]: https://github.com/angular/protractor
[bower]: http://bower.io/
[http-server]: https://github.com/nodeapps/http-server
[karma]: https://github.com/karma-runner/karma
