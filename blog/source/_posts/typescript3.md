---
title: typescript三 类
tags:
- typescript
categories:
- typescript 
---

## 定义类

``` ts
class PerSon{
    name: string = 'ww';
    age: number;
}
let p1 = new PerSon();

console.log(p1.name)
```

## 存取器
在 TypeScript 中，我们可以通过存取器来改变一个类中属性的读取和赋值行为
```ts
class User {
    myname:string;
    constructor(myname: string) {
        this.myname = myname;
    }
    get name() {
        return this.myname;
    }
    set name(value) {
        this.myname = value;
    }
}

let user = new User('ww');
user.name = 'ww2'; 
console.log(user.name);
```


## 参数属性

```ts
class User {
    constructor(public myname: string) {}
    get name() {
        return this.myname;
    }
    set name(value) {
        this.myname = value;
    }
}

let user = new User('ww');
console.log(user.name); 
user.name = 'ww2'; 
console.log(user.name);
```

##  readonly

readonly修饰的变量只能在构造函数中初始化
在 TypeScript 中，const 是常量标志符，其值不能被重新分配
TypeScript 的类型系统同样也允许将 interface、type、 class 上的属性标识为 readonly
readonly 实际上只是在编译阶段进行代码检查。而 const 则会在运行时检查（在支持 const 语法的 JavaScript 运行时环境中）

```ts
class Animal {
    readonly name: string
    constructor(name) {
        this.name = name;
    }
    changeName(name:string){
        this.name = name;
    }
}

let a = new Animal('xiaogou');
a.changeName('xiaomao');
```


## 继承

```ts
namespace a{
    class PerSon{
        myname: string ;
         age: number;
        constructor(myname: string,age: number){
            this.myname = myname;
            this.age = age
        }

        get (){
            console.log('get')
            return this.myname
        } 
        set (val:string){
            //this.myname = '333'
            console.log('set')
        }
    }

    class Son extends PerSon{
        stuNo: number
        constructor(myname: string,age: number,stuNo: number){
            super(myname,age)
            this.stuNo = this.stuNo
        }
        dec (){
            console.log(this.age)
        }
    }

    let p = new PerSon("1",2)
    let p1 = new Son("11",22,222);
    
    console.log(p.age)

    console.log(p1.age)
}

```



## 类里面的修饰符

```ts
namespace a{
    class PerSon{
        public myname: string ; //类里面 子类 其它任何地方外边都可以访问
        protected age: number;  //类里面 子类 都可以访问,其它任何地方不能访问
        private money: number;  //类里面可以访问， 子类和其它任何地方都不可以访问
        constructor(myname: string,age: number){
            this.myname = myname;
            this.age = age
            this.money = money
        }

        get (){
            console.log('get')
            return this.myname
        } 
        set (val:string){
            //this.myname = '333'
            console.log('set')
        }
    }

    class Son extends PerSon{
        stuNo: number
        constructor(myname: string,age: number,stuNo: number){
            super(myname,age)
            this.stuNo = this.stuNo
        }
        dec (){
            console.log(this.age)
        }
    }

    let p = new PerSon("1",2)
    let p1 = new Son("11",22,222);
    
    console.log(p.age)

    console.log(p1.age)
}

```


## 静态属性 静态方法

```ts
class Father {
    static className='Father';
    static getClassName() {
        return Father.className;
    }
    public name: string;
    constructor(name:string) {//构造函数
        this.name=name;
    }

}
console.log(Father.className);
console.log(Father.getClassName());
```
## 装饰器
### 类装饰器
```ts
namespace b {
    interface PerSon{
        xx: string;
        yy: string;
        age:number;
    }
    // function enhancer(target: any){
    //     target.prototype.xx ="xx"
    // }
    // function enhancer(target: any) :any{
    //     return class extends target {   // 替换老的类  跟Person无关了
    //          age :number = 10
    //     }
    // }

    function enhancer(num: number){
        return function enhancer(target: any) :any{
            return class extends target {   // 替换老的类  跟Person无关了
                age :number = num
            }
        }
    }
    @enhancer(1)
    class PerSon {
        myname: string = 'person'
        constructor(){

        }
    }
    let p = new PerSon()
    console.log(p.age)
    console.log(p.myname)
}
```

### 属性装饰器

```ts
//target 如果装饰的是个普通属性的话。指向的是类的原型
//target装饰的是一个类的属性static 那么这个target指向的是类的定义
namespace c{
    function uperCase(target: any,propertyName: string){
    
        let val = target[propertyName]
        const getter = () => val;
        const setter = (newVal: string)=>{
    
            val = newVal.toUpperCase();
        }
        delete target[propertyName]
        Object.defineProperty(target,propertyName,{
            get:getter,
            set: setter,
            enumerable:true,
            configurable:true,
            // writable:true
        })
    }

    function methodName(target:any,methodName:string){
        console.log(target,methodName)
    }
    class Person{
        @uperCase
        name: string = 'ww'
        @methodName
        getName(){
            console.log('getName')
        }
    }

    let person = new Person();
    person.name='zz'
    console.log(person.name)
}

```

