---
title: react 面试   React.Children.map 原理
tags:
- react
- 面试
categories:
- react 
---

# React.Children.map
## 实现单个映射
### src/index.js
```js


import React from 'react';
import ReactDOM from 'react-dom';
import {map} from './react/reactChildren'

class Chid extends React.Component{
    render(){
        const mappenchild = map(
            this.props.children,
            function(item,index){
                {console.log(item)}
            return  [<div key={`${index}`}>{this.name}:{item}</div>]
             },
             {name:"我是个上下文"}
            
        )

        console.log(mappenchild)
        return(<div>
            {mappenchild}
        </div>)
    }
   
}

class App extends React.Component{
    render(){
        return <Chid><span>A</span></Chid>
    }
}

ReactDOM.render(
<App/>
,document.getElementById("root"));
```

### src\react\reactChildren.js
```js

//result:映射出来的所有节点

import { REACT_ELEMENT_TYPE } from "../shared/ReactSymbols";

// let arr = [1,[2,[3,[4]]]];
// console.log(arr.flat())
// console.log(arr1.toString().split(",").map(item=>parseInt(item)))

function mapChildren(children,mapFn,context){
    const result = [];
    mapIntoWithKeyPrefixInternal(children,result,mapFn,context);
    return result
}

function mapIntoWithKeyPrefixInternal(children,result,mapFn,context){
    //遍历的上下文
    const traverseContext = {  
        result,
        mapFn
    }
    traverseAllChildren(children,mapStringChildIntoContext,traverseContext,context)
}
function traverseAllChildren(children,mapStringChildIntoContext,traverseContext,context){
    let type = typeof children;
    //说明是个可渲染节点
    if(type == "string" || type == "number" || (type == "object" && children.$$typeof== REACT_ELEMENT_TYPE)){
        mapStringChildIntoContext(traverseContext,children,context)
    }
}

//map映射 child肯定是一个节点
function mapStringChildIntoContext(traverseContext,child,context){
    let {result,mapFn} = traverseContext;

    //mappenChild <div key={`${index}`}>{item}</div>
   let mappenChild =  mapFn.call(context,child); //child = <span>A</span>
   result.push(mappenChild)

}
export{
    mapChildren as map
}
```
### src/shared/ReactSymbols.js
```js
const hasSymbol = typeof Symbol === 'function' && Symbol.for;
export const REACT_ELEMENT_TYPE = hasSymbol
    ? Symbol.for('react.element')
    : 0xeac7;
export const REACT_FORWARD_REF_TYPE = hasSymbol
    ? Symbol.for('react.forward_ref')
    : 0xead0;
```

## 实现对数组的映射
### src\index.js
```js


import React from 'react';
import ReactDOM from 'react-dom';
import {map} from './react/reactChildren'

class Chid extends React.Component{
    render(){
        const mappenchild = map(
            this.props.children,
            function(item,index){
                {console.log(item)}
                return  [<div key={`${index}`}>{this.name}:{item}</div>]
             },
             {name:"我是个上下文"}
            
        )

        console.log(mappenchild)
        return(<div>
            {mappenchild}
        </div>)
    }
   
}

class App extends React.Component{
    render(){
+      return <Chid><span>A</span><span>B</span><span>c</span></Chid>
    }
}

ReactDOM.render(
<App/>
,document.getElementById("root"));


```

