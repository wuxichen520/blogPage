---
title: typescript六 结构类型系统
tags:
- typescript
categories:
- typescript 
---

类型无关  跟属性有关

## 接口的兼容性

```ts
namespace a{
    interface Ani{
        name: string;
        age: number
    }
    
    interface Per{
        name: string;
        age: number;
        speak:(words: string)=> void
    }
    // Per  包含Ani  的属性 所以可以传入 反之  不行
    let p: Per= {
        name:'ww',
        age:10,
        speak(){

        }
    }
    
    function getName(ani: Per): string{
        return ani.name
    }
    
    console.log(getName(p))
}

```


## 基本类型的兼容性

```ts
let num : string | number =1;
let str: string = 'hello'
num = str


let num2 : {
    toString(): string
}

let str2: string = 'ww'
//str2.toString()
num2 = str2 // string类型中有toString方法

```


## 类的兼容性
在TS中是结构类型系统，只会对比结构而不在意类型
```ts
namespace b{
    class Ani{
        name: string
    }
    class Bird extends Ani{
        swing: number
    }
    let a: Ani;
    a = new Bird(); //父类跟子类都有name属性

    // let b: Bird();
    // b = new Ani()// 父类没有 swing的属性
}
```


## 函数的兼容性 

比较函数的时候是要先比较函数的参数，再比较函数的返回值

### 比较参数
```ts
namespace c{
    type sumFn = (a:number,b:number)=>number
    let sum: sumFn

    function f1(a: number, b: number): number{
        return a
    }
    sum = f1
    function f2(a: number): number{
        return a
    }
    sum = f2

    function f3(): number{
        return 1
    }
    sum = f3

    function f4(a: number, b: number,c:number): number{
        return 1
    }
    //sum = f4 只能少不能多
}

```


### 比较返回值 
```ts
namespace d{
    type GetPer = ()=>{
        name: string,age: number
    }
    let gerPer: GetPer
    function g1 (){
        return {name:"1",age:1}
    }
    gerPer = g1
    function g2 (){
        return {name:"1",age:1,zz:"2"}
    }
    gerPer = g2
    function g3(){
        return {name:"1"}
    }
    //gerPer = g3 //只能多不能少
}
```


## 函数参数的协变
```ts
type logFn =(a:number|string)=>void

let log: logFn;

function log1(a:number| string | boolean){

}
log = log1

function log2(a:number){
    console.log(a)
}
//log = log2
```
## 泛型的兼容性
泛型在判断兼容性的时候会先判断具体的类型,然后再进行兼容性判断
```ts
interface Empty<T>{
    
}
let x:Empty<string>
let y:Empty<number>
x=y  //都是空对象

interface Empty2<T>{
    data: T
}
let x2:Empty2<string> //{data:string}
let y2:Empty2<number> //{data:number}
//x2=y2 // X
```


## 枚举的兼容性

```ts
enum Colors {
    Red,Yellow
}

let c1: Colors;
c1 = Colors.Red;  //0

c1 = 1

let d1: number;
 d1 =  Colors.Red

```