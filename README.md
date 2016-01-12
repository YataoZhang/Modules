# 前端模块化

标签（空格分隔）： 模块化

---

## 前端模块化系列（一）：网站需要模块化的原因
由于我最近在研究前端各种各样的模块化系统，所以就翻译了一篇来自webpack官网的文章，总的来说作者写的还是相当不错的。这样在自己学习的同时也可以与大家共同学习～～～

> 在今天的网站正在逐步的向`web apps`转变。

>+  单个页面中越来越多的Javascript。
>+  在现代浏览器中你可以做越来越多的功能。
>+  少量的全页面刷新，以至于单个页面中有更多的代码。

正因为这些原因造成越来越多的代码镶嵌在浏览器端中。

这样一个大的代码仓库(`code base`)急需做出相应的管理。正好，模块化系统提供了这些功能分割你的代码仓库，把它们分割为一个个的模块。

### 各个模块系统的风格
眼下对于如何定义依赖项和暴露接口有很多的标准：
>+  &lt;script&gt;标签风格(ps：不使用模块系统)。
>+  CommonJs
>+  AMD和它的一些衍生物
>+  ES6模块
>+  更多。。。

*在下面，我们会一次简介这些模块化系统之间的好处以及坏处。*
#### &lt;script&gt;标签风格
如果你没有使用模块化系统，那么你只能用这种方式来处理你的模块化代码了。
```js
<script src="module1.js"></script>
<script src="module2.js"></script>
<script src="libraryA.js"></script>
<script src="module3.js"></script>
```
每个模块向外暴露一个接口给全局对象，即window对象。模块就可以通过全局对象访问依赖项向外暴露的接口。
> 通常存在的问题

* 全局对象中的变量冲突。
* 按需加载的问题。
* 开发者需要手动解析模块或者库的依赖项。
* 在特别大的项目中，这个现象会变得越来越严重，越来越难以管理。

#### CmmonJs:同步require
这种方式使用了一个同步的require方法去加载依赖项并且返回一个向外暴露的接口。一个模块可以通过给exports添加属性或者给module.exports设置固定值来指定向外暴露的值。
```js
require("module");
require("../file.js");
exports.doStuff = function() {};
module.exports = someValue;
```
*这个只是被`node.js`使用在server端。*
> 优点：

* server端的模块可以被复用。
* 有许多现成的模块以供使用(npm)。
* 非常的简单易用。

> 缺点：

* 因为网络请求都是异步的。所以阻塞式的调用在网路中支持的不是很好。
* 多个模块之间并能同时并行的加载进来。

>* 实现
>* 1、[node.js](http://nodejs.org/) - server端。
>* 2、[browserify](https://github.com/substack/node-browserify)
>* 3、[modules-webmake](https://github.com/medikoo/modules-webmake)－编译到一个bundle里
>* 4、[wreq](https://github.com/substack/wreq)－客户端

#### AMD：异步的require
`Asynchronous Module Definition`
其他的模块化系统(对于浏览器来说)对于同步require(CommonJs)都有或多或少的问题。接下来我们介绍一个异步require的模块化系统(定义模块和暴露值的另外一种实现方式)。
```js
require(["module", "../file"], function(module, file) {
    /* ... */ 
});
define("mymodule", ["dep1", "dep2"], function(d1, d2) {
  return someExportedValue;
});
```
> 优点：

* 十分适合在现下网络的异步请求。
* 支持多个模块的同时并行加载。

> 缺点：

* 写码开销。读写十分的困难。
* 看上去像是一种解决方案。

> 实现
1、[require.js](http://requirejs.org/) - client端。
2、[curl](https://github.com/cujojs/curl) － client端。

获取更多相关[CommonJS](http://webpack.github.io/docs/commonjs.html)和[AMD](http://webpack.github.io/docs/amd.html)的知识。
#### ES6模块化
EcmaScript6针对JavaScript添加了一些新的语言结构，其中就包括模块化系统。
```js
import "jquery";
export function doStuff() {}
module "localModule" {}
```
> 优点：

* 很容易的静态模块解析。
* 未来不久将要作为ES标准来推行。

> 缺点：

* 让大部分的浏览器支持这个功能还需要一段时间。
* 这种风格的模块太少了，让人不适应。

##### 咱谁也不偏向谁的解决方案
给开发者关于模块化风格的选择权。在现有代码可以正常运行的前提下，可以很容易地添加自定义模块。关键还要看使用某个模块系统对于现在的系统影响大不大。

### 模块的传输方式
模块是应该在client端被执行的，所以这就需要它们通过http协议让server端向浏览器端传输。
>* 现在有两种方式来处理如何传输模块
>* 1、一个请求一个模块。
>* 2、所有的模块都在一个请求里。

这两种方式都有人在用，不过这两种都是次优的：

##### 一个请求一个模块
> 优点

* 仅仅传输被请求的那个模块。

> 缺点：

* 许多的请求意为着网络开销也很大。
* 程序启动变慢，因为请求会延迟。

##### 所有的模块都在一个请求里
> 优点

* 很小的请求开销，少量的延迟。

> 缺点：

* 不需要(还没有)被请求的模块也被传输过来了。

### 分块传输方式
相对于上面两种方式都太死板了，所以灵活点的模块传输会不会更好哪？因为向两个极端之间的折中妥协通常都是最好的。
> 虽然我们需要把所有的模块都编辑，把模块安装功能和是否公用拆分为多个较小的代码块。

我们有很多的小量请求。把那些不需要一开始就请求，或者是需要按需加载的模块来进行分块传输。浏览器最开始的访问请求并不用包含你的所有代码库(code base)。这样的话，server返回的数据大小也就会变的很小。有效解决了上面两种方式所出现的问题。
至于`如何分隔模块`应该是开发者根据`功能`,`格式`,`加载顺序`,`继承关系`分割为一个一个单独的部分.
*注意:拆分的粒度问题,可复用问题,效率问题.如何这些问题处理的不好，就有可能出现不想要的后果。*
> 这样的话。就算再多的代码也可以解决掉了。

获取更多相关[代码块如何分割](http://webpack.github.io/docs/code-splitting.html)的知识。

### 为什么仅仅只是JavaScript？
不知道大家有没有发现，为什么一个模块化系统仅仅只帮助开发者处理JavaScript哪？除此之外还需要很多的静态资源需要我们去处理呀！
>+ stylesheets(样式表)
>+ images(图片)
>+ webfonts(web字体)
>+ html for templating(html模版)
>+ 等等...

当然，还有其他的资源：
>+ coffeeScript -> javascript
>+ less -> stylesheets
>+ jade -> 经过javascript生成的html
>+ 等等...

它们也应该向JavaScript一样可以被很容易的require到：
```js
require("./style.css");
```
```js
require("./style.less");
require("./template.jade");
require("./image.png");
```

### 模块静态解析
当编译所有模块的时候，为了协调异步环境下模块开发与性能间的矛盾，我们必须在工程阶段就具备依赖分析的能力，把具备依赖关系的资源进行打包。就算不打包，也希望像 `F.I.S` 那样有个记录依赖关系的map.json，可以照单抓药，一次性地把需要的依赖项加载下来。
#### 策略
一个聪明的解析器将允许大多数现有的代码可以有效运行，不管开发者有没有使用模块化系统。即使开发人员做了一些奇怪的东西，它也会尝试找到最适合的解决方案。实在不行，也就只能抱歉咯！！！

翻译自：http://webpack.github.io/docs/motivation.html
翻译人：张亚涛
