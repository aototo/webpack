全局安装webpack
	
	$ sudo cnpm install webpack -g

进入 项目

	$ cnpm init 
	$ cnpm install webpack --save-dev

打包文件

	$ webpack entry.js bundle.js

webpack 会分析依赖的部分比如在name.js 写
	
	module.exports = "aotu";

到出一个模块到entry.js
	
	var name = require('./name');

打包之后就会在生成的文件看到所依赖的模块。

Webpack 默认只能处理 JavaScript .. 如果你打算用它处理其它的东西，比如 html，css 等等，你需要安装对应的` loader ..`
webpack 里说的` loader `有点像是一种转换器 .. 它可以把资源从一种形式转换成另一种形式 .. 不过最终得到的还是 JavaScript ..

进入到项目所在的目录 .. 使用 npm install .. 去安装一下 css-loader .. 还有 style-loader .. 把它们也保存到项目的开发依赖里 ...

	$ cnpm install css-loader style-loader --save-dev

然后在entry.js 里面使用

	require('style!css!./style.css');


webpack 的配置文件是 `webpack.config.js` ，你可以在项目的根目录下去创建一个这样的文件，然后在里面可以描述一下让 webpack 做的一些事情 ..

```javascript
module.exports = {
  entry:'./entry.js',//Need to bundle files
  output:{
    path:__dirname,//Output path
    filename:"bundle.js"
  }
  module:{  //need module
    loaders:[
      {test:/\.css$/,loader:"style!css"}
    ]
  }
}

```

因为我们在配置文件里设置了一下处理 css 文件要使用的 loader .. 所以在这个 entry.js 里面，导入 css 文件的时候，就不需要指定处理它的 loader 了.
	
	require('./style.css');

我们把应用打包以后，想要调试代码就需要用到` source map `这个东西 ..

	$ webpack --devtool source-map

加入到配置文件

	devtool: "source-map"

webpack 可以跟 babel 放到一块儿用 .. 比如你要打包的项目里面用了一些 `es2015 `或者 `react` 的 `jsx` .. 这些东西你可能需要使用 babel 去转换一下 .. 然后再交给 webpack 去打包 ..

	$ cnpm install babel-loader babel-cli babel-preset-es2015 --save-dev


想在 webpack 里使用 babel 需要一个` babel-loader`

Create a file named .babelrc
	
	{
	  "presets":["es2015"]
	}


In webpack.config.js module loaders file inside add test

	{test:/\.js$/,loader:"babel"}

其他模块就可以使用 es6 的写法 
	
	let name = 'aotu';
	export default name;

####webpack-dev-server

`webpack-dev-server` 这个东西可以给为我们生成一个开发用的服务器，在文件有变化的时候自动给我们打包，然后刷新页面 .. 它还有个模块热替换的功能 .. 就是它可以只替换有变化的地方 .. 不需要刷新整个页面 ... 这个功能有很多好处 ..

我们可以先在全局范围安装一下 webpack-dev-server 

	$ sudo cnpm install webpack-dev-server -g

本地项目

	$ cnpm install webpack-dev-server --save-dev

然后执行

	$ webpack-dev-server --inline --hot

结合react
	
	npm install babel-cli babel-preset-es2015 babel-preset-react webpack webpack-dev-server babel-loader react-hot-loader --save-dev

把它们都保存到项目的开发依赖里 ..

完成以后再去安装一下 react ... 还有 react-dom ..

	$ cnpm install react react-dom --save

编辑一下 webpack 的配置文件 .. 在所有 js 文件里面，除了使用 baebl 这个 loader，再用一下 react-hot 这个 loader ..

	{ test: /\.js$/, exclude: /node_modules/, loader: 'react-hot!babel' },

. 打开 package.json .. npm 的配置文件 .. 在它的 scripts 里面，添加一个命令 .. 比如 watch .. 它的值就是要执行的命令 .. webpack-dev-server --inline --hot ..

	"watch": "webpack-dev-server --inline --hot"


