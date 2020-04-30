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
#### components

##### Home.js
```js
import React from 'react';
export default function(props){
    return (
        <div>Home</div>
    )
}
```


##### UserList.js
```js
import React, { useState,useEffect } from 'react';
import { Link } from '../react-router-dom';
// export default function(props){
//     let [users,setUser] = useState([]);

//     return (
//         <div>UserList</div>
//     )
// }

export default class UserList extends React.Component{

            state = {
                users:[]
                }
    
    componentDidMount(){
        let usesStr = localStorage.getItem("users");
        let users = JSON.parse(usesStr);
     
        if(users){
            this.setState({users});
        }
       
    }
    render(){
        console.log(this.state.users)
        return(
            <ul>
                {this.state.users.map((user)=>
                    <li key={user.id}>
                        {/* <Link to={`/user/details/${user.id}`}>{user.username}</Link> */}
                        <Link to={{pathname:`/user/details/${user.id}`,state:user}}>{user.username}</Link>
                        {/* {user.username} */}
                    </li>
                )}
            </ul>
        )
    }
}
```
##### User.js
```js
import React from 'react';
import ReactDOM from 'react-dom';
import { Link, Route, Switch, Redirect,Router } from '../react-router-dom';
import UserAdd from './UserAdd';
import UserList from './UserList';
import UseDetails from './UseDetails';
export default function(props){
    return (
       
        <div className="row">
            <div className="col-md-2" style={{float:"left"}}>
                <ul className="nav nav-stack">
                    <li><Link to="/user/list">用户列表</Link></li>
                    <li><Link to="/user/add">新增用户</Link></li>
                </ul>
            </div>
            <div className="" style={{float:"left"}}>
             
                    <Route path="/user/list" component={UserList} />
                    <Route path="/user/add" component={UserAdd} />
                    <Route path="/user/details/:id" component={UseDetails} />
            </div>
        </div>
       
    )
}
```
##### UserAdd.js
```js
import React,{useRef} from 'react';
/**
 *只要一个组件是通过路由route渲染出来的那么久可以获取一些属性
 location
 history
 match
 * @param {*} props 
 */
export default function(props){
    let usernameRef = useRef();

    function handleSubmit(event){
        event.preventDefault();
        let username = usernameRef.current.value;
        let userStr = localStorage.getItem("users");
        let users = userStr? JSON.parse(userStr) :[];
        users.push({id:Date.now()+"",username});
        localStorage.setItem("users",JSON.stringify(users))
        props.history.push("/user/list")
    }
    return (
        <form onSubmit={handleSubmit}>
            用户名: <input type="text" className="form-control" ref={usernameRef}/>
            <button type="submit">提交</button>
        </form>
    )
}
```
##### UseDetails.js
```js
import React from 'react';
import {local} from "../local"
export default class UseDetails extends React.Component{
    state = {user:{}}
    componentDidMount(){
        console.log(this.props)
        let user = this.props.location.state;
        if(!user){
            let id = this.props.match.params.id
            user = local.getId(id);
        }
        this.setState({user});
       
    }
    render(){
        const {user} = this.state
        return(
            <div>
                <p>id:{user.id}</p>
                <p>用户名:{user.username}</p>
            </div>
        )
    }
}
// export default function(props){
   
//     componentDidMount
//     return (
//         <div>UseDetails</div>
//     )
// }
```
##### Protected.js
为受保护的组件做判断
```js
import React from "react";
import { Route, Redirect } from "../react-router-dom";
export default function ({component:Component,...rest}){
    return <Route {...rest} render={
        props=>{ 
            console.log(props,Component,rest)
            return localStorage.getItem("login")?<Component {...props}/>:<Redirect  to={{pathname:"/login",state:{from:props.location.pathname}}} />
    }}/>
}
```
##### Profile.js
受保护的组件  进入之前没有登录  跳登录  登陆之后方可进
```js
import React from 'react';
export default function(props){
    return (
        <div>Profile</div>
    )
}
```
##### Login.js
登录
```js
import React from "react";
export default class Login extends React.Component{
   
    handleLogin = () =>{
     
        localStorage.setItem("login","true");
        if(this.props.location.state){
         
            this.props.history.push(this.props.location.state.from)
        }else{
        
            this.props.history.push("/")
        }
    }
    render(){
    
        return(<button onClick={this.handleLogin}>登录</button>)
    }
}
```
##### MenuLink.js
导航高亮效果
```js
import React from "react";
import { Route,Link} from "../react-router-dom";
export default function({to,exact,children}){
    return <Route path={to} exact={exact} children={
    props=>{
      
        return <Link to={to} className={props.match?"on":""}>{children}</Link>} 
    }/>
}
```
##### NavHeader.js
不是路由组件  但是要继承路由参数
```js
import React from 'react';
import {WithRouter} from "../react-router-dom"

class NavHeader extends React.Component{
   
    render(){
        console.log(this.props,WithRouter)
        return  (<div>NavHeader</div>)
    }
}

export default WithRouter(NavHeader)
// export default NavHeader
```
#### react-router-dom

