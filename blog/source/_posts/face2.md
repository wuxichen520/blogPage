---
title: react 面试  react 渲染过程
tags:
- react
- 面试
categories:
- face 
- react
---

# 渲染原生组件

虚拟dom属性 ![虚拟dom](/blog/images/xuniDom.png)
## src/index.js
```js
import React from './react';
import ReactDOM from './react-dom';
// let ele = (<button id="sayHello" onClick={onClick}> say <span color='red'>hello</span></button>)
let ele = React.createElement('button',{id:"sayHello",onClick},'say',React.createElement('span',{color:"red"},'hello'))
console.log(ele)
```
## react\index.js
```js
import { TEXT, ELEMENT } from './constants';
import { ReactElement } from './vdom';
function createElement(type, config = {}, ...children) {   // 虚拟dpm
    delete config.__source;
    delete config.__self;
    let { key, ref, ...props } = config;   //  config 为标签上的属性
    let $$typeof = null;
    if (typeof type === 'string') { // span  div button
        $$typeof = ELEMENT;
    }
    props.children = children.map(item => 
        {
            if(typeof item === 'object' || typeof item === 'function' ){  //标签
                return item //React.createElement('span',{color:'red'},'hello')
            }else{
                return { $$typeof: TEXT, type: TEXT, content: item }  //item = 'hello
            }
        })
        return ReactElement($$typeof,type,key,ref,props)
}
const React = {
    createElement
}
export default React;
```
## react\constants.js
```js
export const TEXT = Symbol.for('TEXT');
export const ELEMENT = Symbol.for('ELEMENT');
```
react\vdom.js
```js
/**
 * 
 * @param {*} $$typeof  元素类型  是text  还是element
 * @param {*} type  标签 button  div  span
 * @param {*} key   
 * @param {*} ref  
 * @param {*} props  标签上的属性及child
 */
export function ReactElement($$typeof,type,key,ref,props){   //  虚拟dom
    let  element = {
        $$typeof,
        type,
        key,
        ref,
        props
    }
    return  element
}

```
## react\vdom.js
```js
import { TEXT, ELEMENT } from './constants';
import {setProps} from './utils'
/**
 * 
 * @param {*} $$typeof  元素类型  是text  还是element
 * @param {*} type  标签 button  div  span
 * @param {*} key   
 * @param {*} ref  
 * @param {*} props  标签上的属性及child
 */
export function ReactElement($$typeof,type,key,ref,props){   //  虚拟dom
    let  element = {
        $$typeof,
        type,
        key,
        ref,
        props
    }
    return  element
}
export function createDom(element){
    let {$$typeof} = element;  //类型
    let dom = null;
    if(!$$typeof){ //不存在说明没有类型  string/number
        dom = document.createTextNode(element)
    } else if($$typeof == TEXT){
        dom = document.createTextNode(element.content)
    }
    else if($$typeof == ELEMENT){
        dom = createNativeDom(element)
    }

    return  dom
}

 function createNativeDom(element){
    let {type,props} = element
    let dom = document.createElement(type);  //真实dom  span  button
    //创建子节点
    createNativeDomChildren(dom,element.props.children)
    setProps(dom,props) //给真实DOM传属性及方法
    return  dom
}

 function createNativeDomChildren(parentNode,children){
    children&&children.flat(Infinity).forEach(child => {
            let childDOM = createDom(child)
            parentNode.appendChild(childDOM)
    });
 }
```
## react-dom\index.js
```js

import {createDom} from '../react/vdom'

/**
 * 
 * @param {*} element React节点 因为它可以React元素，也可以是数字 字符串
 * @param {*} container  父容器，它是一个真实DOM元素 
 */


function render(element,container){
   //1.把虚拟dom变成真实dom
   let dom = createDom(element)
   //2.把真实dom挂载到页面上
   container.appendChild(dom)

}

export default {render}
```
## react\utils.js
```js
import {addEvent} from './event'

//如果obj是数组只取第一个元素 否则就返回自己
export function onlyOne(obj){
    return Array.isArray(obj)?obj[0]:obj
}

//给真实DOM节点赋属性
export function setProps(dom,props){
    for(let key in props){
        if(key != 'children'){
            let value  = props[key];
            setProp(dom,key,value)
        }
    }
}
function setProp(dom,key,value){
    if(/^on/.test(key)){ //事件
        // console.log([key.toLowerCase()],value)
        // dom[key.toLowerCase()]=value //改成合成事件   主要原生事件在各个浏览器不一样 
        addEvent(dom,key,value)
    }
    else if(key == 'style'){ //行内样式
        if(value){
            for(let styleName in value){
                if(value.hasOwnProperty(styleName)){
                    dom.style[styleName] = value[styleName];
                }
            }
        }

    }else {  //标签属性
        dom.setAttribute(key, value);
    }
}
```

