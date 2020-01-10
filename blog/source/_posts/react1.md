---
title: react 一 react是什么
tags:
- react
categories:
- react 
---

React 是一个用于构建用户界面的JavaScript库 核心专注于视图,目的实现组件化开发

## 组件化的概念
我们可以很直观的将一个复杂的页面分割成若干个独立组件,每个组件包含自己的逻辑和样式 再将这些独立组件组合完成一个复杂的页面。 这样既减少了逻辑复杂度，又实现了代码的重用
可组合：一个组件可以和其他的组件一起使用或者可以直接嵌套在另一个组件内部
可重用：每个组件都是具有独立功能的，它可以被使用在多个场景中
可维护：每个小的组件仅仅包含自身的逻辑，更容易被理解和维护

## 搭建React开发环境

```bash
npm i create-react-app -g
create-react-app xxx 
cd xxx
npm start
```

## JSX
### 什么是JSX

是一种JS和HTML混合的语法,将组件的结构、数据甚至样式都聚合在一起定义组件

```js
import React from 'react';
import ReactDOM from 'react-dom';

//JSX  js+xml   js和html混合书写的的语法  浏览器不支持
ReactDOM.render(<h1>Hello</h1>, document.getElementById("root"))


//<h1>Hello</h1> = React.createElement("h1", null, "Hello");
```

### 什么是元素

* JSX其实只是一种语法糖,最终会通过[babeljs](https://www.babeljs.cn/repl)转译成createElement语法
* React元素是构成React应用的最小单位
* React元素用来描述你在屏幕上看到的内容
* React元素事实上是普通的JS对象,ReactDOM来确保浏览器中的DOM数据和React元素保持一致


```js
let element1 = let element1 = <h1 id='hello' className='title' style={{border:"1px solid #000"}}>Hello</h1>
let element2 = React.createElement("h1", null, "Hello")  //虚拟dom
//element1 = element2
console.log(element2) // {type:'h1',props:{children:'Hello'}}
console.log(element1) // {type:'h1',props:{id: "hello",className: "title",style:border: "1px solid #000",children: "Hello"}} 所有属性都在props里面
```

### JSX也是对象
* 可以在if或者 for语句里使用JSX
* 将它赋值给变量，当作参数传入，作为返回值都可以
```js
function geeting(name){
    return (<div>Hello {name}</div>)
}

let element1 = geeting("wxc")
ReactDOM.render(element1, document.getElementById("root"))
------------------------------------------------------------------
let names =[1,2,3];
let lis = []
for(let i = 0;i<names.length;i++){
    lis.push(<li key={i}>{names[i]}</li>)  //加入key 是为了优化
}
ReactDOM.render(<ul>{lis} </ul>, document.getElementById("root"))

```

### 映射

```js
import React from 'react';
import ReactDOM from 'react-dom';

let element =(
    <div>
        <span>1</span>
        <span>2</span>
        <span>3</span>
    </div>
)
let spans = [
    <span>1</span>,
    <span>2</span>,
    <span>3</span>
]

// let divs = spans.map((item,index)=><div key={index}>{item}</div>)
let divs = React.Children.map(spans,(item,index)=><div key={index}>{item}</div>)

ReactDOM.render(element, document.getElementById("root"))
ReactDOM.render(<div>{divs}</div>, document.getElementById("root"))
```

### 元素更新
React 元素都是immutable不可变的。如果一个元素创建了 不会去修改它
更新界面的唯一办法是创建一个新的元素，然后将它传入ReactDOM.render()方法
```js
let element = <div>{new Date().toLocaleString()}</div>

setInterval(()=>{
    //element.props.children = new Date().toLocaleString()  //Object.freeze
    let element = <div>{new Date().toLocaleString()}</div>
    ReactDOM.render(element, document.getElementById("root"))
},1000)


function freeze(obj){
    //把属性的writable改为false

}
```
