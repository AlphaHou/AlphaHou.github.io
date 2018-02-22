---
layout:     post
title:      webpack-bootstrap-setting
date:       2018-02-22 19:00:00
author:     "Hou"
header-img: "img/post-bg-node.png"
tags:
    - webpack
    - bootstrap
---
webpack 配置
============


>使用webpack引入bootstrap的优点有两个，一是可以模块热替换，二是可以打包减少请求次数。



## webpack配置

webpack引入bootstrap有两大坑，一是jquery的引入，二是popper.js的引入。package.json依赖如下：


```
  "dependencies": {
    "babel-core": "^6.26.0",
    "babel-loader": "^7.1.2",
    "babel-plugin-transform-runtime": "^6.23.0",
    "babel-preset-es2015": "^6.24.1",
    "bootstrap": "^4.0.0",
    "css-loader": "^0.28.9",
    "expose-loader": "^0.7.4",
    "file-loader": "^1.1.6",
    "jquery": "^3.3.1",
    "less": "^2.7.3",
    "less-loader": "^4.0.5",
    "popper.js": "^1.12.9",
    "style-loader": "^0.20.1",
    "url-loader": "^0.6.2",
    "vue": "^2.5.13",
    "vue-loader": "^14.1.1",
    "vue-preview": "^1.0.5",
    "vue-resource": "^1.3.6",
    "vue-template-compiler": "^2.5.13",
    "webpack": "^3.10.0",
    "webpack-dev-server": "^2.11.1"
  },
"scripts": {
    "test": "echo \"Error: no test specified\" && exit 1",
    "start": "webpack-dev-server --open"
  },
  ```


  webpack.config.js代码如下：


```
  const path=require('path')
const webpack=require('webpack')
module.exports={
    entry:'./src/main.js',
    devServer: {
        contentBase:'./dist',
        hot: true
    },
    output:{
        filename:'bundle.js',
        path:path.resolve(__dirname,'dist')
    },
    plugins:[
    new webpack.ProvidePlugin({
        $ : "jquery",
        jQuery : "jquery",
        'window.$':'jquery',
        "window.jQuery" : "jquery",
        Popper: ['popper.js', 'default'],
    }),
    new webpack.NamedModulesPlugin(),
    new webpack.HotModuleReplacementPlugin()
    ],
    module:{
        rules:[{
            test:/\.vue$/,
            use:'vue-loader'
        },
        {
            test:/\.css$/,
            use:['style-loader',
            'css-loader']
        },
        {
            test:require.resolve('jquery'),
            loader:'expose-loader?$!expose-loader?jQuery'
        },
        {
            test:/\.less$/,
            use:['style-loader',
            'css-loader']

        },
        {
            test:/\.(jpg|png|svg|ttf|woff|woff2|gif)$/,
            loader:'url-loader',
            options:{
                limit:4096,
                name:'[name].[ext]',
            }
        },
        { test: /\.(woff|woff2)$/, loader:"url-loader?prefix=font/&limit=5000" },
        　　 { test: /\.ttf(\?v=\d+\.\d+\.\d+)?$/, loader: "url-loader?limit=10000&mimetype=application/octet-stream" },
            { test: /\.svg(\?v=\d+\.\d+\.\d+)?$/, loader: "url-loader?limit=10000&mimetype=image/svg+xml" },
        {
            test:/\.js$/,
            loader:'babel-loader',
            exclude: /node_modules/,
            options: {
                presets :['es2015'],
                plugins:['transform-runtime'],
            }
        }]
    }
}
```