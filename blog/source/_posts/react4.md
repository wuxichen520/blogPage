---
title: react 四 状态
tags:
- react
categories:
- react 
---
组件的数据来源有两个地方，分别是属性对象和状态对象
属性是父组件传递过来的(默认属性，属性校验)
状态是自己内部的,改变状态唯一的方式就是setState
属性和状态的变化都会影响视图更新


## State的赋值

给类的实例赋值
```js
/**
     * 合成事件  React合成事件
     * 事件代理
     * event并不是原始的dom对象，而是 react 自己二次封装的事件对象，可以实复用
     * setState
     * 异步
     * 合并
     * 当你在进行事件处理的时候，会进行批量更新状态。
     * 先缓存所有的更新，然后等事件结束之后再进行统一的更新
     * 
     */
class Counter extends React.Component {
    //也可以定义或者说初始化状态
    //类的构造函数 类的原型 类的实例
    //state = { number: 0 }
    constructor(props) {
        super(props);
        //定义状态地方，或者 给状态初始化，或者说给this.state直接赋值的地方
        //构造函数是唯一给状态赋值的地方
        this.state = { name: 'zhufeng', number: 0 };
    }
    render(){
        return (
            <div></div>
        )
    }
```

##  State 的更新可能是异步的 
* 出于性能考虑，React 可能会把多个 setState() 调用合并成一个调用
* 因为 this.props 和 this.state 可能会异步更新，所以你不要依赖他们的值来更新下一个状态
* 可以让 setState() 接收一个函数而不是一个对象。这个函数用上一个 state 作为第一个参数

### 异步
```js
let update = [];
let updateQueue = [];
let state = {number:0}
function setState(newState){
    updateQueue.push(newState)
}

setState({number:state.number +1})
setState({number:state.number +1})
setState({number:state.number +1})

updateQueue.forEach(newState => state = newState)
console.log(state) // {number:1}
```
当你在进行事件处理的时候，会进行批量更新状态。
先缓存所有的更新，然后等事件结束之后再进行统一的更新
```js
  class Counter  {

    constructor(props) {
      
        this.state = { number: 0 };
        this.batchUpdate = false
        this.updateQueue = [];
    }
    setState(newState){
        if( this.batchUpdate){
            this.updateQueue.push(newState)
        }
    }
    flushUpdate(){
       this.updateQueue.forEach(newState => state = newState)
    }
      /**
     * 在组件类的实例中 this 是不是类的实例?
     * 一般来说类的方法里的this是undefined
     * 那如何让普通方法的this指定组件实例 
     * 1. 箭头函数 add = () =>{}
     * 2. 匿名函数 ()=>this.add()
     * 3.  bind绑定 this.add.bind(this)
     *      3.1 在render里绑定  
     *      3.2 是在构造函数里绑定 constructor(props) {
                                    super(props);
                                    this.add.bind(this)
                                }
     */
    add(){
        this.batchUpdate = true //开启批量更新
        setState({number:this.state.number +1})
        setState({number:this.state.number +1})
        setState({number:this.state.number +1})

        this.flushUpdate()
    }
    // render() {
    
    //     console.log('render');
    //     return (
    //         <div>
    //             <p>{this.state.name}</p>
    //             <p>{this.state.number}</p>
    //             <button onClick={() => this.add()}>+</button>
    //         </div>
    //     )
    // }
}



let c = new Counter();
c.add()
```
### 同步
```js
//函数参数是上一个状态 返回的是下一个状态
   add(){
        this.setState((pre)=>({number:pre.number +1}),()=>{
            console.log(1,this.state)
        })
        this.setState((pre)=>({number:pre.number +2}),()=>{
            console.log(2,this.state)
        })
        this.setState((pre)=>({number:pre.number +3}),()=>{
            console.log(3,this.state)
        })
    }
```
```js
class Counter  {
    constructor(props) {
        this.state = { number: 0 };
        this.batchUpdate = false
        this.updateQueue = [];
        this.callbackQueue=[]
    }
    setState(updateState,callback){
        if( this.batchUpdate){
            this.updateQueue.push(updateState)
            this.callbackQueue.push(callback)
        }
    }
    flushUpdate(){
       //this.updateQueue.forEach(newState => state = newState)
       let state = this.state
       for(let i =0; i < this.updateQueue.length; i++){
        state =  this.updateQueue[i](state)
       }
       this.state = state;
       this.callbackQueue.forEach(callback=>callback())
    }
    add(){
        this.batchUpdate = true //开启批量更新
        this.setState((pre)=>({number:pre.number +1}),()=>{
            console.log(1,this.state)
        })
        this.setState((pre)=>({number:pre.number +2}),()=>{
            console.log(2,this.state)
        })
        this.setState((pre)=>({number:pre.number +3}),()=>{
            console.log(3,this.state)
        })

        this.flushUpdate()
    }
}
let c = new Counter();
c.add()

```
### 在定时器执行setState
在setTimeout里面如果调用setState的话，会立刻更新
```js
 //在setTimeout里面如果调用setState的话，会立刻更新
        setTimeout(()=>{
            this.setState({number:this.state.number +1})
            console.log(this.state) //{number:1}
        })

```

