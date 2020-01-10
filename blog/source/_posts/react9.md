---
title: react 九 路由
tags:
- react
categories:
- react 
---

不同的路径渲染不同的组件
有两种实现方式
HashRouter:利用hash实现路由切换
BrowserRouter:实现h5 Api实现路由的切换

## HashRouter

利用hash实现路由切换
```html
    <a href="#/a">去a</a>
    <a href="#/b">去b</a>
    <script>
      window.addEventListener('hashchange',()=>{
          console.log(window.location.hash);
      });
    </script>
```

## history
```html
   <div id="root"></div>

    <script>
      //window.history 这是浏览器原生提供的对象，用来让我们通过它操作会话容器
      setTimeout(() => {
      //pushState方法是不会触发事件的
        window.history.pushState({ page: 1 }, 'page1', '/page1');
      }, 1000);
      setTimeout(() => {
      //pushState方法是不会触发事件的
        window.history.pushState({ page: 2 }, 'page2', '/page2');
      }, 1000);
      setTimeout(() => {
      //pushState方法是不会触发事件的
        window.history.pushState({ page: 3 }, 'page3', '/page3');
      }, 1000);
      setTimeout(() => {
        //向前回退
        window.history.go(-1)
      }, 1000);
    

       window.onpopstate = function (event) {
      //type=popstate  当前history的状态  浏览器回退触发的事件
          console.log({ type: event.type, state: event.state, pathname: window.location.pathname });
          root.innerHTML = window.location.pathname;
      }
    </script>
```
重写window.onpushstate
hash值改变触发的是popstate
```html
 <div id="root"></div>
    <script>
      (function(history){
        let pushState = history.pushState
        history.pushState = function(state,title,url){
          pushState.call(history,state,title,url)
            if(typeof window.onpushstate == 'function'){
              window.onpushstate(state,title,url)
            }
        }
      })(window.history)


        //window.history 这是浏览器原生提供的对象，用来让我们通过它操作会话容器
        setTimeout(() => {
        //pushState方法是不会触发事件的
          window.history.pushState({ page: 1 }, 'page1', '/page1');
        }, 1000);
        setTimeout(() => {
        //pushState方法是不会触发事件的
          window.history.pushState({ page: 2 }, 'page2', '/page2');
        }, 2000);
        setTimeout(() => {
        //pushState方法是不会触发事件的
          window.history.pushState({ page: 3 }, 'page3', '/page3');
        }, 3000);
        setTimeout(() => {
          //向前回退
          window.history.go(-1)
        }, 4000);
      

        window.onpopstate = function (event) {
        //type=popstate  当前history的状态  浏览器回退触发的事件
          console.log({ type: event.type, state: event.state, pathname: window.location.pathname });
          root.innerHTML = window.location.pathname;
      }
      window.onpushstate = function (event) {
        //type=popstate  当前history的状态  浏览器回退触发的事件
          console.log({ type: event.type, state: event.state, pathname: window.location.pathname });
          root.innerHTML = window.location.pathname;
      }
    </script>
```
## 路由

### 安装

```bash
    npm i  react-router-dom @types/react-router-dom path-to-regexp -S
```
```js

```

### 操作
 index.js
```js  

import React from 'react';
import ReactDOM from 'react-dom';

import {HashRouter as Router,Route} from 'react-router-dom';

import Home from './components/Home';
import User from './components/User'
import Profile from './components/Profile'
ReactDOM.render(
    <Router>
        <Route path="/" component={Home}></Route>
        <Route path="/user" component={User}></Route>
        <Route path="/profile" component={Profile}></Route>
    </Router>
,document.getElementById("root"))
```

 Home.js...
```js
import React from 'react';
export default function(props){
    return (
        <div>Home</div>
    )
}
```

### createBrowserHistory
```js
export default function createBrowserHistory(){
    const globalHistory = window.history;
    let listeners = [];
    let initialLocation = {
        pathname: window.location.pathname,
        state: globalHistory.state,//历史对象上的状态
    };
    function createHref (history){
        return location.protocol + location.host + location.pathname + location.search + location.hash;
    }
    function setState(state){
        Object.assign(history,state);
        history.length= globalHistory.length;
        listeners.forEach(listener=>listener())
    }
    function listen(listener){
        listeners.push(listener)
    }
    function push(path, state){
        const action = 'PUSH';
        const location = {path,state}

        globalHistory.pushState(state,null,path);
        setState({action,location})
    }
    function go(n){
        globalHistory.go(n)
    }
    function goBack(n){
        go(-1)
    }
    function goForward(n){
        go(1)
    }
    function replace(path, state){
        const action = 'REPLACE';
        const location = {path,state}
        globalHistory.replaceState(state,null,path);
        setState({action,location})
    }
    let isBlock;
    function block(promat){
        isBlock = promat
    }
    let history = {
        length: globalHistory.length,
        action: 'POP',//当前路径是怎么来的?
        location: initialLocation,
        createHref,//通过location对象得到一个路径字符串
        push,//跳转到新的路径里去，往历史里添加一个新条目
        replace,//跳转到新的路径里去，不会添加新的条目，而是替换当前的条目
        go,
        goBack,
        goForward,
        block,
        listen
    }
    return history;
}
```

### react-router-dom

