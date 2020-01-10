---
title: typescript五 泛型
tags:
- typescript
categories:
- typescript 
---

泛型（Generics）是指在定义函数、接口或类的时候，不预先指定具体的类型，而在使用的时候再指定类型的一种特性
泛型T作用域只限于函数内部使用
## 泛型函数
```ts
    function createArr(length: number,val: any):Array<any> {
        let result: Array<any> = [];
        for (let i=0;i<length;i++){
            result.push(val)
        }
        return result
    }


    //使用泛型
    function createArr<T>(length: number,val: T):Array<T> {
        let result: Array<any> = [];
        for (let i=0;i<length;i++){
            result.push(val)
        }
        return result
    }

    console.log(createArr<number>(3,1))
    console.log(createArr<string>(3,"1"))
```
## 类数组

```ts
function sum(...args: any[]){
    let ars: IArguments = arguments ;
    console.log(ars)
    for (let i=0;i++;i<args.length){
        console.log(args[i])
    }
}
sum(1,2,3)

let root: HTMLElement | null = document.getElementById("root")
let children: HTMLCollection = (root as HTMLElement).children;
children.length;
let nodeList: NodeList = (root as HTMLElement).childNodes;
nodeList.length;
```
## 泛型类

```ts
class MyArray<T>{
    list: T[] = [];
    add(val: T){
        this.list.push(val)
    }
    getMax(): T{
        let result: T = this.list[0]
        for(let i=0;i<this.list.length;i++){
            if(this.list[i]>result){
                result = this.list[i]
            }
        }
        return result
    }
}

let myArr = new MyArray<number> ();

myArr.add(1)
myArr.add(4)
myArr.add(2)

console.log(myArr.getMax())
```

## 泛型接口
```ts
interface Cal{
    <T>(a:T,b:T):T
}

let add: Cal = function <T>(a:T,b:T) :T{

    return a

}
```

## 多个类型参数
如何在不增加变量的情况下交换两个变量的值
```ts
function swap<A,B>(tuple:[A,B]): [B,A]{
 return [tuple[1],tuple[0]]
}

let result = swap<string,number>(['ww',20])
console.log(result)
```


## 默认泛型类型
```ts
function createArr<T = number>(length: number,val: T):Array<T> {
        let result: Array<T> = [];
        for (let i=0;i<length;i++){
            result.push(val)
        }
        return result
    }

    let result = createArr(3,'a')
    console.log(result)
```
## 泛型约束
在函数中使用泛型时 由于预先不知道具体类型 所以不能访问相应类型的方法
```ts
interface LenthWise{
    length: number
}
function logger<T extends LenthWise>(val: T){
    console.log(val.length)
    
}
logger('ww')
```


## 泛型接口
在函数中使用泛型时 由于预先不知道具体类型 所以不能访问相应类型的方法
```ts
interface LenthWise{
    length: number
}
function logger<T extends LenthWise>(val: T){
    console.log(val.length)
    
}
logger('ww')

interface Car<T>{
    list: T[]
}

let car: Car<string> = {list:["1"]}
```


## 泛型类型别名
```ts
  type Cart<T> = {list: T[]} | T[];

    let c1 : Cart<string> = ["2"]
    let c2 : Cart<number> = [2]
    let c3 : Cart<number> = {list:[1]}
```


## 泛型接口 vs 泛型类型别名
interface 定义一个实实在在的接口，它是一个真正的类型
type 一般用来定义别名，并不是一个真正的类型
接口创建了一个新的名字，它可以在其他任意地方被调用。而类型别名并不创建新的名字，例如报错信息就不会使用别名
类型别名不能被 extends和 implements,这时我们应该尽量使用接口代替类型别名
当我们需要使用联合类型或者元组类型的时候，类型别名会更合适
