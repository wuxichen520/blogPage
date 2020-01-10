---
title: typescript八 类型变换
tags:
- typescript
categories:
- typescript 
---

## 交叉类型
两个接口类型的属性的并集
``` ts

  interface Bird{
        name: string;
        fly():void
    }
    interface Person{
        name: string;
        eat():void
    }
    
    type BirdMan = Bird & Person;
    
    let p: BirdMan = {
        name:"xxx",
        fly(){},
        eat(){}
    }
```

## typeof 
可以获取一个变量的类型
``` ts
   type Person = {
        name:string,
        age:number
    }

    let p: Person = {
        name:'zz',
        age:13
    }
    type Person1 = typeof p;

    let p2: Person1 = {
        name:"xx",
        age:20
    }
```

## 索引访问操作符

通过[]来获取一个类型的子类型
``` ts
 type Person = {
        name:string,
        age:number,
        job:{
            name:string
        },
        inters:{name: string; level:number}[]
    }
    let myname1: Person['job']['name'] ='w'
    let myname2: Person['job'] ={name:"x"}
    let myname3:Person['inters'][0]["name"] = "10"
```

## keyof
索引类型查询操作符
``` ts
 interface Person{
        name:string,
        age:number,
        gender: 'male' | 'female'
   }
   //type PersonKey = 'name' | 'age' | 'gender';
   type PersonKey  = keyof Person
   function getVal(val:Person, key:PersonKey){
        return val[key]
   }

    let p :Person= {
        name:"1",
        age:1,
        gender: 'male'
    }
    let name = getVal(p,'name')
```

## 映射类型
在定义的时候用in操作符去批量定义
``` ts

 interface Person{
         name:string,
         age:number,
         gender: 'male' | 'female'
    }
    //type PersonKey = 'name' | 'age' | 'gender';
    type PersonKey  ={[key in keyof Person]?:PersonKey[key]}


    //Partial
    //type Partial<T>  ={[key in keyof T]?:T[key]}
    type PersonKey1 = Partial<Person>

    //Required
    //type Required<T>  ={[key in keyof T]-?:T[key]}
    type PersonKey2 = Required<Person>

    let p: PersonKey2 ={
        name:"1",
        age:2,
        gender: 'male' 
    }


    //Readonly
    //type Readonly<T>  ={readonly [key in keyof T]:T[key]}
    type PersonKey4 = Readonly<Person>
    let p2: PersonKey4 ={
        name:"1",
        age:2,
        gender: 'male' 
    }
    //p2.name = "2"


    //Pick
    // type Pick<T,k extends keyof T>  ={ [key in k]:T[key]}
    type PersonKey5 = Pick<Person,'name'>
    let p3: PersonKey5 ={
        name:"1",
    }


    

```

## 内置工具类型

### Partial
Partial 可以将传入的属性由非可选变为可选
``` ts
type Partial<T> = { [P in keyof T]?: T[P] };

interface A {
  a1: string;
  a2: number;
  a3: boolean;
}

type aPartial = Partial<A>;

const a: aPartial = {}; // 不会报错
```
### Required
Required 可以将传入的属性中的可选项变为必选项，这里用了 -? 修饰符来实现
``` ts
//type Required<T> = { [P in keyof T]-?: T[P] };

interface Person{
  name:string;
  age:number;
  gender?:'male'|'female';
}
/**
 * type Require<T> = { [P in keyof T]-?: T[P] };
 */
let p:Required<Person> = {
  name:'zhufeng',
  age:10,
  //gender:'male'
}
```

### Readonly
Readonly 通过为传入的属性每一项都加上 readonly 修饰符来实现
``` ts
interface Person{
  name:string;
  age:number;
  gender?:'male'|'female';
}
//type Readonly<T> = { readonly [P in keyof T]: T[P] };
let p:Readonly<Person> = {
  name:'zhufeng',
  age:10,
  gender:'male'
}
p.age = 11;
```

### Pick 
Pick 能够帮助我们从传入的属性中摘取某一项返回
``` ts
interface Animal {
  name: string;
  age: number;
}
/**
 * From T pick a set of properties K
 * type Pick<T, K extends keyof T> = { [P in K]: T[P] };
 */
// 摘取 Animal 中的 name 属性
type AnimalSub = Pick<Animal, "name">; //{ name: string; }
let a:AnimalSub = {
    name:'zhufeng',
    age:10
}
```