### src\react\ReactChildren.js
```js

//result:映射出来的所有节点

import { REACT_ELEMENT_TYPE } from "../shared/ReactSymbols";

// let arr = [1,[2,[3,[4]]]];

// // console.log(arr.toString().split(",").map(item=>parseInt(item)))
// function flat(arr){
//     return arr.toString().split(",").map(item=>parseInt(item))
// }
// console.log(arr.flat())

// function mapChildren(children,mapFn,context){
//     return children.flat(Infinity).map(mapFn).flat(Infinity)
// }


function mapChildren(children,mapFn,context){
    const result = [];

    mapIntoWithKeyPrefixInternal(children,result,mapFn,context);
    return result
}



function mapIntoWithKeyPrefixInternal(children,result,mapFn,context){
    //遍历的上下文
    const traverseContext = {  
        result,
        mapFn
    }
    traverseAllChildren(children,mapStringChildIntoContext,traverseContext,context)
}
function traverseAllChildren(children,mapStringChildIntoContext,traverseContext,context){
    let type = typeof children;
    //说明是个可渲染节点
    if(type == "string" || type == "number" || (type == "object" && children.$$typeof== REACT_ELEMENT_TYPE)){
        mapStringChildIntoContext(traverseContext,children,context)
    }
+    if(Array.isArray(children)){
+        for(let i =0; i<children.length;i++){
+           traverseAllChildren(children[i],mapStringChildIntoContext,traverseContext,context)
+       }
+    }
}

//map映射 child肯定是一个节点
function mapStringChildIntoContext(traverseContext,child,context){
    let {result,mapFn} = traverseContext;

    //mappenChild <div key={`${index}`}>{item}</div>
   let mappenChild =  mapFn.call(context,child); //child = <span>A</span>


   //mappenChild = [item,[item,[item,[item]]]]
   result.push(mappenChild)
  

}
export{
    mapChildren as map
}
```
## 实现映射为数组
### src\index.js
```js


import React from 'react';
import ReactDOM from 'react-dom';
import {map} from './react/reactChildren'

class Chid extends React.Component{
    render(){
        const mappenchild = map(
            this.props.children,
            function(item,index){
                {console.log(item)}
 +               return  [item,[item,[item,[item,[item]]]]]
             },
             {name:"我是个上下文"}
            
        )

        console.log(mappenchild)
        return(<div>
            {mappenchild}
        </div>)
    }
   
}

class App extends React.Component{
    render(){
        return <Chid><span>A</span><span>B</span><span>c</span></Chid>
    }
}

ReactDOM.render(
<App/>
,document.getElementById("root"));
```
### src\react\ReactChildren.js
```js

//result:映射出来的所有节点

import { REACT_ELEMENT_TYPE } from "../shared/ReactSymbols";

// let arr = [1,[2,[3,[4]]]];

// // console.log(arr.toString().split(",").map(item=>parseInt(item)))
// function flat(arr){
//     return arr.toString().split(",").map(item=>parseInt(item))
// }
// console.log(arr.flat())

// function mapChildren(children,mapFn,context){
//     return children.flat(Infinity).map(mapFn).flat(Infinity)
// }
function mapChildren(children,mapFn,context){
    const result = [];

    mapIntoWithKeyPrefixInternal(children,result,mapFn,context);
    return result
}
function mapIntoWithKeyPrefixInternal(children,result,mapFn,context){
    //遍历的上下文
    const traverseContext = {  
        result,
        mapFn
    }
    traverseAllChildren(children,mapStringChildIntoContext,traverseContext,context)
}
function traverseAllChildren(children,mapStringChildIntoContext,traverseContext,context){
    let type = typeof children;
    //说明是个可渲染节点
    if(type == "string" || type == "number" || (type == "object" && children.$$typeof== REACT_ELEMENT_TYPE)){
        mapStringChildIntoContext(traverseContext,children,context)
    }
    if(Array.isArray(children)){
        for(let i =0; i<children.length;i++){
            traverseAllChildren(children[i],mapStringChildIntoContext,traverseContext,context)
        }
    }
}

//map映射 child肯定是一个节点
function mapStringChildIntoContext(traverseContext,child,context){
    let {result,mapFn} = traverseContext;
    //mappenChild <div key={`${index}`}>{item}</div>
   let mappenChild =  mapFn.call(context,child); //child = <span>A</span>
   //mappenChild = [item,[item,[item,[item]]]]
 +  if(Array.isArray(mappenChild)){
 +       mapIntoWithKeyPrefixInternal(mappenChild,result,c=>c,context)
 +  }else{
 +       result.push(mappenChild)
 +  }
  

}
export{
    mapChildren as map
}
```
## 映射为数组

### src\index.js
```js



import React from 'react';
import ReactDOM from 'react-dom';
import {map} from './react/reactChildren'

class Chid extends React.Component{
    render(){
        const mappenchild = map(
            this.props.children,
            function(item,index){
                {console.log(item)}
                //return  [<div key={`${index}`}>{this.name}:{item}</div>]
                 return  [item,[item,[item,[item,[item]]]]]
             },
             {name:"我是个上下文"}
            
        )

        console.log(mappenchild)
        return(<div>
            {mappenchild}
        </div>)
    }
   
}

class App extends React.Component{
    render(){
        //return <Chid><span>A</span><span>B</span><span>c</span></Chid>
+    return <Chid>{[<span key="keyA">A</span>,<span>B</span>]}{[<span>C</span>,<span>D</span>]}</Chid>
    }
}

ReactDOM.render(
<App/>
,document.getElementById("root"));


//数组映射的key
// 0: {$$typeof: Symbol(react.element), type: "span", key: ".0:$keyA/.$keyA", ref: null, props: {…}, …}
// 1: {$$typeof: Symbol(react.element), type: "span", key: ".0:$keyA/.1:$keyA", ref: null, props: {…}, …}
// 2: {$$typeof: Symbol(react.element), type: "span", key: ".0:$keyA/.1:1:$keyA", ref: null, props: {…}, …}
// 3: {$$typeof: Symbol(react.element), type: "span", key: ".0:$keyA/.1:1:1:$keyA", ref: null, props: {…}, …}
// 4: {$$typeof: Symbol(react.element), type: "span", key: ".0:$keyA/.1:1:1:1:$keyA", ref: null, props: {…}, …}
// 5: {$$typeof: Symbol(react.element), type: "span", key: ".0:1/.0", ref: null, props: {…}, …}
// 6: {$$typeof: Symbol(react.element), type: "span", key: ".0:1/.1:0", ref: null, props: {…}, …}
// 7: {$$typeof: Symbol(react.element), type: "span", key: ".0:1/.1:1:0", ref: null, props: {…}, …}
// 8: {$$typeof: Symbol(react.element), type: "span", key: ".0:1/.1:1:1:0", ref: null, props: {…}, …}
// 9: {$$typeof: Symbol(react.element), type: "span", key: ".0:1/.1:1:1:1:0", ref: null, props: {…}, …}
// 10: {$$typeof: Symbol(react.element), type: "span", key: ".1:0/.0", ref: null, props: {…}, …}
// 11: {$$typeof: Symbol(react.element), type: "span", key: ".1:0/.1:0", ref: null, props: {…}, …}
// 12: {$$typeof: Symbol(react.element), type: "span", key: ".1:0/.1:1:0", ref: null, props: {…}, …}
// 13: {$$typeof: Symbol(react.element), type: "span", key: ".1:0/.1:1:1:0", ref: null, props: {…}, …}
// 14: {$$typeof: Symbol(react.element), type: "span", key: ".1:0/.1:1:1:1:0", ref: null, props: {…}, …}
// 15: {$$typeof: Symbol(react.element), type: "span", key: ".1:1/.0", ref: null, props: {…}, …}
// 16: {$$typeof: Symbol(react.element), type: "span", key: ".1:1/.1:0", ref: null, props: {…}, …}
// 17: {$$typeof: Symbol(react.element), type: "span", key: ".1:1/.1:1:0", ref: null, props: {…}, …}
// 18: {$$typeof: Symbol(react.element), type: "span", key: ".1:1/.1:1:1:0", ref: null, props: {…}, …}
// 19: {$$typeof: Symbol(react.element), type: "span", key: ".1:1/.1:1:1:1:0", ref: null, props: {…}, …}

```

