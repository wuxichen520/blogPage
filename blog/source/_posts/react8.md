---
title: react 八 高阶组件
tags:
- react
categories:
- react 
---

高阶组件就是一个函数，传给它一个组件，它返回一个新的组件
高阶组件的作用其实就是为了组件之间的代码复用
## 函数(定义的)组件
```js
/**
 * 高阶组件高在哪?
 * 高阶来自我们的高阶函数
 * JS函数是一等公民，可以作为函数参数，也可以作为函数的返回值
 */
// function sum(fn, a, b) {
//     return fn(a, b);
//     return fn;
// }
/**
 * 高阶组件
 * React组件可以作为方法的参数和返回值
 *
 */


import React from 'react';
import ReactDOM from 'react-dom';
/**
 * 1. 代码会冗余
 * 2. 工作量大
 * 3. 逻辑耦合
 * 高阶组件是为了解决逻辑复用的问题
 * 如果包裹太多的话。就非常的复杂的
 * 以下就是嵌套太多
 * hooks可以解决
 */
function withLogger(OldComponent) {
    return class extends React.Component {
        componentWillMount() {
            this.start = Date.now();
        }
        componentDidMount() {
            console.log((Date.now() - this.start) + 'ms');
        }
        render() {
            return (
                <div>
                  
                    <OldComponent {...this.props}/>
                </div>
            )
        }
    }
}
class App extends React.Component{
    render(){
        return (
            <div>
                App {this.props.name}
            </div>
        )
    }
}
let LoggerApp = withLogger(App);
ReactDOM.render(<LoggerApp name="ww"/>, document.getElementById('root'));
```

