---
title: TypeScript
categories: [TypeScript]
mermaid: true
---

## 常用类型

### 1. any

### 2. unknown
* unknown可以理解为一个类型安全的any，适用于：不确定数据的具体类型
```typescript
let a: unknown

// 以下对a的赋值，均正常
a = 100;
a = false

let x: string;
x = a // 警告：不能将类型"unknown"分配给类型"string"
```
* unknown强制在使用之前进行类型检查，从而提供更强的类型安全性
```typescript
let a: unknown;
let x: string;

a = 'hello';

// 第一种方式，加类型判断
if (typeof a === 'string') {
    x = a;
}

// 第二种方式，加断言
x = a as string;
```

### 3. never

### 4. void

### 5. object
#### 声明对象类型
* 索引签名
允许定义对象可以具有任意数量的属性，这些属性的键和类型是可变的，常用于：描述类型不确定的属性，(具有动态属性的对象)。
```typescript
let person: {
    name: string, // , 或者 ; 都可以
    age: number,
    [key: string]: unknow
}
```

#### 声明函数类型
```typescript
let count: (a: number, b: number) => number // =>在函数类型声明时表示函数类型，描述其参数类型和返回类型

count = (x, y) => {
    return x + y;
}
```

### 6. tuple
元组是一种特殊的数组类型，可以存储固定数量的元素，并且每个元素的类型是已知的且可以不同。元组用于精确描述一组值的类型。
```typescript
let arr1 = [string, number];
let arr2 = [number, boolean?];
let arr3 = [number, ...string[]]; // 第一个元素是number类型，后面可以是任意数量的string类型
arr3 = [100]
arr3 = [100, 'hello', 'world']
```

### 7. enum
枚举可以定义一组命名常量，它能增强代码的可读性，也让代码更好维护。
#### 数字枚举
数字枚举是一种最常见的枚举类型，其成员的值会自动递增，且数字枚举还具有反向映射的特点。
```typescript
enum Direction {
    Up,
    Down,
    Left,
    Right
}

console.log(Direction.Up) // 输出：0
console.log(Direction[0]) // 反向映射，输出: Up
```

#### 字符串枚举
枚举成员的值是字符串，没有反向映射
```typescript
enum Direction {
    Up = 'up',
    Down = 'down',
    Left = 'left',
    Right = 'right'
}

let direction: Direction = Direction.Up;
console.log(direction) // 输出：'up'
```

#### 常量枚举
常量枚举是一种特殊枚举类型，使用const关键字定义，在编译时可以减少生成的javascript代码量，并提高运行时性能。
```typescript
const enum Direction {
    Up,
    Down,
    Left,
    Right
}

let x = Direction.Up;
```
编译后生成的javascript代码量较小：
```javascript
"use strict";
let x = 0 /* Direction.Up */
```

### 8. type
type可以为任意类型创建别名，让代码更简洁、可读性更强，同时能更方便地进行类型复用和扩展。

#### 联合类型
联合类型是一种高级类型，它表示一个值可以是几种不同类型之一。
```typescript
type Status = number | string;
```

#### 交叉类型
```typescript
type A = {
    width: number
}

type B = {
    height: number
}

type C = A & B;
```

### 9. 一个特殊情况
在函数定义时，限制函数返回值为void，那么函数的返回值就必须是空。
```typescript
function demo(): void {
    // 返回undefined合法
    return undefined;

    // 以下返回不合法
    return 100;
    return null;
}
```
但是，如果使用类型声明限制函数返回值为void时，typescript并不会严格要求函数返回空。
```typescript
type logFunc = () => void;

const f1: logFunc = () => {
    return 100; // 允许返回非空值
}

const f2: logFunc = () => 200; // 允许返回非空值

console.log(f1); // 输出：100

if(f1) {} // 报错，由于定义是void，所以只能使用，不能用于进一步的判断
```

## 类
覆写父类的方法时候，最好加上override
```typescript
override run() {}
```

### 抽象类
抽象类不能实例化，其意义是可以被继承，抽象类里可以有普通方法，也可以有抽象方法。

总结：何时使用抽象类？
1. 定义通用接口： 为一组相关的类定义通用的行为(方法或属性)时。
2. 提供基础实现： 在抽象类中提供某些方法或为其提供基础实现，这样派生类就可以继承这些实现。
3. 确保关键实现： 强制派生类实现一些关键行为。
4. 共享代码和逻辑： 当多个类需要共享部分代码时，抽象类可以避免代码重复。

