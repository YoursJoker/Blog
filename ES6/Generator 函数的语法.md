---
title: Generator 函数的语法
date: 2020-09-15 14:18:44
tags: 'ES6入门'
categories: 'ES6入门'
cover:
---

# ES6 总结系列之 Generator 函数的语法 篇

## 1. 何为 Generator

1. Generator 函数是 ES6 提供的一种**异步编程解决方案**，执行 Generator 函数的会**返回一个遍历器对象**，Generator 函数是一个状态机，封装了**多个内部状态**。
2. 与传统函数不同的是，传统函数一旦运行就不能停止，一次性执行完；而 Generator 函数类似于将函数分割成多个‘小函数’，可将一个函数实现分步执行

## 2. 基本用法

1. Generator 函数的 `function` 和**关键字**之间有一个`*`

ES6 没有规定，function 关键字与函数名之间的星号，写在哪个位置。这导致下面的写法都能通过。

```javascript
function * foo(x, y) { ··· }
function *foo(x, y) { ··· }
function* foo(x, y) { ··· }
function*foo(x, y) { ··· }
```

由于 Generator 函数仍然是**普通函数**，所以一般的写法是上面的第三种，即星号紧跟在 function 关键字后面。

2. 函数体内使用 `yield` 表达式，定义**不同的内部状态**(yield 英语意思“产出”)

```javascript
function* gen() {
  yield 1
  yield 2
  return 3
}

let iter = gen()
iter.next() //{value: 1, done: false}
iter.next() //{value: 2, done: false}
iter.next() //{value: 3, done: true}
```

调用 Generator 函数生成的遍历器对象，调用遍历器对象的`next` 方法，返回 `{value: xxx, done: xxx}` 其中 `value` 是 `yield` 后面表达式的值，`done` 是一个布尔值表示是否遍历结束

## 3. yield 表达式

1. 遇到 `yield` 表达式，就**暂停**执行后面的操作，并将 `yield` 后面表达式的值作为 `value` 值
2. 下一次调用 `next` 方法时，再继续往下执行，直到遇到下一个 `yield` 表达式。
3. 没有遇到 `yield` 就一直运行到函数结束，如果有 `return` ，就将人 `return` 后面的值作为 `value` 值，没有 `return` 语句 `value` 值就为 `undefined`

## 4. next 方法的参数

`yield` 表达式本身**没有返回值**( `undefined` )
`next` 可以带一个参数作为**上一个** `yield` 表达式的返回值，向 Generator 函数内部输入值

```javascript
function* dataConsumer() {
  console.log('Started')
  console.log(`1. ${yield}`)
  console.log(`2. ${yield}`)
  return 'result'
}

let genObj = dataConsumer()
genObj.next()
// Started
genObj.next('a')
// 1. a
genObj.next('b')
// 2. b
```

## 5. 与 Iterator 接口的联系

任意一个对象的 `Symbol.iterator` 方法，等于该对象的**遍历器生成函数**，调用该函数会**返回**该对象的一个**遍历器对象**
Generator 函数就是遍历器生成函数，因此可以把 Generator 赋值给对象的 `Symbol.iterator` 属性，从而使得该对象具有 `Iterator` 接口。

```javascript
let myIterable = {
  [Symbol.iterator]: function* () {
    yield 1
    yield 2
    yield 3
  },
}  [...myIterable] // [1, 2, 3]

// 或者采用下面的简洁写法

let obj = {
  *[Symbol.iterator]() {
    yield "hello"
    yield "world"
  },
}

for (let x of obj) {
  console.log(x)
}
// "hello"
// "world"
```

Generator 函数执行后，返回一个遍历器对象。**该对象本身**也具有 `Symbol.iterator` 属性，执行后**返回自身**。

```javascript
function* gen() {
  // some code
}

var g = gen()

g[Symbol.iterator]() === g
// true
```

## 6. for...of 循环

`for...of` 循环可以**自动遍历** Generator 函数运行时**生成**的 **Iterator 对象**

```javascript
function* foo() {
  yield 1
  yield 2
  yield 3
  yield 4
  yield 5
  return 6
}

for (let v of foo()) {
  console.log(v)
}
// 1 2 3 4 5
```

一旦 next() 返回的对象的 `done` 属性为 `true` 就终止循环，且不包括该 `value` ，如上面的 6 不在循环中

除了 `for...of` 循环外，扩展运算符（ `...` ），解构赋值和 `Array.from` 方法内部调用的都是遍历器接口。
即它们都可以将 Generator 函数返回的 **Iterator 对象**，作为对象

```javascript
function* numbers() {
  yield 1
  yield 2
  return 3
  yield 4
}

// 扩展运算符
;[...numbers()] // [1, 2]

// Array.from 方法
Array.from(numbers()) // [1, 2]

// 解构赋值
let [x, y] = numbers()
x // 1
y // 2

// for...of 循环
for (let n of numbers()) {
  console.log(n)
}
// 1
// 2
```

