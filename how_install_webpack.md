# 安装webpack

标签（空格分隔）： 模块化

---

#### node.js
安装`node.js`
node.js提供了一个包管理工具(`npm`)。

#### webpack
webpack可以通过`npm`安装：
```shell
$ npm install webpack -g
```
webpack现在就被安装到了全局环境中，在终端中使用`webpack`命令就可以了。

##### 在一个项目中使用webpack
最好让WebPack依赖于你的项目。通过这个，你可以选择一个本地的WebPack版本，将不会被强制使用单一的全局的一个。
> 为npm添加一个`package.json`配置文件：
```shell
$ npm init
```

如果你不希望你的项目发布到npm上，那么上面的哪个就不重要了，可以忽略。

安装并且添加webpack：
```shell
$ npm install webpack --save-dev
```
#### 版本
安装固定的版本
```shell
$ npm install webpack@1.2.x --save-dev
```

#### 开发工具
如果你想使用开发工具，那么你就需要安装它们。
```shell
$ npm install webpack-dev-server --save-dev
```