##### index.js
```js
import HashRouter from './HashRouter';
import Route from './Route';
import Link from './Link';
import Switch from './Switch';
import Redirect from './Redirect';
import WithRouter from './WithRouter';
import Prompt from './Prompt';
import BrowserRouter from './BrowserRouter';
export {
    HashRouter,
    BrowserRouter,
    Route,
    Link,
    Switch,
    Redirect,
    WithRouter,
    Prompt
}
```
##### BrowserRouter.js
```js

import React  from 'react';
import  RouterContext from './RouterContext';

let pushState = window.history.pushState;
window.history.pushState = function(state,title,url){
    pushState.call(window.history,state,title,url);
    window.onpushstate.call(this,state,url)
}
export default class BrowserRouter extends React.Component{
    state = {
        location:{
            pathname:window.location.pathname  || "/", // #/user ==> /user
            state:window.history.state
        }
    }
    componentDidMount(){
       
        window.onpopstate =(event)=>{
            this.setState({
                ...this.state,
                location:{
                    ...this.state.location,
                    state:event.state,
                    pathname:window.location.pathname || "/"
                }
              
            })
        }

        window.onpushstate = (state,pathname) =>{
            this.setState({
                ...this.state,
                location:{
                    ...this.state.location,
                    state,
                    pathname
                }
              
            })
        }
        // window.addEventListener('hashchange',event=>{
        //     this.setState({
        //         ...this.state,
        //         location:{
        //             ...this.state.location,
        //             pathname:window.location.hash.slice(1) || "/"
        //         }
              
        //     })
        //     window.location.hash = window.location.hash || "/"
        // })
    }

    render(){
        let that = this
        let history = {
            location:this.state.location,
            push(to){
                if(that.message){
                   let confirm =  prompt(that.block(typeof to == "object"?to:{pathname:to}));
                   if(!confirm){
                        return
                   }
                   
                }
                if(typeof to == "object"){
                    let {pathname,state} = to;
                    // window.location.hash = pathname
                    window.history.pushState(state,'',pathname)
                   // that.state.location.state = state;
                }else{
                    // window.location.hash = to
                    //that.state.location.state =null;
                    window.history.pushState(null,'',to)
                }
            },
            block(message){
                that.message = message
            }
        }
        let routerValue = {
          
            location:this.state.location,
            history,
        }
        return (
            <RouterContext.Provider value = {routerValue}>
                {this.props.children}
            </RouterContext.Provider>
        )
    }
}
```
##### HashRouter.js
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
        let that = this
        let history = {
            location:this.state.location,
            push(to){
                if(that.message){
                   let confirm =  prompt(that.block(typeof to == "object"?to:{pathname:to}));
                   if(!confirm){
                        return
                   }
                   
                }
                if(typeof to == "object"){
                    let {pathname,state} = to;
                    window.location.hash = pathname
                    that.state.location.state = state;
                }else{
                    that.state.location.state =null
                    window.location.hash = to;
                }
            },
            block(message){
                that.message = message
            }
        }
        let routerValue = {
          
            location:this.state.location,
            history,
        }
        return (
            <RouterContext.Provider value = {routerValue}>
                {this.props.children}
            </RouterContext.Provider>
        )
    }
}
```
##### Link.js
```js
import React  from 'react';
import RouterContext from './RouterContext'


// export default function Link(props){
//     return (
//         <RouterContext.Consumer>
//             {
             
//                 routerValue=>(
//                    //console.log(props.children),
//                    <a {...props} onClick={()=>routerValue.history.push(props.to)}>{props.children}</a>
//                 )
//             }
//         </RouterContext.Consumer>
//     )
// }


export default class Link extends React.Component{
    static contextType = RouterContext
    render(){
        return (
            <a {...this.props} onClick={()=>this.context.history.push(this.props.to)} >{this.props.children}</a>
        )
    }
}
// export default function Link(props){
//     return <a href={`#${props.to}`}>{props.children}</a>
// }
```
##### Prompt.js
弹窗  路由跳转可截止
```js
import React from 'react';
import RouterContext from "./RouterContext" ;
export default class Prompt extends React.Component{
    static contextType = RouterContext
    componentWillUnmount(){
        this.context.history.block(null);
    }
    render(){
        let history = this.context.history
        const {when ,message} = this.props;
        if(when){
            history.block(message)
        }else{
            history.block(null)
        }
        return (
            <div>Prompt</div>
        )
    }
}

