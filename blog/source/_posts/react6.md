---
title: react 六 生命周期
tags:
- react
categories:
- react 
---

##  旧版生命周期
```js
/**
 * 只有类组件才有生命周期
 */

class Counter extends React.Component {
    static defaultProps = {//0.设置默认属性
        name: 'ww'
    }
    constructor(props) {
        super(props);
        this.state = { count: 0 };//1.执行构造函数，设置初始状态
        console.log('1.执行构造函数，设置初始状态');
    }
    componentWillMount() {
        console.log('2.componentWillMount 组件将要挂载到页面上');
    }
    handleClick = (event) => {
        this.setState({ count: this.state.count + 1 });
    }
    //询问组件是否需要更新
    shouldComponentUpdate(nextProps, nextState) {
        console.log('5.shouldComponentUpdate 询问组件是否需要更新?');
        //return nextState.count % 3 == 0;
        return true;
    }
    componentWillUpdate() {
        console.log('6.componentWillUpdate 组件将要更新');
    }
    componentDidUpdate() {
        console.log('7.componentDidUpdate 组件的最新状态已经同步到界面中去了');
    }
    //render是要返回虚拟DOM
    render() {
        console.log('3.render 确定要显示虚拟DOM是什么?');
        return (
            <div>
                <p>{this.state.count}</p>
                <button onClick={this.handleClick}>+</button>
                <hr />
               
                {
                    this.state.count < 3 ? <ChildCounter count={this.state.count} /> : null
                }
            </div>
        )
    }
    //当此虚拟DOM已经被挂载到真实DOM中之后就会执行componentDidMount,这个时候才能获得真实DOM元素
    componentDidMount() {
        console.log('4.componentDidMount 组件挂载完成后');
    }
}

class ChildCounter extends React.Component {
    componentWillReceiveProps(newProps) {
        console.log('ChildCounter componentWillReceiveProps', newProps);
    }
    shouldComponentUpdate(nextProps, nextState) {
        console.log('ChildCounter 询问组件是否需要更新?');
        //return nextProps.count % 6 == 0;
        return true;
    }
    componentWillUpdate() {
        console.log('ChildCounter 组件将要更新');
    }
    componentDidUpdate() {
        console.log('ChildCounter 组件的最新状态已经同步到界面中去了');
    }
    componentWillUnmount() {
        console.log('ChildCounter 将要被销毁了');
    }
    render() {
        console.log('ChildCounter render');
        return (
            <div>{this.props.count}</div>
        )
    }
}
ReactDOM.render(<Counter  />, document.getElementById('root'));

/**
 * 父组件 子组件
 * componentWillMount 先父先子
 * componentDidMount 先父先子
 */

```

## 新版生命周期
新生命周期去掉三个函数  componentWillReceiveProps  componentWillUpdate componentWillMount
新增两个 getDerivedStateFromProps  getSnapshotBeforeUpdate

###  getDerivedStateFromProps
```js
class Counter extends React.Component {
    static defaultProps = {//0.设置默认属性
        name: 'ww'
    }
    constructor(props) {
        super(props);
        this.state = { count: 0 };//1.执行构造函数，设置初始状态
    }
    render() {
        return (
            <div>
                <p>{this.state.count}</p>
                <button onClick={this.handleClick}>+</button>
                <hr />
                <ChildCounter count={this.state.count} /> 
                
            </div>
        )
    }
    //当此虚拟DOM已经被挂载到真实DOM中之后就会执行componentDidMount,这个时候才能获得真实DOM元素
    componentDidMount() {
        console.log('4.componentDidMount 组件挂载完成后');
    }
}

class ChildCounter extends React.Component {
    state = {
        name:"wx",
    }
     //每当状态、属性变更的时候会插此方法。可以通过最新的属性得到最新的状态
    //nextProps新的属性对象 prevState代表老的状态对象
    static getDerivedStateFromProps(nextProps,prevState){
        if(nextProps.count % 2 == 0){
            return  {count:nextProps.count*2}
        }else{
            return  {count:nextProps.count*3}
        }
     
    }
    render() {
        return (
            <div>{this.state.count}</div>
        )
    }
}
ReactDOM.render(<Counter  />, document.getElementById('root'));
```

### getSnapshotBeforeUpdate
```js
import React from 'react';
import ReactDOM from 'react-dom';
const COLORS = ['red', 'yellow', 'blue', 'green']
class ScrollList extends React.Component {
    constructor() {
        super();
        this.container = React.createRef();
        this.state = { messages: [] };
    }
    addMessage = () => {
        this.setState({
            messages: [`${this.state.messages.length}`, ...this.state.messages]
        });
    }
    componentDidMount() {
        this.$timerID = window.setInterval(() => {
            this.addMessage();
        }, 1000);
    }
    //获取DOM更新前的快照
    getSnapshotBeforeUpdate() {
        // scrollHeight获取容器的内容高度
        return this.container.current.scrollHeight;//内容 高度=200px
    }
    componentDidUpdate(prevProps, prevState, prevScrollHeight) {//prevScrollHeight=200px
        let scrollHeight = this.container.current.scrollHeight;
        let newScrollTop = this.container.current.scrollTop + (scrollHeight - prevScrollHeight);
        this.container.current.scrollTop = newScrollTop;
    }
    componentWillUnmount() {
        clearInterval(this.$timerID);
    }
    render() {
        let style = {
            height: 100,
            width: 200,
            border: '1px solid red',
            overflow: 'auto'
        }
        return (
            <div style={style} ref={this.container}>
                {
                    this.state.messages.map((message, index) => (
                        <div key={index} style={{ backgroundColor: COLORS[index % 4] }}>{message}</div>
                    ))
                }
            </div>
        )
    }
}
ReactDOM.render(<ScrollList />, document.getElementById('root'));
```