### 参数装饰器
会在运行时当作函数被调用，可以使用参数装饰器为类的原型增加一些元数据
第1个参数对于静态成员是类的构造函数，对于实例成员是类的原型对象
第2个参数的名称
第3个参数在函数列表中的索引
```ts
namespace d{
    

    function addName(target:any,methodName:string,paramsIndex: number){
            console.log(target,methodName,paramsIndex)
            
    }
    class Animal{
        login(username: string, @addName name: string){
            console.log(username,name)
        }
    }

    let a = new Animal()
    a.login("ww","2")
  
}

```

### 装饰器执行顺序

```ts
namespace e {
    function Class1Decorator() {
        return function (target: any) {
            console.log("类1装饰器");
        }
    }
    function Class2Decorator() {
        return function (target: any) {
            console.log("类2装饰器");
        }
    }
    function MethodDecorator() {
        return function (target: any, methodName: string, descriptor: PropertyDescriptor) {
            console.log("方法装饰器");
        }
    }
    function Param1Decorator() {
        return function (target: any, methodName: string, paramIndex: number) {
            console.log("参数1装饰器");
        }
    }
    function Param2Decorator() {
        return function (target: any, methodName: string, paramIndex: number) {
            console.log("参数2装饰器");
        }
    }
    function PropertyDecorator(name: string) {
        return function (target: any, propertyName: string) {
            console.log(name + "属性装饰器");
        }
    }

    @Class1Decorator()
    @Class2Decorator()
    class Person {
        @PropertyDecorator('name')
        name: string = 'zhufeng';
        @PropertyDecorator('age')
        age: number = 10;
        @MethodDecorator()
        greet(@Param1Decorator() p1: string, @Param2Decorator() p2: string) { }
    }
}
/**
name属性装饰器
age属性装饰器
参数2装饰器
参数1装饰器
方法装饰器
类2装饰器
类1装饰器
 */
```

## 抽象类
抽象描述一种抽象的概念，无法被实例化，只能被继承
无法创建抽象类的实例
抽象方法不能在抽象类中实现，只能在抽象类的具体子类中实现，而且必须实现

``` ts
abstract class Animal{
    name: string="ww";
    abstract getName():string

}

class Cat extends Animal{
    getName():string{
        return this.name
    }
}
let cat = new Cat()
console.log(cat.getName())
```
访问修饰符
只读属性	readonly
静态属性	static
抽象类、抽象方法	abstract
## 抽象类 vs 接口
不同类之间公有的属性或方法，可以抽象成一个接口（Interfaces）
而抽象类是供其他类继承的基类，抽象类不允许被实例化。抽象类中的抽象方法必须在子类中被实现
抽象类本质是一个无法被实例化的类，其中能够实现方法和初始化属性，而接口仅能够用于描述,既不提供方法的实现，也不为属性进行初始化
一个类可以继承一个类或抽象类，但可以实现（implements）多个接口
抽象类也可以实现接口
```ts
namespace e{
    interface Pig{
        age: number
    }
    abstract class Animal{
        name: string='animal'
        constructor(){

        }
    }
    class  Ele {
        type: string='ele'
    }
    class Cat extends Animal implements Pig{
        age = 18
    }
    let c = new Cat()
    console.log(c)
}


```
## 抽象方法
抽象类和方法不包含具体实现，必须在子类中实现
抽象方法只能出现在抽象类中
子类可以对抽象类进行不同的实现
```ts
namespace f{
    abstract class Animal{
        name: string='animal'
        constructor(){

        }
        abstract getName():void
    }

    class Cat extends Animal{
        getName(){
            console.log(111)
        }
    }

    let c = new Cat()
    c.getName()
}

```
## 重写（override） vs 重载（overload）
重写是指子类重写继承自父类中的方法
重载是指为同一个函数提供多个类型定义

```ts
//重写
namespace g{
    class Animal{
        getName(){
            console.log('xx')
        }
    }
    class Cat extends Animal{
        getName(){
            console.log('gg')
            super.getname() //可以调用父类  super = Animal.prototype 两种指向，在静态方法和构造函数指向父类，在普通函数中指向父类的prototype
        }
    }

    let c = new Cat()
    c.getName()
}

//重载

function double(val:number):number
function double(val:string):string
function double(val:any):any{
  if(typeof val == 'number'){
    return val *2;
  }
  return val + val;
}

let r = double(1);
console.log(r);

```

## 继承 vs 多态
继承(Inheritance)子类继承父类，子类除了拥有父类的所有特性外，还有一些更具体的特性  子类不能继承父类的静态方法
多态(Polymorphism)由继承而产生了相关的不同的类，对同一个方法可以有不同的行为

```ts
class Animal{
    speak(word:string):string{
        return 'Animal: '+word;
    }
}
class Cat extends Animal{
    speak(word:string):string{
        return 'Cat:'+word;
    }
}
class Dog extends Animal{
    speak(word:string):string{
        return 'Dog:'+word;
    }
}
let cat = new Cat();
console.log(cat.speak('hello'));
let dog = new Dog();
console.log(dog.speak('hello'));

```