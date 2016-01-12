```js
var webpack = require('webpack');
/**
 * 插件
 * @link http://segmentfault.com/q/1010000004273417?_ea=552328
 * @type {*}
 */
var HtmlWebpackPlugin = require('html-webpack-plugin');
var path = require('path');
/**
 * 利用process.env定义开发环境还是生产环境
 * @type {webpack.DefinePlugin}
 */
var definePlugin = new webpack.DefinePlugin({
    __DEV__: JSON.stringify(JSON.parse(process.env.BUILD_DEV || 'true')),
    __PRERELEASE__: JSON.stringify(JSON.parse(process.env.BUILD_PRERELEASE || 'false'))
});
console.log('__DEV__', definePlugin.definitions.__DEV__);
console.log('__PRERELEASE__', definePlugin.definitions.__PRERELEASE__);
/**
 * webpack配置文件
 */
module.exports = {
    /**
     * 入口和输出的路径判断都是根据此选项判断
     * @default process.cwd()
     */
    context: process.cwd(),
    /**
     * 是否开启开发者工具
     * @default false
     * @description Prefixing @, # or #@ will enforce a pragma style. (Defaults to #, recommended)
     * eval 每个模块都通过eval来执行
     * source-map 每个文件最下面会添加sourceMap //# sourceMappingURL=chunk.js.map
     * hidden-source-map 与source-map相反
     * eval-source-map 每个模块都通过eval执行,并且都有//# sourceMap
     * cheap-source-map  每个文件中忽略加载器的sourceMap和行数映射
     * cheap-module-source-map 和上面的一样,唯一的不同就是每个模块
     */
    devtool: '#source-map',
    /**
     * 模块加载入口
     * @type [string|Object|Array]
     */
    entry: {
        // 改变key 'js/index' 输出指定目录
        'index': "./src/entry.js",
        // vendor表示单独合并
        vendor: ['jQuery', 'aliasAsync/alias'] //单独合并第三方库
    },
    /**
     * 输出配置
     */
    output: {
        // 输出路径
        path: path.join(__dirname, '/dest'),
        // filename: 'bundle-[id]-[name]-[hash].js',hash chunkhash
        // 输出文件名
        // 名称中可以使用[id] [name] [hash]
        filename: '[name]-bundle.js',
        // 需要异步加载的chunk文件名
        // chunk文件中可以使用[hash](ps:和filname中的hash相同) [chunkhash]
        chunkFilename: 'chunk.js',
        // 公共引用路径,就像图片,css
        // <link href="http://localhost:63343/teapot/dest/spinner.gif"/>
        publicPath: 'http://localhost:63343/teapot/dest/',
        // sourceMap文件名
        // 可使用[file] [id] [hash]
        sourceMapFilename: '[file].map',
        // webpack异步加载chunks使用JOSNP函数名
        // 默认值为 webpackJsonp
        jsonpFunction: 'webpackJsonp',
        // 通过注释的形式来包含模块的信息
        // 生产环境勿用
        pathinfo: true,
        /**
         * 如何向外暴露
         * @default var
         * this Export by setting a property of this: this["Library"] = xxx
         * commonjs   exports["Library"] = xxx
         * commonjs2  module.exports = xxx
         * amd Export to AMD (optionally named - set the name via the library option)
         * umd Export to AMD, CommonJS2 or as property in root 编写类库十分合适
         */
        libraryTarget: 'var',
        // 暴露到全局中的字段名
        library: 'yunVipAPI'
    },
    /**
     * 编译模块配置项
     */
    module: {
        // 加载器,根据文件后缀名或者指定的文件路径来匹配加载器
        loaders: [
            // 输出到指定目录
            {
                test: /\.png/,
                include: [
                    path.resolve(__dirname, './img')
                ],
                exclude: [],
                loader: 'url-loader?limit=1024&name=[path][name].[ext]?[hash:8]&context=src'
            },
            {test: /\.css/, loader: 'style!css'},
            {test: require.resolve('./src/entry.js'), loader: 'expose?yunVipAPI'} //暴露到全局中
        ],
        // 不解析指定文件 里面传正则表达式,前提是这个模块没有依赖其他模块
        // module.noParse 是 webpack 的另一个很有用的配置项，如果你 确定一个模块中没有其它新的依赖 就可以配置这项，webpack 将不再扫描这个文件中的依赖。
        noParse: []
    },
    /**
     *  声明该包为外部依赖.不需要webpack处理
     *  [Object|string|RegExp]
     *  引用外部的package的方式根据libraryTarget变化
     *  @see http://webpack.github.io/docs/configuration.html#externals
     */
    externals: [
        {
            _: true // 这种方式需要注意,必须在html中先引用<script src="http://apps.bdimg.com/libs/underscore.js/1.7.0/underscore-min.js"></script>
            // 等同 {'_':'http://apps.bdimg.com/libs/underscore.js/1.7.0/underscore-min.js'} 这种方法不需要提前在html中script,不过package的体积会增加
        }
        // /\_/ig //使用正则匹配,这种方法和116行一样
    ],
    /**
     * 帮助模块解析的配置项
     */
    resolve: {
        // 依次按指定顺序后缀名解析require的文件
        extensions: ['', '.js', '.json', '.coffee'],
        // 别名配置
        alias: {
            // 可以是别名也可以是路径
            aliasAsync$: './async.js',
            'jQuery': process.cwd() + '/src/libs/jquery.js'
        },
        // 该配置文件中的require从哪里找 (PS:必须是绝对路径)
        root: [process.cwd() + '/src', process.cwd() + '/node_modules']
    },
    /**
     * 目标使用环境,默认为web环境
     * @default web
     * web
     * webworker
     * node
     * async-node
     * node-webkit
     * electron
     */
    target: 'web',
    // 插件集合
    plugins: [
        //plugins: [definePlugin,
        function () {
            this.plugin('done', function (stats) {
                //console.log(stats);
            });
        }, new HtmlWebpackPlugin({
            title: 'Custom template',
            // Load a custom template
            template: 'index.html',
            // Inject all scripts into the body
            inject: 'head'
        }),
        // 这是妮第三方库打包生成的文件
        // 生成的common chunk文件必须在主bundle之前引用,例如
        // <script src="http://localhost:63343/teapot/dest/vendor.base.js"></script>
        // <script src="http://localhost:63343/teapot/dest/index-bundle.js"></script>
        new webpack.optimize.CommonsChunkPlugin({
            name: 'vendor',
            filename: 'vendor.base.js',
            minChunks: 3
        }),
        // 往每个文件头添加banner
        new webpack.BannerPlugin('created by 张亚涛')],
    /**
     * 使用webpack-dev-server时 可配置此项
     */
    devServer: {},
    /**
     * node环境的配置项
     */
    node: {
        console: false,
        global: true,
        process: true,
        Buffer: true,
        __filename: "mock",
        __dirname: "mock",
        setImmediate: true
    },
    amd: {
        jQuery: true
    }
};
```