```js
// let update = [];
// let updateQueue = [];
// let state = {number:0}
// function setState(newState){
//     updateQueue.push(newState)
// }

// setState({number:state.number +1})
// setState({number:state.number +1})
// setState({number:state.number +1})

// updateQueue.forEach(newState => state = newState)
// console.log(state)




/**
 * 属性 父组件传过来的，自己是不能控制，也不能改变
 * 状态 状态是组件自己内部产生的维护的,由自己维护，外界无法访问。唯一改变状态 的方式就是setState
 * 
 */

class Counter  {
    constructor(props) {
        this.state = { number: 0 };
        this.batchUpdate = false
        this.updateQueue = [];
        this.callbackQueue=[]
    }
    setState(updateState,callback){
        if( this.batchUpdate){
            this.updateQueue.push(updateState)
            this.callbackQueue.push(callback)
        }else{
            this.state = typeof updateState == 'function'?updateState(this.state) : updateState
        }

    }
    flushUpdate(){
       //this.updateQueue.forEach(newState => state = newState)
       let state = this.state
       for(let i =0; i < this.updateQueue.length; i++){
        state = typeof this.updateQueue[i] == 'function'?this.updateQueue[i](state) : this.updateQueue[i]
       }
       this.state = state;
       this.callbackQueue.forEach(callback=>callback())
       this.batchUpdate = false
    }
    add(){
        this.batchUpdate = true //开启批量更新
        this.setState({number:this.state.number +1})
        console.log(this.state)

        this.flushUpdate()
    }
    // render() {

    //     return (
    //             <button onClick={() => this.add()}>+</button>
    //     )
    // }
}
let c = new Counter();
c.add()
```

### State 的更新会被合并
```js
 constructor(props) {
        super(props);
        
        this.state = {name:"ww", number: 0 }; //添加name
    }
    add(){
        this.setState({number:this.state.number +1})
    }
```
```js
class Counter  {
    constructor(props) {
        this.state = {name:"ww", number: 0 };
        this.batchUpdate = false
        this.updateQueue = [];
        this.callbackQueue=[]
    }
    setState(partialState,callback){
        if( this.batchUpdate){
            this.updateQueue.push(partialState)
            if(callback){
                this.callbackQueue.push(callback)
            }
            
        }else{
            this.state = typeof partialState == 'function'?partialState(this.state) : partialState
        }

    }
    flushUpdate(){
       let state = this.state
       for(let i =0; i < this.updateQueue.length; i++){
        let partialState =  typeof this.updateQueue[i] == 'function'?this.updateQueue[i](state) : this.updateQueue[i]
        this.state = {...state,...partialState}; //合并
       }
     
       this.callbackQueue.forEach(callback=>callback())
       this.batchUpdate = false
    }
    add(){
        this.batchUpdate = true //开启批量更新
        this.setState({number:this.state.number +1})
        this.flushUpdate()
    }
}
let c = new Counter();
c.add()
console.log(c.state)
```
##  数据是向下流动的
不管是父组件或是子组件都无法知道某个组件是有状态的还是无状态的，并且它们也并不关心它是函数组件还是 class 组件
这就是为什么称 state 为局部的或是封装的的原因,除了拥有并设置了它的组件，其他组件都无法访问
任何的 state 总是所属于特定的组件，而且从该 state 派生的任何数据或 UI 只能影响树中“低于”它们的组件
如果你把一个以组件构成的树想象成一个 props 的数据瀑布的话，那么每一个组件的 state 就像是在任意一点上给瀑布增加额外的水源，但是它只能向下流动