### 映射类型修饰符的控制
TypeScript中增加了对映射类型修饰符的控制
具体而言，一个 readonly 或 ? 修饰符在一个映射类型里可以用前缀 + 或-来表示这个修饰符应该被添加或移除
TS 中部分内置工具类型就利用了这个特性（Partial、Required、Readonly...），这里我们可以参考 Partial、Required 的实现

## 条件类型
在定义泛型的时候能够添加进逻辑分支，以后泛型更加灵活

### 定义条件类型
``` ts
 interface Fish {
     name1: string
 }
 interface Fish2 {
    name1: string
    age: number
}
 interface Water {
     name2: string
 }
 interface Bird {
    name3: string
}
interface Sky {
    name4: string
}

type Condition<T> = T extends Fish?Water : Sky

let cond1 : Condition<Fish> = {
    name2:"2"
}
let cond2 : Condition<Fish2> = {
    name2:"2"
}
```

### 条件类型的分发
``` ts
 interface Fish {
     name1: string
 }
 interface Fish2 {
    name1: string
    age: number
}
 interface Water {
     name2: string
 }
 interface Bird {
    name3: string
}
interface Sky {
    name4: string
}
type Condition2<T> = T extends Fish?Water : Sky
let cond3 : Condition<Fish | Bird> = {
    name2:"2",
    name4:"3",
    
}

let cond4 :Water | Sky = {
    name2:"2",
    name4:"3"
}
```

### 内置条件类型
``` ts

 type E1 = Exclude<string | number,string>//从前者中排出

    let e1: E1 = 10  //不能为字符串

    type E2 = Extract<string | number,string>//从前者中提取

    let e2: E2 = "1"  //只能为字符串

    type E3 = NonNullable<string | number|undefined|null>//排出为空选项
    let e3: E3 = "1"  
    let e4: E3 = 10 


    // ReturnType 

    function getUserInfo(){
        return {name:"xx",age:18}
    }
    type UserInfo = ReturnType<typeof getUserInfo>

    let user : UserInfo = {
        name:"xx",age:18
    }

    //InstanceType

    class Person{
        name: string;
        constructor(name:string){
            this.name = name
        }
    }

    type P = InstanceType<typeof Person> 
    let p :P = new Person("xx")
```

#### Exclude
从 T 可分配给的类型中排除 U
```ts
type  E = Exclude<string|number,string>;
let e:E = 10;
```
#### Extract 
从 T 可分配的类型中提取 U
``` ts
type  E = Extract<string|number,string>;
let e:E = '1';
```

#### NonNullable
从 T 中排除 null 和 undefined
``` ts
type  E = NonNullable<string|number|null|undefined>;
let e:E = null;
```

#### ReturnType 
获取函数类型的返回类型
``` ts
function getUserInfo() {
  return { name: "ww", age: 10 };
}

// 通过 ReturnType 将 getUserInfo 的返回值类型赋给了 UserInfo
type UserInfo = ReturnType<typeof getUserInfo>;

const userA: UserInfo = {
  name: "ww",
  age: 10
};
```

#### InstanceType
获取构造函数类型的实例类型
``` ts
class Person{
  name:string;
  constructor(name){
    this.name = name;
  }
  getName(){console.log(this.name)}
}

type  P = InstanceType<typeof Person>;
let p:P = {name:'zhufeng',getName(){}};
```

## 模块VS命名空间
### 模块
模块是TS中外部模块的简称，侧重于代码和复用
模块在期自身的作用域里执行，而不是在全局作用域里
一个模块里的变量、函数、类等在外部是不可见的，除非你把它导出
如果想要使用一个模块里导出的变量，则需要导入


``` ts
export const a = 1;
export const b = 2;
export default 'xx';
import name, { a, b } from './1';
console.log(name, a, b);
```

### 命名空间
在代码量较大的情况下，为了避免命名空间冲突，可以将相似的函数、类、接口放置到命名空间内
命名空间可以将代码包裹起来，只对外暴露需要在外部访问的对象，命名空间内通过export向外导出
命名空间是内部模块，主要用于组织代码，避免命名冲突
```ts
export namespace zoo {
    export class Dog { eat() { console.log('zoo dog'); } }
}
export namespace home {
    export class Dog { eat() { console.log('home dog'); } }
}
let dog_of_zoo = new zoo.Dog();
dog_of_zoo.eat();
let dog_of_home = new home.Dog();
dog_of_home.eat();
import { zoo } from './3';
let dog_of_zoo = new zoo.Dog();
dog_of_zoo.eat();
```