## 7. Generator.prototype.throw()

1. Generator 函数返回的对象都有一个 `throw` 方法，调用该方法可以在**函数体外**抛出错误，在 Generator **函数体内**被捕获

2. `throw` 方法可以接受一个参数，该参数会被 `catch` 语句接收，**建议抛出 Error 对象的实例**

3. 如果 Generator **内部没有部署** `try...catch` 代码块，那么 `throw` 方法抛出的错误，将被**外部**的 `try...catch` **捕获**

```javascript
var g = function* () {
  try {
    yield
  } catch (e) {
    console.log('内部捕获', e)
  }
}

var i = g()
i.next()

try {
  i.throw('a')
  i.throw('b')
} catch (e) {
  console.log('外部捕获', e)
}
// 内部捕获 a
// 外部捕获 b
```

第一个错误被 Generator 函数内部的 `try...catch` 捕获，第二个错误由于函数体内的 `try...catch` 执行过了，不会再捕获这个错误，所以被函数体外的 `try...catch` 捕获了

4. `throw` 方法抛出的错误要被内部捕获，前提是必须至少执行过一次 `next` 方法。

```javascript
function* gen() {
  try {
    yield 1
  } catch (e) {
    console.log('内部捕获')
  }
}

var g = gen()
g.throw(1)
// Uncaught 1
```

5. `throw` 方法被执行后，自动执行了一次 `next` 方法
6. Generator 函数执行过程中抛出错误，且**没有**被内部捕获，此时 JavaScript 引擎认为 Generator 函数已经**运行结束**了，如果伺候还调用 `next` 方法，将返回 `{value: undefined, done: true}`

## 8. Generator.prototype.return()

Generator 函数生成的遍历器对象的 `return` 方法，可以返回指定的值，并**终结**遍历函数

```javascript
function* gen() {
  yield 1
  yield 2
  yield 3
}

var g = gen()

g.next() // { value: 1, done: false }
g.return('foo') // { value: "foo", done: true }
g.next() // { value: undefined, done: true }
```

如果 Generator 函数内部有 `try...finally` 代码块，且正在执行 `try` 代码块，那么 `return` 方法会导致**立刻进入**`finally` 代码块，执行完以后，整个函数才会结束。

```javascript
function* numbers() {
  yield 1
  try {
    yield 2
    yield 3
  } finally {
    yield 4
    yield 5
  }
  yield 6
}
var g = numbers()
g.next() // { value: 1, done: false }
g.next() // { value: 2, done: false }
g.return(7) // { value: 4, done: false }
g.next() // { value: 5, done: false }
g.next() // { value: 7, done: true }
```

## 9. yield\* 表达式

yield\* 表达式用来在一个 Generator 函数里面执行另一个 Generator 函数。

```javascript
function* foo() {
  yield 'a'
  yield 'b'
}

function* bar() {
  yield 'x'
  yield* foo()
  yield 'y'
}

// 等同于
function* bar() {
  yield 'x'
  yield 'a'
  yield 'b'
  yield 'y'
}

// 等同于
function* bar() {
  yield 'x'
  for (let v of foo()) {
    yield v
  }
  yield 'y'
}

for (let v of bar()) {
  console.log(v)
}
// "x"
// "a"
// "b"
// "y"
```

任何数据结构只要有 Iterator 接口，就可以被 yield\* 遍历。

```javascript
function* bar() {
  yield 'hello'
  yield* 'Hi'
}

console.log([...bar()])
// ['hello', 'H', 'i']
```

## 10. 作为对象属性的 Generator 函数

```javascript
let obj = {
  myGeneratorMethod: function* () {
    ...
  }
}

// 等同于
let obj = {
  * myGeneratorMethod() {
    ...
  }
}
```

## 总结

1. 调用 next 方法返回的对象 `{value: xxx, done: bool}` 其中 `value` 即为 yield 表达式的值， `done` 表示是否遍历结束，`true` 即为结束
2. next 方法的参数传给**上一个** yield 表达式的返回值，从而实现**向** Generator 函数**内部输入值**
3. 内部调用的都是遍历器接口的 `for...of` 循环，扩展运算符（ `...` ），解构赋值和 `Array.from` 方法，可以自动遍历 Generator 函数生成的 Iterator 对象
4. Generator.prototype.throw 方法，在 Generator 函数体外部抛出错误，内部进行捕获（**外抛内捕**），且会**自动执行一次** `next` 方法
5. Generator.prototypel.return 方法可以**终止遍历**，但是如果 Generator 函数体内部有 `try...finally` 函数，会直接**跳入并执行** `finally` 代码块，**再终止**
6. yield\* 可以在 Generator 函数体**内部执行另一个** Generator 函数

---

- [个人 Github]("https://github.com/YoursJoker" '欢迎stars')，欢迎 star\^\_^
- ES6 总结系列参考自阮一峰《ECMAScript6 入门》