###  src\react\ReactChildren.js
```js

//result:映射出来的所有节点

import { REACT_ELEMENT_TYPE } from "../shared/ReactSymbols";

// let arr = [1,[2,[3,[4]]]];

// // console.log(arr.toString().split(",").map(item=>parseInt(item)))
// function flat(arr){
//     return arr.toString().split(",").map(item=>parseInt(item))
// }
// console.log(arr.flat())

// function mapChildren(children,mapFn,context){
//     return children.flat(Infinity).map(mapFn).flat(Infinity)
// }

+const SEPARATOR = '.'; //分隔符  开头的分隔符
+const SUBSEPARATOR = ':'; //子分隔符  中间件分隔符
function mapChildren(children,mapFn,context){
    const result = [];
    debugger;
    mapIntoWithKeyPrefixInternal(children,result,null,mapFn,context);
    return result
}


//prefix指的是渲染前的节点   key: ".0:0/.0",  /前面的部分
function mapIntoWithKeyPrefixInternal(children,result,prefix,mapFn,context){
+    if(prefix !== null){
+       prefix = prefix + "/" ;//  .0:0=>.0:0/
+    }
    //遍历的上下文
    const traverseContext = {  
        result,
        mapFn,
+       prefix
    }
    traverseAllChildren(children,'',mapStringChildIntoContext,traverseContext,context)
}
function traverseAllChildren(children,nameSoFar,mapStringChildIntoContext,traverseContext,context){
    let type = typeof children;
    //说明是个可渲染节点
    if(type == "string" || type == "number" || (type == "object" && children.$$typeof== REACT_ELEMENT_TYPE)){
        mapStringChildIntoContext(traverseContext,children,context,nameSoFar === ""?SEPARATOR+getComponentKet(children,0):nameSoFar)
    }
    if(Array.isArray(children)){
        //如果传过来的nameSoFar是空前缀就是.否则就是: 
        //第二次进来的时候nameSoFar =.0 nextNamePrefix=:
        let nextNamePrefix = nameSoFar === ""?SEPARATOR:nameSoFar+SUBSEPARATOR
        for(let i =0; i<children.length;i++){
+            let child = children[i]; //[<span>A</span>,<span>B</span>] .0
+            let nextName = nextNamePrefix+getComponentKet(child,i) //.0   .0:0
            traverseAllChildren(children[i],nextName,mapStringChildIntoContext,traverseContext,context)
        }
    }
}
function getComponentKet(component,index){
    return component.key || index.toString(36) //如果此节点有自己的key就用自己的key  如果没有就用索引
}
//map映射 child肯定是一个节点
//childKey=.0:0
function mapStringChildIntoContext(traverseContext,child,context,childKey){
    let {result,mapFn,prefix} = traverseContext;

    //mappenChild <div key={`${index}`}>{item}</div>
    console.log(traverseContext)
   let mappenChild =  mapFn.call(context,child); //child = <span>A</span>


   //mappenChild = [item,[item,[item,[item]]]]
   if(Array.isArray(mappenChild)){
        mapIntoWithKeyPrefixInternal(mappenChild,result,childKey,c=>c,context)
   }else{

       // result.push(mappenChild) //.0:0/

+        result.push({...mappenChild,key:prefix+childKey}) //.0:0/.0
   }
  

}
export{
    mapChildren as map
}

```

