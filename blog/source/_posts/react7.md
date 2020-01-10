---
title: react 七 Context(上下文)
tags:
- react
categories:
- react 
---

## 使用
```js
import React from 'react';
import ReactDOM from 'react-dom';
/**
 * react是单向流  数据从上传下  只能通过属性
 * 
 * 1.层级特别多
 * 2.每个组件都需要
 */

let ThemeContext = React.createContext(); //上下文

function Title(props){   
    return (
        <ThemeContext.Consumer>
        {
            (value) =>(
                <div  style={{  border: `1px solid ${value.color}`}}>Title</div>
            )
        }
        </ThemeContext.Consumer>
    )
}
// class Title extends React.Component {
//     static contextType = ThemeContext   //接收上下文
//     render(){
//         return (
//                 <div  style={{  border: `1px solid ${this.context.color}`}}>Title</div>
//         )
//     }
// }
class Header extends React.Component {
    static contextType = ThemeContext   //接收上下文
    render(){
        return (
        <div style={{ border: `1px solid ${this.context.color}`}}>
            Header
                <Title />
        </div>
            
        )
    }
}
class Main extends React.Component  {
    static contextType = ThemeContext   //接收上下文
    render(){
        return (
            <div style={{ border: `1px solid ${this.context.color}`}}>
            Main
                <Content/>
            </div>
        )
    }
}
class Content extends React.Component {
    static contextType = ThemeContext   //接收上下文
    render(){
        return (
            <div style={{border: `1px solid ${this.context.color}` }}>
                Content
                <button onClick={()=>this.context.changeColor('red')}>变红</button>
                <button onClick={()=>this.context.changeColor('blue')}>变绿</button>
            </div>
        )
    }
}
class Panel extends React.Component {
    state = { color: 'green' }
    changeColor = (color) => {
        this.setState({ color });
    }
    render() {
        let value = { color: this.state.color, changeColor: this.changeColor };
        //Provider提供者，它负责向下层所有的组件提供数据value
        return (
            <ThemeContext.Provider value={value}>   
                <div style={{ border: `1px solid ${this.state.color}`, width: 300 }}>
                    Panel
                    <Header />
                    <Main />
                </div>
            </ThemeContext.Provider>
        )
    }
}

ReactDOM.render(<Panel />, document.getElementById('root'));
```

## 实现
```js
function createContext(initialValue){
    class Provider extends React.Component{
        static contextValue = initialValue
        constructor(props){
            super(props)
            this.state={}
            Provider.contextValue = props.value
        }
        static getDerivedStateFromProps(nextProps){
            Provider.contextValue = nextProps.value
            return {}
        }
        render(){
            console.log(this.props)
            return this.props.children
        }
    }
    class Consumer extends React.Component{
        render(){
            return this.props.children(Provider.contextValue)
        }
    }

    return {Provider,Consumer}
}

let ThemeContext = createContext(); //上下文

function Title(props){   
    return (
        <ThemeContext.Consumer>
        {
            (value) =>(
                <div  style={{  border: `1px solid ${value.color}`}}>Title</div>
            )
        }
        </ThemeContext.Consumer>
    )
}
function Header(props){   
    return (
        <ThemeContext.Consumer>
        {
            (value) =>(
                <div  style={{  border: `1px solid ${value.color}`}}>Header<Title /></div>
            )
        }
        </ThemeContext.Consumer>
    )
}
// function Main(props){   
//     return (
//         <ThemeContext.Consumer>
//         {
//             (value) =>(
//                 <div  style={{  border: `1px solid ${value.color}`}}>Main <Content/></div>
//             )
//         }
//         </ThemeContext.Consumer>
//     )
// }
class Main extends React.Component  {
    static contextType = ThemeContext   //接收上下文
    constructor(){
        super();
       // this.context = Title.contextType.Provider.contextValue
    }
    render(){
        return (
            <div style={{ border: `1px solid ${this.context.color}`}}>
            Main
                <Content/>
            </div>
        )
    }
}
function Content(props){   
    return (
        <ThemeContext.Consumer>
        {
            (value) =>(
                <div style={{border: `1px solid ${value.color}` }}>
                Content
                <button onClick={()=>value.changeColor('red')}>变红</button>
                <button onClick={()=>value.changeColor('blue')}>变绿</button>
             </div>
            )
        }
        </ThemeContext.Consumer>
    )
}

// class Main extends React.Component  {
//     static contextType = ThemeContext   //接收上下文
//     render(){
//         return (
//             <div style={{ border: `1px solid ${this.context.color}`}}>
//             Main
//                 <Content/>
//             </div>
//         )
//     }
// }

class Panel extends React.Component {
    state = { color: 'green' }
    changeColor = (color) => {
        this.setState({ color });
    }
    render() {
        let value = { color: this.state.color, changeColor: this.changeColor };
        //Provider提供者，它负责向下层所有的组件提供数据value
        return (
            <ThemeContext.Provider value={value}>   
                <div style={{ border: `1px solid ${this.state.color}`, width: 300 }}>
                    Panel
                    <Header />y
                    <Main />
                </div>
            </ThemeContext.Provider>
        )
    }
}

ReactDOM.render(<Panel />, document.getElementById('root'));
```
