# webpack

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

####上线
另一份配置文件
用 webpack --config webpack.min.js 指定另一个名字的配置文件
这个文件当中可以写不一样配置, 专门用于代码上线时的操作.

	webpack --config webpack.min.js

注意在这个文件下 ，webpack-dev-server 就无法使用了


为了解决react使用hot 而无法在配置中使用query 配置。

	var babelPresets = {presets: ['react', 'es2015']};
	loaders: ['react-hot', 'babel-loader?'+JSON.stringify(babelPresets)]
