---
title: typescript四 接口
tags:
- typescript
categories:
- typescript 
---
## 对象的形状
```ts
//接口可以用来描述`对象的形状`,少属性或者多属性都会报错
interface Speakable{
    speak():void;
    name?:string;//？表示可选属性
}

let speakman:Speakable = {
    speak(){},//少属性会报错
    name,
    age//多属性也会报错
}
```

## 行为的抽象 
```ts
//接口可以在面向对象编程中表示为行为的抽象
interface Speakable{
    speak():void;
}
interface Eatable{
    eat():void
}
//一个类可以实现多个接口
class Person implements Speakable,Eatable{
    speak(){
        console.log('Person说话');
    }
    eat(){}
}
class TangDuck implements Speakable{
    speak(){
        console.log('TangDuck说话');
    }
    eat(){}
}
```
## 任意属性
``` ts
namespace a{
    // interface PlainObject{
    //     x:number;
    //     y:number;
    // }
    interface PlainObject{
        [propName: string]: number
    }
    let obj: PlainObject = {
        x:1,
        y:2
    }
}
```

## 接口继承
```ts
namespace b{
   interface Speakable{
       speak():void
   }

   interface SpeakChinese extends Speakable{
        speakChinese(): void
   }
   class Person implements SpeakChinese{
        speak(){}
        speakChinese(){}
   }
}
```

## 接口的readonly

```ts
interface Circle{
        readonly PI: number;
        radius: number
    }
 
    let circle: Circle ={
        PI:3.14,
        radius:4
    }
    //circle.PI=3.15 //不能更改
```

## 接口约束函数

```ts
 interface Discount{
      (price: number) : number
    }
 
   let cost: Discount = function(price:number){
    return price*1
   }
```
## 接口约束索引(数组和对象)

```ts

 interface usArr {
        [index: number]:number
    }
    let arr: usArr = [1,2,3];
    let objs: usArr = {
        1:2,
        "2":3
    }
 console.log(objs)

```
## 接口约束类
```ts
 interface Discount{
        name: string;
        getName(name: string): void
    }
    
    class Cost implements Discount{
        name:string
        getName(){
            
        }
    }

```

## 接口约束构造函数

```ts
 namespace f{
     class Animal {
         constructor(name: string){

         }
     }
 interface WitnName {
     new(name: string): Animal
 }
 function createAnimal(clazz: any,name: string){
     return new clazz(name)
 }
 let a = createAnimal(Animal,"z")
}
```




