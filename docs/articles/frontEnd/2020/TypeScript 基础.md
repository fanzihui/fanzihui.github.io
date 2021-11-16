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

// never 代表用不存在的值的类型,never类型—即为除去所有可能情况后剩下的类型
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


// 抽象类, 经常作为基类使用,抽象类中的方法在派生类中必须实现
abstract class Person {
  constractor(public name: string) {}
  printName(): void {
    console.log('name' + this.name)
  }
  abstract printSay(): void;
}
// 派生类
class Child extends Person {
  constractor(name: string) {
    super(name)
  }

  printSay(): void {
    console.log('1231')
  }
}

// 把类当做接口使用
class Point {
    x: number;
    y: number;
}

interface Point3d extends Point {
    z: number;
}

let point3d: Point3d = {x: 1, y: 2, z: 3};
```
### TypeScript 函数

```typescript
// 函数分为有名字函数 和 匿名函数

// 为函数定义类型, 可选参数前面加 ? 
function add(x: number = 10, y?: number): number {
    return x + y;
}

let myAdd = function(x: number, y: number): number { return x + y; };

// 函数中的 this 指向

// 箭头函数中的 this 指向 类的本身

// 重载, 在定义重载的时候，一定要把最精确的定义放在最前面。

function Person (name: string, age: number, height?: number): void {}

function Person (name: string, age: number, sex: boolean): void {}

```
### TypeScript 泛型

```typescript

function identity<T>(arg: T): T {
    return arg;
}

identity<string>('132132')

// 类型推论,编译器自动确定类型
let output = identity("myString");

// 泛型类, 类的静态属性不能使用这个泛型类型
interface Lengthwise {
    length: number;
}

function loggingIdentity<T extends Lengthwise>(arg: T): T {
    console.log(arg.length);  // Now we know it has a .length property, so no more error
    return arg;
}

```

### TypeScript 枚举

```typescript
enum Direction {
    Up = 1,
    Down,
    Left,
    Right
}

Direction.Up    // 1
Direction.Left    // 3
Direction.Right    // 4


// 常数枚举
const enum Enum {
    A = 1,
    B = A * 2
}

// 外部枚举
declare enum Enum {
    A = 1,
    B,
    C = 2
}


```

### TypeScript 类型兼容性

```typescript
// 类型系统强制源函数的返回值类型必须是目标函数返回值类型的子类型。
let x = () => ({name: 'Alice'});
let y = () => ({name: 'Alice', location: 'Seattle'});

x = y; // OK
y = x; // Error because x() lacks a location property

// 类型保护常用的是:  typeof  和 instanceof


// 映射类型 及 类型推断
type Record<K extends string, T> = {
    [P in K]: T;
}
type ThreeStringProps = Record<'prop1' | 'prop2' | 'prop3', string>
```

### TypeScript 模块
```typescript
// 导出  export
// 导入  import   || require
```
### TypeScript 命名空间
### TypeScript 命名空间



