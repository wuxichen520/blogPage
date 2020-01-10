---
title: typescript一 数据类型
tags:
- typescript
categories:
- typescript 
---

##  TypeScript安装和编译
``` bash
npm install typescript -g
// 查看安装是否成功 看见版本号代表成功
tsc -v
```
tscj简单命令
```
// 编译tsc
tsc 文件名
// 编译tsc 到指定目录
tsc --outDir 指定目录文件名 入口文件名
// 文件热更新
tsc -w

```

### 创建ts配置文件

```
// 生成 tsconfig.json文件
tsc --init
// 修改 tsconfig.json文件 配合tsc -w实时热更新
    "outDir": "出口文件名" /* 出口文件设置 */,
    "rootDir": ".入口文件名" /* 入口文件设置 */,
```

tsconfig.json

```json
{
  "compilerOptions": { //编译选项
    "target": "es5",                          /* 编译目标: 'ES3' (default), 'ES5', 'ES2015', 'ES2016', 'ES2017', 'ES2018', 'ES2019' or 'ESNEXT'. */
    "module": "commonjs",                     /* 编译语法: 'none', 'commonjs', 'amd', 'system', 'umd', 'es2015', or 'ESNext'. */
    /* Strict Type-Checking Options */
    "strict": true,                           /* 严格模式 */
    "noFallthroughCasesInSwitch": true,    /* Report errors for fallthrough cases in switch statement. */
    "esModuleInterop": true,                  /* 通过为所有导入创建命名空间对象，启用CommonJS和ES模块之间的互操作性。暗示“allowSyntticDefaultImports” */
    /* Advanced Options */
    "forceConsistentCasingInFileNames": true  /* Disallow inconsistently-cased references to the same file. */
  }
}

```

esModuleInterop 选项 作用

可以启用CommonJS和ES模块转义 为所有导出创建namespace（命名空间）  

npm i
es6.ts
```  ts
exoprt const name = 'xx'

```
comm.ts
```
 let es6 = require('./es6')//  es6={name:'xx'}  namespace
```

### vscode运行
Terminal->Run Task-> tsc:build 编译
Terminal->Run Task-> tsc:watch 编译并监听
```
    "build": "tsc",
    "build:watch" :"tsc --watch"
```
## 数据类型
``` ts
//Cannot redeclare block-scoped variable 'name'
 //如果代码里有export  import  之类的代码 那么这个文件变成一个模块
 export {}
``` 
### 布尔类型(boolean)
``` ts
let married: boolean = true;
```
### 数字类型(number)
``` ts
let age: number = 10;
```
### 字符串类型(string)
``` ts
let name: string = 'xx'
```
### 数组类型(array)
``` ts
let hobbies1:number[] = [1,2,3];
let hobbies2:string[] = ["1","2","3"];
let inh1:Array<string> = ["1","2","3"];
let inh2:Array<number> = [1,2,3];
```
### 元组类型(tuple)
类似一个数组  是一个长度与类型都固定的数组
1.长度固定
2.类型可以不一样
``` ts
let point:[number,number] = [100,100];
point[0],point[1];

let person:[number,string]=[18,'wx'];
```
### 枚举类型(enum)

事先考虑某一个变量的所有的可能的值，尽量用自然语言中的单词表示它的每一个值
比如性别、月份、星期、颜色、单位、学历

普通枚举可以改 常数枚举不能改
``` ts
```
#### 普通枚举
``` ts
enum Gener{
    BOY=2,
    GIRL=0
}
console.log(`gg是${Gener.BOY}`)
console.log(`mm是${Gener.GIRL}`)
```
#### 常数枚举
常数枚举与普通枚举的区别是，它会在编译阶段被删除，并且不能包含计算成员。
假如包含了计算成员，则会在编译阶段报错
``` ts
const enum Colors{
    Red,
    Yellow,
    Blue
}

console.log(Colors.Red,Colors.Yellow,Colors.Blue)
```
### 任意类型(any)
any就是可以赋值给任意类型
第三方库没有提供类型文件时可以使用any
类型转换遇到困难时
数据结构太复杂难以定义
``` ts
let root :any = document.getElementById('root');
root.style.color = 'red' //! 断言不为空
```
### null 和 undefined
strictNullChecks (tsconfig改为false就不会报错)
null 和 undefined 是其它类型的子类型，可以赋值给其它类型，如数字类型，此时，赋值后的类型会变成 null 或 undefined
``` ts
let x: number;
x = 1;
x = undefined;    
x = null;   

let y: number | null | undefined;
y = 1;
y = undefined;   
y = null;  
```
### void 类型
表示没有任何类型
当我们声明一个变量类型是 void 的时候，它的非严格模式下仅可以被赋值为 null 和 undefined;
``` ts
function greestring(name:string) :void {
    console.log(`${name}`)
    return null;  // 赋值为 null 和 undefined
}

greestring('ww')
```
### never类型 
代表不会出现的值  循环/事件队列
作为不会返回（ return ）的函数的返回值类型

``` ts
// 返回never的函数 必须存在 无法达到（ unreachable ） 的终点
function error(message: string): never {
    throw new Error(message);
}

// 由类型推论得到返回值为 never
function fail() {
    return error("Something failed");
}

// 返回never的函数 必须存在 无法达到（ unreachable ） 的终点
function infiniteLoop(): never {
    while (true) {}
}
```

never 和 void 的区别
void 可以被赋值为 null 和 undefined的类型。 never 则是一个不包含值的类型。
拥有 void 返回值类型的函数能正常运行。拥有 never 返回值类型的函数无法正常返回，无法终止，或会抛出异常。
### 类型推论
定义时未赋值就会推论成any类型
如果定义的时候就赋值就能利用到类型推论
``` ts
let username2;
username2 = 10;
username2 = 'zhufeng';
username2 = null;
```
### 包装对象（Wrapper Object） 
自动在基本类型和对象类型之间切换
JavaScript 的类型分为两种：原始数据类型（Primitive data types）和对象类型（Object types）
原始数据类型：布尔值、数值、字符串、null、undefined、Symbol
``` ts
let name1: string ='wx';
name1.toLocaleLowerCase(); 
//1.基本类型没有方法  但会在内部迅速完成一个装箱的操作 ：把基本类型包装成对象类型，然后用对象来调方法
name1.toLocaleLowerCase() === /*let name11 = new String(name1); name11.toLocaleLowerCase()*/
```
### 联合类型
联合类型上只能访问两个类型共有的属性和方法
``` ts
let name2: string | number;
```
### 类型断言
类型断言可以将一个联合类型的变量，指定为一个更加具体的类型
``` ts
let name3: string | number;
(name3 as number).toFixed();
(name3 as string).toLocaleLowerCase();
```
### 字符串、数字、布尔值字面量
``` ts
let name4:'BOY' |'GIRL';
name4 = 'BOY'
```
### 字符串字面量 vs 联合类型
字符串字面量类型用来约束取值只能是某几个字符串中的一个, 联合类型（Union Types）表示取值可以为多种类型中的一种
字符串字面量 限定了使用该字面量的地方仅接受特定的值,联合类型 对于值并没有限定，仅仅限定值的类型需要保持一致