使用 loaders , 比如:file-loader
	
	$ npm install xxx-loader --save

	{ test: /\.png$/, loader: "file?name=dist/app/assets/[hash].png"}

下面是输出的文件，css用了extract-text-webpack-plugin 插件单独导出css文件，要注意的是css里面的图片,字体的引用问题，如果在开发阶段可以不使用 , url-loader 其实就是file-loader  的封装，用它来处理要加载的文件
```javascript
var ExtractTextPlugin = require("extract-text-webpack-plugin");
var webpack = require("webpack");
var path = require('path');
var babelPresets = {presets: ['react', 'es2015']};
module.exports = {
  entry: {
    app: ["./app/main.js"]
  },
  output: {
    path: path.resolve(__dirname, "build"),
    // publicPath: "assets", 通过  localhost:8080/assets/bundle.js  来访问
    publicPath:"/",
    filename: "bundle.js"
  },
  devtool: "source-map",
  module:{
    loaders:[
      {
        test: /\.js$/,
        exclude: /node_modules/,
        loaders: ['react-hot', 'babel-loader?'+JSON.stringify(babelPresets)],// 'babel-loader' is also a legal name to reference
      },
      // { test: /\.css$/, loader: ExtractTextPlugin.extract("style-loader", "css-loader",{
      //   publicPath: "../../"
      // })},
      {test: /\.css$/, loader: 'style!css'},
      {
          test: /\.(png|jpe?g|eot|svg|ttf|woff2?)$/,
          loader: "file?name=assets/images/[hash][name].[ext]"
      },
    ]
  },
  plugins: [
    new ExtractTextPlugin( "assets/stylesheets/[name].css" ,  {
        allChunks: true
    }),
    // new webpack.HotModuleReplacementPlugin()
	]
}


```
test: /\.(png|jpe?g|eot|svg|ttf|woff2?)$/, 各种需要用的文件格式
loader: "file?name=assets/images/[hash][name].[ext]"   [hash]会生成hash的字符，也可以选择你要的生成的个数。
####上线
另一份配置文件
用 webpack --config webpack.min.js 指定另一个名字的配置文件
这个文件当中可以写不一样配置, 专门用于代码上线时的操作.

	webpack --config webpack.min.js

注意在这个文件下 ，webpack-dev-server 就无法使用了


为了解决react使用hot 而无法在配置中使用query 配置。这是个坑要注意，除非你在根目录下有.babelrc 的文件，不然就像下面一样拼接的方式，不然会报错。

	var babelPresets = {presets: ['react', 'es2015']};
	loaders: ['react-hot', 'babel-loader?'+JSON.stringify(babelPresets)]




####react-hot-boilerplate 配置  这是一位大神配置的，自定义了一份server 配置文件，可以前去参观下载！
https://github.com/gaearon/react-hot-boilerplate

```javascript
var path = require('path');
var webpack = require('webpack');

module.exports = {
  devtool: 'eval',
  entry: [
    'webpack-dev-server/client?http://localhost:3000',
    'webpack/hot/only-dev-server',
    './src/index'
  ],
  output: {
    path: path.join(__dirname, 'dist'),
    filename: 'bundle.js',
    publicPath: '/static/'
  },
  plugins: [
    new webpack.HotModuleReplacementPlugin()
  ],
  module: {
    loaders: [{
      test: /\.js$/,
      loaders: ['react-hot', 'babel'],
      include: path.join(__dirname, 'src')
    }]
  }
};


```
还有个server.js

```javascript
var webpack = require('webpack');
var WebpackDevServer = require('webpack-dev-server');
var config = require('./webpack.config');

new WebpackDevServer(webpack(config), {
  publicPath: config.output.publicPath,
  hot: true,
  historyApiFallback: true
}).listen(3000, 'localhost', function (err, result) {
  if (err) {
    return console.log(err);
  }

  console.log('Listening at http://localhost:3000/');
});

```