```typescript
abstract class Package {
    constructor(public weight: number) {}
    // 抽象方法
    abstract calculate(): number
    // 普通方法
    pringPackage() {
        console.log(`weight: ${this.weight}, price: ${this.calculate()}`)
    }
}

class StandardPackage extends Package {
    constructor(weight: number, public unitPrice: number) {
        super(weight)
    }
    calculate(): number {
        return this.weight * this.unitPrice;
    }
}

const s1 = new StandardPackage(7, 5)
s1.pringPackage();

class ExpressPackage extends Package {
    constructor(weight: number, public unitPrice: number, public additional: number) {
        super(weight)
    }
    calculate(): number {
        return this.weight * this.unitPrice + this.additional;
    }
}

const e1 = new ExpressPackage(12, 8, 2)
e1.pringPackage();
```

### 接口
interface是定义结构的一种方式，不能包含任何实现。
总结：何时使用接口？
1. 定义对象的格式： 描述数据模型、API响应格式、配置对象等。
2. 类的契约： 规定一个类需要实现哪些属性和方法。
3. 自动合并： 一般用于扩展第三方库的类型

#### 定义类结构
```typescript
interface IPerson {
    name: string,
    age: number,
    speak(n: number): void
}

class Person implements IPerson {
    constructor(public name: string, public age: number) {}
    speak(n: number): void {}
} 
```

#### 定义对象结构
```typescript
interface IUser {
    name: string,
    readonly role: string,
    run: (n: number) => void
}
const user: IUser = {
    name: 'Tom',
    role: 'admin',
    run(n) {}
}
```

#### 定义函数结构
```typescript
interface CountInterface {
    (a: number, b: number): number
}
const count: CountInterface = (x, y) => {
    return x + y;
}; 
```

#### 接口之间的继承
```typescript
interface IPerson {
    name: string,
    age: number
}

interface IStudent extends IPerson{
    grade: string
}

const student: IStudent = {
    name: 'a',
    age: 18,
    grade: "first grade"
}
```

#### 接口自动合并（可重复定义）
```typescript
interface IPerson {
    name: string,
    age: number
}
interface IPerson {
    grade: string
}

const person: IPerson = {
    name: 'a',
    age: 18,
    grade: "first grade"
}
```

### interface 与 type 的区别
相同点：都可以用于定义对象结构
不同点：
1. interface: 更专注于定义对象和类的结构，支持继承、合并
2. type: 可以定义类型别名、联合类型、交叉类型，但不支持继承和自动合并

### interface 与 抽象类 的区别
相同点：都用于定义一个类的格式
不同点：
1. 接口：只能描述结构，不能有任何实现，一个类可以实现多个接口
2. 抽象类： 既可以包含抽象方法，也可以包含具体方法，一个类只能继承一个抽象类

## 泛型
泛型允许在定义函数、类或接口时，使用类型参数来表示未指定的类型，这些参数在具体使用时，才被指定具体的类型，泛型能让同一段代码适用于多种类型，同时仍然保持类型的安全性。

### 泛型函数
```typescript
function logData<T, U>(data1: T, data2: U): T | U {
    return Date.now() % 2 ? data1 : data2;
}

logData<number, boolean>(100, false);
logData<string, number>('100', 100);
```

### 泛型接口
```typescript
interface IPerson<T> {
    name: string,
    age: number,
    extraInfo: T
}
const p1: IPerson<number> = {
    name: 'Tom',
    age: 18,
    extraInfo: 100
}
const p2: IPerson<string> = {
    name: 'Will',
    age: 21,
    extraInfo: 'male'
}
type JobInfo = {
    title: string,
    company: string
}
const p3: IPerson<JobInfo> = {
    name: 'Will',
    age: 21,
    extraInfo: {
        title: 'developer',
        company: 'x'
    }
}
```

### 泛型类
```typescript
class Person<T> {
    constructor(public name: string, public extraInfo: T) {}
}

const person = new Person<number>('Tom', 18)
```

## 类型声明文件
类型声明文件是TypeScript中的一种特殊文件，通常以.d.ts作为扩展名。它的主要作用是为现有的JavaScript代码提供类型信息，使得TypeScript能够在使用这些JavaScript库或模块时进行类型检查和提示。
```javascript
// demo.js
export function add(a, b) {
    return a + b;
}
export function mul(a, b) {
    return a * b;
}
```

```typescript
// demo.d.ts
declare function add(a: number, b:number): number;
declare function mul(a: number, b:number): number;
export {add, mul};
```