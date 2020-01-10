---
title: webpack配置一
tags:
- webpack
categories:
- webpack            
---

## webpack是什么

WebPack可以看做是模块打包机：找到JavaScript模块以及其它的一些浏览器不能直接运行的拓展语言（Scss，TypeScript等），并将其打包为合适的格式以供浏览器使用。 (支持es6 commonjs模块)
构建就是把源代码转换成发布到线上的可执行 JavaScrip、CSS、HTML 代码

+ 代码转换：TypeScript 编译成 JavaScript、SCSS 编译成 CSS 等。
+ 文件优化：压缩 JavaScript、CSS、HTML 代码，压缩合并图片等。
+ 代码分割：提取多个页面的公共代码、提取首屏不需要执行部分的代码让其异步加载。
+ 模块合并：在采用模块化的项目里会有很多个模块和文件，需要构建功能把模块分类合并成一个文件。
+ 自动刷新：监听本地源代码的变化，自动重新构建、刷新浏览器。
+ 代码校验：在代码被提交到仓库前需要校验代码是否符合规范，以及单元测试是否通过。
+ 自动发布：更新完代码后，自动构建出线上发布代码并传输给发布系统。


## 基础配置

"build": "npx webpack --config ./build/webpack.base.js
```js
const path = require('path');
const resolve = (...filename)=>path.resolve(...filename)
module.exports = {
    mode:"development",
    entry:resolve(__dirname,"..","./src/main.js"),
    output:{
        filename:"bundle.js",
        path:resolve(__dirname,"..",'./dist')  //必须绝对路径
    }
}

```


webpack 配置可以是个函数，函数的参数是传入的参数
"build": "npx webpack --config ./build/webpack.base.js --env.production"
```js
module.exports = (env)=>{  
    console.log(env)
}
```


分成三个部分  1.基础  2.开发环境  3.生产环境

webpack.base.js
```js
const path = require('path');
const devConfig = require("./webpack.dev");
const prodConfig = require("./webpack.pro");
const merge = require('webpack-merge'); //合并两个对象
const htmlWebpackPlugin = require('html-webpack-plugin')
const resolve = (...filename)=>path.resolve(...filename)

module.exports = (env)=>{  //webpack 配置可以是个函数，函数的参数是传入的参数
   const isDev = env.development;
   const baseCondig = { //多入口可以改成对象/数组/字符转
    entry:resolve(__dirname,"..","./src/main.js"),
    output:{
        filename:"bundle.js",
        path:resolve(__dirname,"..",'./dist')  //必须绝对路径
    },
    plugins:[
        new htmlWebpackPlugin({
            template:resolve(__dirname,"../public/index.html"),
            filename:"index.html",
            hash:true, //防止缓存
            minify:!isDev?{ //是否压缩
                removeAttributeQuotes:true, //删除双引号
                collapseWhitespace:true //删除空格
            }:false,
            //excludeChunks:['main']  //排除某个模块
            // chunksSortMode:"manual" //手动排序 +chunks
            chunks:["main"] //需要引入的代码块
        })
    ]
   }

   return isDev?merge(baseCondig,devConfig):merge(baseCondig,prodConfig)
}
```
webpack.dev.js
```js
const path = require('path');
module.exports = {
    mode:"development",
    devServer:{ //开发服务
        //更改静态文件基础的目录
        contentBase:path.resolve(__dirname,'../dist'),
        compress:true,//启动gzip压缩
        port:3000,
        open:true//自动打开浏览器
    }
}

```
webpack.pro.js
```js
module.exports = {
    mode:"production",
}
```



安装
npm i webpack-merge
npm i webpack-dev-server
npm i html-webpack-plugin


