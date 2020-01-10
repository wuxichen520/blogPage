---
title: typescript七 类型保护
tags:
- typescript
categories:
- typescript 
---

类型保护就是一些表达式，他们在编译的时候就能通过类型信息确保某个作用域内变量的类型
类型保护就是能够通过关键字判断出分支中的类型

## typeof 类型保护

```ts
function double(inp: string | number | boolean){
    if(typeof inp === 'string'){
        inp.toLowerCase()
    }
    if(typeof inp === 'number'){
        inp.toFixed()
    }
   
}
```

## instanceof类型保护
```ts
    class Ani {
        name: string="bird"
    }
    class Cat extends Ani{
        age: number=18
    }

    function getName(a:Ani){
        if(a instanceof Cat){
            a.age
        }else{
            a.name
        }
    } 

```
## null保护

如果开启了strictNullChecks选项，那么对于可能为null的变量不能调用它上面的方法和属性
```ts
function getFn(s: string | null){
    // if(s === null){
    //     s=""
    // }
    // s = s|| ""
    // return s.charAt(0)


    function ensure(){
        s = s|| ""
    };
    ensure();
    return s!.charAt(0)
}

```

## 链判断运算符
链判断运算符是一种先检查属性是否存在，再尝试访问该属性的运算符，其符号为 ?.
如果运算符左侧的操作数 ?. 计算为 undefined 或 null，则表达式求值为 undefined 。否则，正常触发目标属性访问，方法或函数调用。
```ts
a?.b; //如果a是null/undefined,那么返回undefined，否则返回a.b的值.
a == null ? undefined : a.b;

a?.[x]; //如果a是null/undefined,那么返回undefined，否则返回a[x]的值
a == null ? undefined : a[x];

a?.b(); // 如果a是null/undefined,那么返回undefined
a == null ? undefined : a.b(); //如果a.b不函数的话抛类型错误异常,否则计算a.b()的结果

a?.(); //如果a是null/undefined,那么返回undefined
a == null ? undefined : a(); //如果A不是函数会抛出类型错误
//否则 调用a这个函数

```
链判断运算符 还处于 stage1 阶段,TS 也暂时不支持
stage[link](https://blog.csdn.net/kinsxkins/article/details/79987184)

## 可辨识的联合类型
就是利用联合类型中的共有字段进行类型保护的一种技巧
相同字段的不同取值就是可辨识
```ts
interface Wan{
    class:"wan",
    text1:"up"
}
interface Dan{
    class:"Dan",
    text2:"del"
}

type But = Wan | Dan;

function getBtn(but:But){
    if(but.class=="wan"){
        but.text1
    }else{
        but.text2
    }
}

```

## in操作符 
in 运算符可以被用于参数类型的判断
```ts
interface Bird{
    swing: string
 }
 
 interface Dog{
     leg: string
 }
 
 function getName2(x:Bird | Dog){
         if("swing" in x){
             x.swing
         }else{
             x.leg
         }
 }

```

## 自定义的类型保护

TypeScript 里的类型保护本质上就是一些表达式，它们会在运行时检查类型信息，以确保在某个作用域里的类型是符合预期的
要自定义一个类型保护，只需要简单地为这个类型保护定义一个函数即可，这个函数的返回值是一个类型谓词
类型谓词的语法为 parameterName is Type 这种形式，其中 parameterName 必须是当前函数签名里的一个参数名`
```ts

interface Bird2{
    legs: number,
    name1:"1"
 }
 
 interface Dog2{
    legs: number,
    name2:"1"
 }

 function isBird(x : Bird2 | Dog2) :x is Bird2{
    return x.legs === 2
 }

 function getAni(x : Bird2 | Dog2){
     if(isBird(x)){
        console.log( x.name1)
     }else{
        console.log( x.name2)
     }
 }

 let x4 : Bird2={name1:"1",legs:2}

 getAni(x4)
```