## react\event.js
react 事件
```js
/**合成事件：
 * 1.可屏蔽浏览器差异   不同浏览器绑定事件和触发事件的方法不一样
 * 2.合成事件可以实现事件对对象的复用 ，重用，减少垃圾回收，提高性能
 * 3.因为默认实现批量更新   setState  两个setState合并一次更新也是在合成事件中
 * 
 * 
 * 在React中  并不是把事件绑定在DOM节点中，而是绑定在document上，类似事件委托
 * @param {*} dom        要绑定事件的dom节点
 * @param {*} eventType 事件类型 onClick  onChange
 * @param {*} listener  事件处理函数
 */
export function addEvent(dom,eventType,listener){

    eventType = eventType.toLowerCase();  //onClick=>onclick

    //在要绑定的dom节点上挂在一个对象，准备存放监听函数
    let eventStore = dom.eventStore || (dom.eventStore = {});
 
    //eventStore.onclick = () =>{alert('hello)}
    eventStore[eventType] = listener;
    //document.addEventListener('click',dispatchEvent)
    //第1阶段捕获，第二阶段冒泡
    document.addEventListener(eventType.slice(2),dispatchEvent,false)
    // document.attachEvent()
}

//真正的事件触发的回调统一是dispatchEvent方法
let syntheticEvent;  //合成事件
function dispatchEvent(event){  //event就是原来的事件对象  ，但是传递给我们监听函数并不是它
    console.log(event)
    let {type,target} = event  
   
    //type=click  target = button
    let eventType = 'on' + type;//onclick
    syntheticEvent = getSyntheticEvent(event);
    //模拟事件冒泡
    while (target) {
        let {eventStore} = target;
        let listener = eventStore && eventStore[eventType]  //onclick   eventStore={onclick:()=>{alert('hello)}}
        if(listener){  //alert('hello)
        
                listener.call(target,syntheticEvent)
           
        }
        target = target.parentNode
    }
    for (let key in syntheticEvent) {  
        syntheticEvent[key] = null;  
    }
}

function getSyntheticEvent(nativeEvent){ //合成对象
    if(!syntheticEvent){ 
        syntheticEvent = {persist}
    }
    syntheticEvent.nativeEvent = nativeEvent;
    syntheticEvent.currentTarget = nativeEvent;
    //把原生事件对上的方法和属性都拷贝合成事件上
    for (let key in nativeEvent){
        if(typeof nativeEvent[key] == 'function'){ //为了this出问题
            syntheticEvent[key] = nativeEvent[key].bind(nativeEvent)
        }else{
            syntheticEvent[key] = nativeEvent[key]
        }
    }
    return syntheticEvent
}

// function getSyntheticEvent(){

// }
function persist() {
    syntheticEvent = {persist}
}
```



# 渲染类组件和函数组件

##  src\index.js

```js


import React from './react';
import ReactDOM from './react-dom';
class ClassComponent extends React.Component{
        render(){
            return ( React.createElement('div',{id:"counter4"},'hello'))
        }
}

function FunctionCounter(){
    return ( React.createElement('div',{id:"counter"},'hello'))
}
let ele1 = React.createElement('div',{id:"counter3"},'hello')
let ele2=React.createElement(ClassComponent)
let ele3=React.createElement(FunctionCounter)

ReactDOM.render(

    ele2
,document.getElementById("root"));


```

## react\constants.js

```js
export const TEXT = Symbol.for('TEXT');
export const ELEMENT = Symbol.for('ELEMENT');

+export const FUNCTION_COMPONENT = Symbol.for('FUNCTION_COMPONENT');
+export const CLASS_COMPONENT = Symbol.for('CLASS_COMPONENT');

```

## react\index.js

```js
import { TEXT, ELEMENT,CLASS_COMPONENT,FUNCTION_COMPONENT } from './constants';
import { ReactElement } from './vdom';
import {Component} from './component'

function createElement(type, config = {}, ...children) {   // 虚拟dpm
    delete config.__source;
    delete config.__self;
    let { key, ref, ...props } = config;   //  config 为标签上的属性
    let $$typeof = null;
    if (typeof type === 'string') { // span  div button
        $$typeof = ELEMENT;
    }
    //说明这个类型是个类组件
    +else if(typeof type === 'function' && type.prototype.isReactComponent){
    +    $$typeof = CLASS_COMPONENT;
    +}
     //说明这个类型是个函数组件
    +else if(typeof type === 'function'){
    +    $$typeof = FUNCTION_COMPONENT;
    +}
    props.children = children.map(item => 
        {
            if(typeof item === 'object' || typeof item === 'function' ){  //标签
                return item //React.createElement('span',{color:'red'},'hello')
            }else{
                return { $$typeof: TEXT, type: TEXT, content: item }  //item = 'hello
            }
        })
        
        return ReactElement($$typeof,type,key,ref,props)
}

const React = {
    createElement,
    Component
}
export {Component}
export default React;
```

