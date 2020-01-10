---
title: webpack配置二
tags:
- webpack
categories:
- webpack            
---

## babel

 @babel/core(提供转换方法)  @babel/preset-env(es6-es5) babel-loader(用babel转换)

webpack.base.js
```js
 module:{ //要处理模块  
        rules:[
            {  //执行顺序 右往左  从下往上  loader是桥梁
                test:/\.js$/,
                use:['babel-loader','eslint-loader'],
                exclude:/node_modules/
            },
            // {
            //     test:/\.js$/,
            //     use:'eslint-loader',
            //     exclude:/node_modules/,
            //     enforce:'pre' //强制在所有js的loader之前执行
            // }
    ]
```
.babelrc

```json
{
    "presets": [
        ["@babel/preset-env",{
            "corejs":2,
            "useBuiltIns":"usage"
        }]
    ],
    "plugins": [
        
    ]

}
```
## 代码校验
eslint-loader   eslint

npx eslint --init 

```bash
? How would you like to use ESLint? To check syntax, find problems, and enforce code style
? What type of modules does your project use? CommonJS (require/exports)
? Which framework does your project use? None of these
? Does your project use TypeScript? No
? Where does your code run? Browser
? How would you like to define a style for your project? Use a popular style guide
? Which style guide do you want to follow? Airbnb: https://github.com/airbnb/javascript
? What format do you want your config file to be in? JavaScript
```
.eslintrc

```
 rules: {
      "no-console":0
  },
```


