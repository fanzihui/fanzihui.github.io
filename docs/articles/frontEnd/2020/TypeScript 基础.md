---
title: TypeScript 基础
date: 2021-11-13
author: FANLE
categories:
 - 前端
tags:
 - TypeScript
---

## TypeScript

TypeScript具有类型系统，且是JavaScript的超集。


### TypeScript 基础类型

```typescript
// boolean
let isDone: boolean = false;
// 数字
let number: number = 1;
// 字符串
let str: string = 'fanfan';
// 数字数组
let arr: number[] = [1, 2, 3, 5]
let list: Array[number] = [1, 2, 3, 4]
// 元组
let x: [string, number] = ['hello', 123]
// 枚举, 默认 从 0 开始
enum Color {
  Red,
  Green,
  Yellow
}

let g: Color = Color.Green
// 任意值
let anyData: any = '123123'

// 空值
function getUser(): void {
  console.log('name is fan')
}

// 联合类型
let sex: string|number|boolean = 0

// never 代表用不存在的值的类型
// 返回never的函数必须存在无法达到的终点
function error(message: string): never {
    throw new Error(message);
}

```
### TypeScript变量声明

``` TypeScript

// let   const  var  三者区别

// let 作用域问题, 下列闭包问题解决

for(let i = 0; i < 10; i ++) {
  setTimeout(() => {
    console.log(i)
  }, (1000));
}

// 输出结果
0
1
2
3
4
5
6
7
8
9

// 使用 var 时会产生闭包问题

// 数组解构
let in = [1, 2]
let [first, ...rest] = [1, 2, 3, 4];
console.log(first); // outputs 1
console.log(rest); // outputs [ 2, 3, 4 ]

// 对象结构
let o = {
    a: "foo",
    b: 12,
    c: "bar"
};

console.log({...o})

//输出结果
{a: "foo", b: 12, c: "bar"}

// 创建剩余变量
let { a, ...passthrough } = o;
let total = passthrough.b + passthrough.c.length;

// 函数声明/传参
type C = { a: string, b?: number }
function f({ a, b }: C): void {
    // ...
}

let defaults = { food: "spicy", price: "$$", ambiance: "noisy" };
let search = { ...defaults, food: "rich" };
console.log(search)
// 输出结果
{food: "rich", price: "$$", ambiance: "noisy"}

```
### TypeScript 基础接口(interface)

函数传参时候多个变量需要限制时,使用 interface 声明
```typescript

interface Person {
  name: string;
  sex: boolean;
  // 可选属性
  height?: number;
  // 只读属性
  readonly color: number;
  // 其他任意数量属性
  [propName: string]: any;
}

function getPerson({ person: Person}) : Person {
  person.name = 'fanfan'
  return person
}

getPerson({ name: 'fan', sex: true })



// 继承接口
interface Person {
  name: string;
}

interface Child extends Person{
  age: number
}

interface Son extends Person, Child{
  sex: number
}

let son = <Son> {}
son.name = 'son'
son.age = 10
son.sex = 1
```

### TypeScript 类

```typescript
// 创建类
class Greeter {
  // 属性
  greeting: string;
  // 构造函数
  constructor(message: string) {
    this.greeting = message
  }
  // 方法
  greet() {
    return 'hello ' + this.greeting
  }
}

let greeter  = new Greeter('fanfan')


// 继承, 使用 super 调用上基类的函数
class Animal {
  name: string;
  constructor(theName: string) { this.name = theName; }
  say(msg: string = '123') {
    console.log('msg' + msg)
  }
}

class Dog extends Animal {
  bark() {
    console.log('woof')
  }
}

class Snake extends Animal {
    constructor(name: string) { super(name); }
    say(msg = '1231') {
        console.log("Slithering...");
        super.say(msg);
    }
}

const dog = new Dog();
dog.bark();
dog.say('sllslsl');
dog.bark();

let sam = new Snake("Sammy the Python");
sam.say('hello')

```
### TypeScript 函数
### TypeScript 泛型
### TypeScript 枚举
### TypeScript type
### TypeScript 继承