##  react\vdom.js

```js
import { TEXT, ELEMENT,CLASS_COMPONENT,FUNCTION_COMPONENT } from './constants';
import {setProps,flatten} from './utils'
/**
 * 
 * @param {*} $$typeof  元素类型  是text  还是element
 * @param {*} type  标签 button  div  span
 * @param {*} key   
 * @param {*} ref  
 * @param {*} props  标签上的属性及child
 */
export function ReactElement($$typeof,type,key,ref,props){   //  虚拟dom
    let  element = {
        $$typeof,
        type,
        key,
        ref,
        props
    }
    return  element
}
export function createDom(element){
    let {$$typeof} = element;  //类型
    let dom = null;
    if(!$$typeof){ //不存在说明没有类型  string/number
        dom = document.createTextNode(element)
    } else if($$typeof == TEXT){
        dom = document.createTextNode(element.content)
    }
    else if($$typeof == ELEMENT){
        dom = createNativeDom(element)
    }

+   else if($$typeof == FUNCTION_COMPONENT){ //函数组件
+       dom = createFunctionComponentDom(element)
+   }
+   else if($$typeof == CLASS_COMPONENT){ //类组件
+      dom = createClassComponentDom(element)
+  }
    return  dom
}

 function createNativeDom(element){
    let {type,props} = element
    let dom = document.createElement(type);  //真实dom  span  button
    //创建子节点
    createDomChildren(dom,element.props.children)
    setProps(dom,props) //给真实DOM传属性及方法
    return  dom
}

//创建函数dom对象  真实dom
function createFunctionComponentDom(element){
    let {type,props} = element;   //type = Function
    let renderElement = type(props);
    element.renderElement = renderElement
    let newDOM = createDom(renderElement);
    renderElement.dom = newDOM //我们从虚拟domReact元素中创建出真实dom，创建出来后我们会把真实dom添加到虚拟dom的dom属性上
    return newDOM
    // element.renderElement.dom = 真实dom
}
function createClassComponentDom(element){
    let {type,props} = element;   //type = Class
    let componentInstance =  new type(props)  //创建实例
    //创建类组件实例后会在类组件的虚拟dom对象上添加一个属性componentInstance，指向类组件实例
    element.componentInstance = componentInstance //以后组件运行当中componentInstance是不变的
    let renderElement = componentInstance.render();
    //在类组件实例上添加renderElement，指向上一次要渲染的虚拟dom节点
    //后面组件更新，会重新render，然后要跟上一次的renderElement进行dom diff
    componentInstance.renderElement = renderElement
    let newDOM = createDom(renderElement);
    return newDOM
    //element. componentInstance.renderElement.dom = 真实dom
}
 function createDomChildren(parentNode,children){
    // children&&children.flat(Infinity).forEach(child => {  深克隆
        children&&flatten(children).forEach((child,index) => { //自己写的方法
            //child其实是虚拟dom 我们会在虚拟dom加一个属性_mountIndex挂在的索引，指向此虚拟节点在父节点的索引，为了domdiff
            child._mountIndex = index
            let childDOM = createDom(child)
            parentNode.appendChild(childDOM)
    });
 }


```
##  react\component.js
```js
class Component {
    constructor(props){
        this.props = props
    }
}


Component.prototype.isReactComponent = {}

export {
    Component
}
```
## react\utils.js

```js
import {addEvent} from './event'

//如果obj是数组只取第一个元素 否则就返回自己
export function onlyOne(obj){
    return Array.isArray(obj)?obj[0]:obj
}

//给真实DOM节点赋属性
export function setProps(dom,props){
    for(let key in props){
        if(key != 'children'){
            let value  = props[key];
            setProp(dom,key,value)
        }
    }
}
function setProp(dom,key,value){
    if(/^on/.test(key)){ //事件
        // console.log([key.toLowerCase()],value)
        // dom[key.toLowerCase()]=value //改成合成事件   主要原生事件在各个浏览器不一样 
        addEvent(dom,key,value)
    }
    else if(key == 'style'){ //行内样式
        if(value){
            for(let styleName in value){
                if(value.hasOwnProperty(styleName)){
                    dom.style[styleName] = value[styleName];
                }
            }
        }

    }else {  //标签属性
        dom.setAttribute(key, value);
    }
}

//展开多维数组
+export function flatten(array){
    let flattend = [];
    // console.log(array)
    (function (array){
        array.forEach(element => {
            if(Array.isArray(element)){
                flatten(element)
            }else{
                flattend.push(element)
            }
        });
    })(array);
    return flattend
}
```