index.js
```js
import HashRouter from './HashRouter';
import Route from './Route';
export {
    HashRouter,
    Route

}
```
HashRouter.js
```js

import React  from 'react';
import  RouterContext from './RouterContext';

/**
 * HashRouter只是一个容器，并没有DOM结构，它渲染的就是它的子组件
 * 它就是为了向下层组件传递 location
 */
export default class HashRouter extends React.Component{
    state = {
        location:{
            pathname:window.location.hash.slice(1) || "/", // #/user ==> /user
            state:window.history.state
        }
    }
    componentDidMount(){
        window.addEventListener('hashchange',event=>{
            this.setState({
                ...this.state,
                location:{
                    ...this.state.location,
                    pathname:window.location.hash.slice(1) || "/"
                }
              
            })
            window.location.hash = window.location.hash || "/"
        })
    }

    render(){
        let routerValue = {
            location:this.state.location
        }
        return (
            <RouterContext.Provider value = {routerValue}>
                {this.props.children}
            </RouterContext.Provider>
        )
    }
}
```
Route.js
```js

import React from 'react';
import  RouterContext from './RouterContext';
/**
 * Route代表一条路由规则
 * path代表此规则的路径 
 * component代表要渲染的组件
 * 如果说当前路径 #/user   hashRouter state location pathname =>context传下来了
 */
export default class Route extends React.Component{
    static contextType = RouterContext  //this.context.location.pathname

    render() {
        let {path ,component:RouteComponent,exact} = this.props;
        let pathname = this.context.location.pathname;
        
        if((exact && pathname === path) || (!exact && pathname.startsWith(path))){
            return <RouteComponent />
        }else{
            return null
        }
       
    }
}
```

RouterContext.js

```js
import {createContext} from 'react';


let context = createContext()

export default context;
```

### path-to-regexp

#### /home 结束

```js
et pathToRegExp = require('path-to-regexp');
let regxp = pathToRegExp('/home',[],{end:true});
console.log(regxp);//   /^\/home\/?$/i
console.log(regxp.test('/home'));
console.log(regxp.test('/home/2'));
```

####  /home非结束

```js
let pathToRegExp = require('path-to-regexp');
let regx2 = pathToRegExp('/home',[],{end:false});
console.log(regx2);//   /^\/home\/?(?=\/|$)/i
console.log(regx2.test('/home'));
console.log(regx2.test('/home/'));
console.log(regx2.test('/home//'));
console.log(regx2.test('/home/2'));
```

#### 路径参数
```js
let params = [];
let regx3 = pathToRegExp('/user/:id',params,{end:true});
console.log(regx3,params);
/**
/^\/user\/(?:([^\/]+?))\/?$/i
[ { name: 'id', optional: false, offset: 7 } ]
**/
```

|表达式|	含义|
|:-|-:|
|(?=pattern)	|正向肯定查找(前瞻),后面必须跟着什么|
|(?!pattern)	|正向否定查找(前瞻)，后面不能跟着什么|
|(?<=pattern)	|反向肯定条件查找(后顾),不捕获|
|(?<!pattern)	|反向否定条件查找（后顾）|

```js
console.log('1a'.match(/\d(?=[a-z])/));
console.log('1@'.match(/\d(?![a-z])/));
console.log('a1'.match(/(?<=[a-z])\d/));
console.log('$1'.match(/(?<![a-z])\d/));
```

#### 正则路由
Route.js
```js
import React from 'react';
import  RouterContext from './RouterContext';
import {pathToRegexp} from 'path-to-regexp'
/**
 * Route代表一条路由规则
 * path代表此规则的路径 
 * component代表要渲染的组件
 * 如果说当前路径 #/user   hashRouter state location pathname =>context传下来了
 */
export default class Route extends React.Component{
    static contextType = RouterContext  //this.context.location.pathname

    render() {
        let {path ,component:RouteComponent,exact} = this.props;
        let pathname = this.context.location.pathname;
        let paramNames = [];

        let rex = pathToRegexp(path,paramNames,{end:exact});
        // if((exact && pathname === path) || (!exact && pathname.startsWith(path))){
        if(rex.test(pathname)){
            return <RouteComponent />
        }else{
            return null
        }
       
    }
}
```

#### Link

```js
import React  from 'react';
import RouterContext from './RouterContext'


export default function Link(props){
    return (
        <RouterContext.Consumer>
            {
             
                routerValue=>(
                 
                    <a onClick={()=>routerValue.history.push(props.to)}>{props.children}</a>
                )
            }
        </RouterContext.Consumer>
    )
}

// export default function Link(props){
//     return <a href={`#${props.to}`}>{props.children}</a>
// }
```

#### Switch

* 负责进行子组件的匹配，只会渲染第一个匹配上的子组件
* useContext 是获取上下文对象的第三种方式
* static contextType (类中的)  Consumer(函数中) 还可以ReactHooks useContext 可以上下文对象

Switch.js
```js
import React, { useContext }  from 'react';
import RouterContext from './RouterContext'
import {pathToRegexp} from 'path-to-regexp'

export default function (props){

    let routerContext = useContext(RouterContext);
    let children = props.children;
    children = Array.isArray(children)?children:[children];
    let pathname = routerContext.location.pathname//当前路径
    for(let i =0;i<children.length;i++){
        let child = children[i];
        /**
         * React.createElement(Route,{exact,path,component})
             {type:Route,props:{exact,path,component}}
         */
        let {path="/",component,exact} = child.props;
        let regexp =  pathToRegexp(path,[],{end: !!exact});
        let matched = pathname.match(regexp);
        if(matched){
            return child
        }
    }
    return (
       null
    )
}

```
#### Redirect

Redirect.js
```js
import React, { useContext }  from 'react';
import RouterContext from './RouterContext'

export default function (props){

    let routerContext = useContext(RouterContext);
    if(!props.from || props.from === routerContext.location.pathname)
    routerContext.history.push(props.to)
    
    return (
       null
    )
}

```

### 二级路由