```
##### Redirect.js
```js
import React, { useContext }  from 'react';
import RouterContext from './RouterContext'
// export default function (props){
//     let routerContext = useContext(RouterContext);
//         if(!props.from || props.from === routerContext.location.pathname){
//             routerContext.history.push(props.to)
//         }
//     return (
//        null
//     )
// }
export default class Redirect extends React.Component{
    static contextType = RouterContext
    render(){
        if(!this.props.from || this.props.from ===  this.context.location.pathname){
            this.context.history.push(this.props.to);

        }
     
        return null;
    }
}
```
##### Route.js
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
        let {path="/" ,component:RouteComponent,exact,render,children} = this.props;
        let pathname = this.context.location.pathname;
        let paramNames = [];
       
        let rex = pathToRegexp(path,paramNames,{end:!!exact});
        let result = pathname.match(rex);
        let props = {
            history:this.context.history,
            location:this.context.location
        }
        console.log(result)
        // if((exact && pathname === path) || (!exact && pathname.startsWith(path))){
        if(rex.test(pathname)){
        
            paramNames = paramNames.map(item=>item.name);
           //console.log(result)
            // return
            let [url,...values] = result;
            //console.log(url,values)
            let params = {};
            for(let i = 0; i<paramNames.length;i++){
                params[paramNames[i]] = values[i]
            }
            props.match = {
                path,
                url,
                isExact:url === pathname,
                params
            };
            if(RouteComponent){
                return <RouteComponent {...props}/>
            }else if(render){
                //console.log(props,"route")
                return render(props);
            }else if(children){
                return children(props);
            }
            else{
                return null
            }
           
        }else{
            if(children){
                return children(props);
            }
            else{
                return null
            }
        }
       
    }
}
```
##### RouterContext.js
```js
import {createContext} from 'react';


let context = createContext()

export default context;
```
##### Switch.js
```js
import React, { useContext }  from 'react';
import RouterContext from './RouterContext'
import {pathToRegexp} from 'path-to-regexp'
/**
 * 负责进行子组件的匹配，只会渲染第一个匹配上的子组件
 * useContext 是获取上下文对象的第三种方式
 * static contextType (类中的)  Consumer(函数中) 还可以ReactHooks useContext 可以上下文对象
 * @param {*} props 
 */
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
        let params = [];
        let {path="/",component,exact} = child.props;
        let regexp =  pathToRegexp(path,params,{end: !!exact});
        let matched = pathname.match(regexp);
        if(matched){
            //console.log(child)
            return child  //虚拟dom
        }
    }
    return (
       null
    )
}

```
##### WithRouter.js
将不是路由组件包裹一下
```js
import React from 'react'
import  Route  from "./Route";


export default function(WrappedComponent){
    //console.log(WrappedComponent)
    return props => <Route  component={WrappedComponent}></Route>
}
```

### index.js
```js  



import React from 'react';
import ReactDOM from 'react-dom';

import {BrowserRouter as Router,Route,Link,Switch,Redirect} from './react-router-dom';

import Home from './components/Home';
import User from './components/User';
import Profile from './components/Profile';
import Protected from './components/Protected'; //受保护
import Login from './components/Login';
import MenuLink from "./components/MenuLink";
import NavHeader from "./components/NavHeader";
ReactDOM.render(
    // router  传递history.location 以及 history.push  
    <Router>    
        {/* 调用history.push 将to的参数传入 */}
        {/* <Link to="/">Home</Link>   
        <Link to="/user">User</Link>
        <Link to="/profile">Profile</Link>
        <Link to="/login">登录</Link> */}
        <NavHeader >nav</NavHeader>
        <MenuLink exact to="/">Home</MenuLink>   
        <MenuLink to="/user">User</MenuLink>
        <MenuLink to="/profile">Profile</MenuLink>
        <MenuLink to="/login">登录</MenuLink>
        <Switch>
            <Route exact={true} path="/" component={Home}></Route>
            <Route path="/login" component={Login}></Route>
            <Route path="/user" component={User}></Route>
            {/* <Route path="/profile" component={Profile}></Route> */}
            <Protected  exact path="/profile" component={Profile}/>  
            <Redirect from="/home" to="/"></Redirect>
        </Switch>
       
    </Router>
,document.getElementById("root"));
```



#### createBrowserHistory
History 实现  暂可忽略

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


### path-to-regexp

路由中使用的正则匹配
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




