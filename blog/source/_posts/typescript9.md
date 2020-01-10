---
title: typescript九 类型声明
tags:
- typescript
categories:
- typescript 
---

## 普通类型声明

``` ts
//jquery js 没有ts
namespace a{
    declare const $:(selector: string)=>{
        click():void
        with(length: number): void
    }
    
    $("#root").click()
    $("#root").with(100)
    
    declare let name1: string;
    declare function getName(name:string) : string;
    declare class Ani {name: string}

    interface Person{
        name: string
    }
    type Student = Person | string

    declare enum Seasons{
        Spring,
        Summer,
        Auyumn,
        Winter
    }
    let seasons :Seasons[] = [
        Seasons.Spring,
        Seasons.Summer,
        Seasons.Auyumn,
        Seasons.Winter,
    ]
}

```

## 复杂类型声明 
let、const、var 声明
namespace 或 module 声明包含一个 Value
enum 声明
class 声明
import 声明指向一个值
function 声明
``` ts

//jquery
// declare namespace jQuery{
//     function ajax(url: string,config: any) : void

//     let name: string; //let name: string = 'jquery'  只声明不赋值
//     namespace fn {
//         function extend(val: any): void
//     }
// }

jQuery.ajax('1',{})
jQuery.name;
jQuery.fn.extend({})

// 类型声明文件

//创建 typings 文件夹   创建子文件夹  *.d.ts    (如果报错 tsconfig  "include":["src/**/*","typings/**/*"])


//vscode Types auto installer  自动安装申明文件



```

## 合并声明

同一名称的两个独立声明会被合并成一个单一声明
合并后的声明拥有原先两个声明的特性
类既可以作为类型使用，也可以作为值使用，接口只能作为类型使用

|关键字|作为类型使用|作为值使用|
|:-|:-:|-:|
|class|	yes|	yes|
|enum|	yes	|yes|
|interface	|yes	|no|
|type|	yes	|no|
|function	|no	|yes|
|var,let,const	|no	|yes|

```ts
class Person{
    name:string=''
}
let p1:Person;//作为类型使用
let p2 = new Person();//作为值使用

interface Animal{
    name:string
}
let a1:Animal;
let a2 = Animal;//接口类型不能用作值
```


