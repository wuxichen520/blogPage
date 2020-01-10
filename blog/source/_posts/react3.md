---
title: react 三 虚拟DOM
tags:
- react
categories:
- react 
---

## src\index.js
```js

import React from './react';
import ReactDOM from './react-dom';

// let element = <h1 id='hello'><span>hello</span><span>world</span></h1>

// React.createElement("h1", {
//     id: "hello"
//   },
//    React.createElement("span", null, "hello"),
//     React.createElement("span", null, "world"));


// function Welcome(props){
//     return (
//         <h1 id={props.id}><span>hello</span><span>world</span></h1>
//     )
// }

class Welcome extends React.Component{
    constructor(props){
        super(props)
        console.log(props)
    }
    render(){
        return  <h1 id={this.props.id}><span>hello</span><span>world</span></h1>
    }
}

// let element = React.createElement("h1", {
//         id: "hello"
//       },
//        React.createElement("span", {style:{color:"red",backgroundColor:"yellow"}}, "hello"),
//         React.createElement("span", {className:"world"}, "world"));
      

let element =  React.createElement(Welcome,{id:"111"})
       
ReactDOM.render(element, document.getElementById('root'));





```

## src\react/index.js
```js

// function ReactElement(type,props){
//     let element = { $$typeof: REACT_ELEMENT_TYPE, type, props }
//       return element
// }
const hasSymbol = typeof Symbol === 'function' && Symbol.for;


export const REACT_ELEMENT_TYPE = hasSymbol
    ? Symbol.for('react.element')
    : 0


class Component {
        constructor(props) {
            this.props = props;
        }
        static isReactComponent = true
}
function createElement(type,config,children){
    let props = {}
    for(let k in config){
        props[k] = config[k]
    }

    const childrenLength = arguments.length-2
    if(childrenLength == 1){ ////如果说只有一个儿子的话，那么props.children是一个对象,也就是一个ReactNode
        props.children = children

    }else{
        ////如果儿子的数量大于1 个话，就把所有的儿子放到一个数组里
        props.children = Array.prototype.slice.call(arguments,2)
       
    }

    //表示这是一个React元素类型
    return { $$typeof: REACT_ELEMENT_TYPE, type, props };
}
/**
 * REACT_ELEMENT_TYPE 表示这是一个React元素 
 * 不管你的type是不是原生的，都是 reactElemnt类型 
 * type React元素的类型  string number Function Class
 */

export default { createElement ,Component}

```

## src\react-dom/index.js
```js






/**
 * 
 * @param {*} node React节点 因为它可以React元素，也可以是数字 字符串
 * @param {*} parent  父容器，它是一个真实DOM元素 
 */

 function component(){

 }

function render(node,parent){
    if(typeof node === 'string'){
        return parent.appendChild(document.createTextNode(node))
    }
    let type,props;
    type = node.type; //h1 Function ClassComponent
    props = node.props
    if(type.isReactComponent){ //类组件
        let ele =  new type(props).render();
        type = ele.type;
        props = ele.props
    }else if(typeof type == 'function'){  //函数组件
      let newElement =   type(props)
      type = newElement.type;
      props = newElement.props
    }
    let domElement =  document.createElement(type)
    for(let proName in props){
   
        if(proName == 'children'){
            let children = props.children; //可能是一个对象，也可以是一个数组
            if(!Array.isArray(children)){
                children = [children];
            }
            children.forEach(child => render(child,domElement));
        }else if(proName == 'className'){
            domElement.className = props.className
        }else if(proName == 'style'){//值就是一个行内的样式对象
            let styleObject = props.style;  
            for(let attr in styleObject){
                domElement.style[attr] = styleObject[attr]
            }

            // let cssText = Object.keys(styleObject).map(attr=>{
            //     return `${attr.replace(/A-Z/g,()=>{return "-"+arguments[1].toLowerCase})}:${styleObject[attr]}`
            // }).join(";")
            // domElement.style.cssText=cssText
        }else{
            domElement.setAttribute(proName,props[proName])
        }
    }
    parent.appendChild(domElement)
}

export default {render}
```

