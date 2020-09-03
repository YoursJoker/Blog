
# ES6总结系列之 class(上) 篇

## 1.何为class

1. ES6 的 `class` 完全可以看作构造函数的**另一种写法**，使用的时候，也是直接对类使用new命令，跟构造函数的用法完全一致
2. `class` 也可以看作只是一个**语法糖**，它的绝大部分功能，ES5 都可以做到，新的class写法只是让对象原型的写法更加清晰、更像面向对象编程的语法而已

## 2.基本用法

我们先来看一个例子

```javascript
class Point {
  constructor(x, y) {
    this.x = x;
    this.y = y;
  }

  toString() {
    return '(' + this.x + ', ' + this.y + ')';
  }
}

let p = new Point(1,2);
p.x; //1
p.y; //2
p.toString(); //(1, 2)
```

1. `this `关键字代表**实例对象**
2. `constructor`为构造方法, 通过 `new` 命令生成实例时自动被调用， 没有显式定义时一个空的 `constructor` 会被默认添加, `constructor` 方法默认返回实例对象（即this）
2. 定义类方法**不需要**关键字`function`
3. 方法之间**不需要逗号分隔**，加了会报错

4. 类的所有方法都定义在类的`prototype`属性上面

```javascript
class Point {
  constructor() {
    // ...
  }

  toString() {
    // ...
  }

  toValue() {
    // ...
  }
}

// 等同于

Point.prototype = {
  constructor() {},
  toString() {},
  toValue() {},
};
```

5. `prototype` 对象的 `constructor` 属性，直接指向“类”的本身，这与 ES5 的行为是一致的
6. 类的内部所有定义的**方法**，都是**不可枚举的**

```javascript
class Foo {
  constructor() {
    return Object.create(null);
  }
}

Foo()
// TypeError: Class constructor Foo cannot be invoked without 'new'
```

7. `class` **必须**使用 `new` 调用，否则会报错。这是它跟普通构造函数的一个主要区别，后者不用 new 也可以执行

## 注意点

1. 类和模块内部，**默认**就是**严格模式**
2. 类**不存在变量提升**

```javascript
new Foo(); // ReferenceError
class Foo {}
```

3. name 属性返回 class 关健字后面的类名

```javascript
class Point {}
Point.name // "Point"
```

4. 如果某个方法之前加上星号（*），就表示该方法是一个 Generator 函数
```javascript
class Foo {
  constructor(...args) {
    this.args = args;
  }
  * [Symbol.iterator]() {
    for (let arg of this.args) {
      yield arg;
    }
  }
}

for (let x of new Foo('hello', 'world')) {
  console.log(x);
}
// hello
// world
```

## 总结
### 基本用法
1. `this `关键字代表**实例对象**
2. `constructor`为构造方法, 通过 `new` 命令生成实例时自动被调用， 没有显式定义时一个空的 `constructor` 会被默认添加, `constructor` 方法默认返回实例对象（即this）
2. 定义类方法**不需要**关键字`function`
3. 方法之间**不需要逗号分隔**，加了会报错
4. 类的所有方法都定义在类的`prototype`属性上面
5. `prototype` 对象的 `constructor` 属性，直接指向“类”的本身，这与 ES5 的行为是一致的
6. 类的内部所有定义的**方法**，都是**不可枚举的**
7. `class` **必须**使用 `new` 调用，否则会报错。这是它跟普通构造函数的一个主要区别，后者不用 new 也可以执行
### 注意要点
1. 类和模块内部，**默认**就是**严格模式**
2. 类**不存在变量提升**
3. name 属性返回 class 关健字后面的**类名**
4. 如果某个方法之前加上星号（*），就表示该方法是一个 Generator 函数
___
欢迎star，持续更新ing...
ES6总结系列参考自阮一峰《ES6入门教程》