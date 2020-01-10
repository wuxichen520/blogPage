---
title: react 二 组件&props
tags:
- react
categories:
- react 
---
## 定义组件
1.函数式
2.类
返回的是一个React的顶级元素
### 函数(定义的)组件
```js
/**
 * 1.函数式 
 * React 是如何渲染的
 * 1.把所有的属性组合成一个对象
 * 2.把属性对象作为参数传给函数组合
 * 3.函数组件会返回React函数
 * 4.然后由ReactDOM.render方法把虚拟DOM（React元素）转为真实元素并且插入页面中
 */

 function Welcome(props){
     return (
         <h1>hello {props.name} <span>world</span></h1>
     )
 }
 ReactDOM.render(<Welcome name='ww'></Welcome>,document.getElementById("root"))
```

### 类(定义的)组件
```js
/**
 * 类
 * React 是如何渲染的
 * 1.收集props对象
 * 2.把props对象传递给Welcome类的构造函数  new Welcome(props) 然后获取Welcome的实例
 * 3.调用实例上的render方法进行渲染，获得返回的React元素
 * 4.然后把此元素渲染到页面上
 */
class Welcome extends React.Component{
    constructor(props){
        super(props)  //把props传递给父组件
    }
    render(){
        return   (
                      <h1>hello {this.props.name} <span>world</span></h1>
                 )
    }
}
 ReactDOM.render(<Welcome name='ww'></Welcome>,document.getElementById("root"))


```

## 组件渲染
```js
class Welcome extends React.Component{
    constructor(props){
        super(props)
    }
    render(){
        return   (
                      <h1>hello {this.props.name} <span>world</span></h1>
                 )
    }
}
//createElement 对一个参数是元素类型  字符串/类组件/函数   React是通过字母是否大写区分是否是原生DOM组件和自定义（函数组件和类组件）  自定义组件是大写
let element = <Welcome name='ww' /> // React.createElement(Welcome,{name:'ww'})

 ReactDOM.render(<Welcome name='ww'></Welcome>,document.getElementById("root"))

```

## 复合组件 & 提取组件
```js

import React from 'react';
import ReactDOM from 'react-dom';
/**
 * 复合组件
 * 如何划分组件的维度或者说粒度
 * 太大 组件太复杂，不好易维护
 * 太小太细 组件太简单，而且数量会非常的多，也难以管理和维护
 * 高内聚，低耦合
 * 功能要高度内聚，组件和组件之间尽量不要耦合
 * 什么需要拆分组件
 * 1.组件已经太复杂了，无法管理了
 * 2.组件需要被复用  
 * 
 * 1. 如何进行组件的拆分和组件
 * 2. 组件内的数据如何传递
 */
class PanelHead extends React.Component {
    render() {
        return (
            <div className="panel-heading" style={{ border: `1px solid ${this.props.color}` }}>
                我是面板的头部
            </div>
        )
    }
}
class PanelBody extends React.Component {
    render() {
        return (
            <div className="panel-body" style={{ border: `1px solid ${this.props.color}` }}>
                我是面板主体部分
            </div>
        )
    }
}
class PanelFooter extends React.Component {
    render() {
        return (
            <div className="panel-footer" style={{ border: `1px solid ${this.props.color}` }}>
                我是面板的尾部
            </div>
        )
    }
}
class Panel extends React.Component {
    render() {
        return (
            <div className="panel panel-default" style={{ border: '1px solid red' }}>
                <PanelHead color="red" />
                <PanelBody color="red" />
                <PanelFooter color="red" />
            </div>
        )
    }
}
ReactDOM.render(<Panel />, document.getElementById('root'));






```

