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

### TypeScript 基础接口
### TypeScript 类
### TypeScript 函数
### TypeScript 泛型
### TypeScript 枚举
### TypeScript type
### TypeScript 继承