## Props的只读性
无论是使用函数或是类来声明一个组件，它决不能修改它自己的props
纯函数没有改变它自己的输入值，当传入的值相同时，总是会返回相同的结果
所有的React组件必须像纯函数那样使用它们的props
```js
/**
 * 函数式编程
 * 什么叫纯函数
 * 1.在函数体内部不能改变传入的参数
 * 2.相同的参数一定要返回相同的值
 * 3.不能修改作用域变量之外的值
 */
function withdraw(account, amount) {
     account.balance -= amount; //在函数体内部不能改变传入的参数
}
withdraw({ name: '张三', balance: 1000 }, 100);


function sum(a, b) {
    return a + b + Math.random(); //相同的参数一定要返回相同的值
}
sum(1, 2)
sum(2, 3);

function add(a, b) {
    //global.x = 'a'; //不能修改作用域变量之外的值
    let c = 10;//可以修改在函数作用域内声明的变量
    c = 20;
    return a + b + c;
}
//就是纯函数
function Welcome(props) {
    return <div>hello{props}</div>
}
```

## 类型检查
要在组件的 props 上进行类型检查，你只需配置特定的 propTypes 属性
您可以通过配置特定的 defaultProps 属性来定义 props 的默认值：

| 用法 | 含义 |
| :-| -: |
| PropTypes.array |	数组 |
| PropTypes.bool |	布尔类型 |
| PropTypes.func |	函数 |
| PropTypes.number |	数字 |
| PropTypes.object |	对象 |
| PropTypes.string |	字符串 |
| PropTypes.symbol |	Symbol |
| PropTypes.node |	任何可被渲染的元素(包括数字、字符串、元素或数组) |
| PropTypes.element |	一个 React 元素 |
| PropTypes.instanceOf(Message) |	Message类的实例 |
| PropTypes.oneOf(['News', 'Photos']) |	枚举类型 |
| PropTypes.oneOfType([PropTypes.string,PropTypes.
number,PropTypes.instanceOf(Message)]) |	几种类型中的任意一个类型 |
| PropTypes.arrayOf(PropTypes.number) |	一个数组由某一类型的元素组成 |
| PropTypes.objectOf(PropTypes.number) |	可以指定一个对象由某一类型的值组成 |
| PropTypes.shape({color: PropTypes.string,fontSize: PropTypes.number}) |	可以指定一个对象由特定的类型值组成 |
| PropTypes.func.isRequired |	你可以在任何 PropTypes 属性后面加上 isRequired ，确保这个 prop 没有被提供时，会打印警告信息 |
| PropTypes.any.isRequired |	任意类型的数据 
| customProp: function(props, propName, componentName){} |	你可以指定一个自定义验证器。它在验证失败时应返回一个 Error 对象 |


```js
import React from 'react';
import ReactDOM from 'react-dom';
import PropTypes from 'prop-types';//现在组件属性校验器已经变成了一个独立的模块

/**
 *  属性校验
 * 1. 为什么需要属性校验
 * 
 */
class Person extends React.Component {
    static defaultProps = {  //默认属性 无需传值
        gender: 'male'
    }
    static propTypes = {
        //age: PropTypes.number.isRequired,
        gender: PropTypes.oneOf(['male', 'female']).isRequired,
        hobby: PropTypes.arrayOf(PropTypes.string).isRequired,
        position: PropTypes.shape({
            x: PropTypes.number,
            y: PropTypes.number
        }),
        // age: function (props, propName, componentName) {
        //     if (props.age < 18) {
        //         throw new Error('你还未成年');
        //     }
        // }
    }
    render() {
        return (
            <table>
                <thead>
                    <tr>
                        <td>age</td>
                        <td>gender</td>
                        <td>hobby</td>
                        <td>position</td>
                        <td>friends</td>
                    </tr>
                </thead>
                <tbody>
                    <tr>
                        <td>{this.props.age}</td>
                        <td>{this.props.gender}</td>
                        <td>hobby</td>
                        <td>position</td>
                        <td>friends</td>
                    </tr>
                </tbody>
            </table>
        )
    }
}
let personProps = {
    // age: 10,//年龄
    //gender: 'male',//性别 male female
    hobby: ['basketball', 'football'],//爱好
    position: { x: 10, y: 10 },//地理坐标
    friends: [{ name: 'zhangsan', age: 10 }, { name: 'lisi', age: -20 }]
}
ReactDOM.render(<Person {...personProps} />, document.getElementById('root'));

```