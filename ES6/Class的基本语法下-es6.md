# ES6总结系列之 Class（下） 篇
这篇文章我们来继续来聊聊class的基础用法，没看过上篇的小伙伴可以先看看Class的基本语法上(es6)，再来看这篇比较容易入手。

## 1. 静态方法

静态方法即在类的方法前加上`static`关键字

```javascript
class Foo {
  static classMethod() {
    return 'hello';
  }
}

Foo.classMethod() // 'hello'

var foo = new Foo();
foo.classMethod()
// TypeError: foo.classMethod is not a function
```

#### 静态方法的注意要点
1. 静态方法**不会被实例继承**
2. **直接通过类来调用**，**不能**通过类的实例来调用
3. 静态函数里的 `this` **指向类**

```javascript
class Foo {
  static bar() {
    this.baz();
  }
  static baz() {
    console.log('hello');
  }
  baz() {
    console.log('world');
  }
}

Foo.bar() // hello
```

4. 父类的静态方法，**可以**被子类**继承**

```javascript
class Foo {
  static classMethod() {
    return 'hello';
  }
}

class Bar extends Foo {
}

Bar.classMethod() // 'hello'
```

## 2. 静态属性

静态属性指定义在 Class 本身的属性，即 Class.proName，而不是定义在实例对象(this)上

```javascript
// 1.直接在类上定义静态属性（类的外部）
class Foo {
  // ...
}
Foo.prop = 1;

// 在类的内部用关键字 static 定义（类的内部）
class Foo {
  static prop = 1;
}
```

## 3. 实例属性的新写法
实例属性除了可以定义在 `constructor()` 方法里面的 `this` 上之外，还可以**直接定义在类的最顶层**，其他的都不变

```javascript
//定义在 constructor() 方法里面的 this 上
class IncreasingCounter {
  constructor () {
    this.count = 0;
  }

  getCount () {
    console.log(this.count)
  }
  add () {
    this.count++
  }
}

//直接定义在类的最顶层
class IncreasingCounter {
  count = 1;

  getCount () {
    console.log(this.count)
  }
  add () {
    this.count++
  }
}
```
## 4. 私有方法和私有属性
在属性名和方法名前，使用 `#` 表示


#### 私有方法和私有属性注意要点
1. 私有方法和私有属性,只能在类的内部使用，外部不能使用
```javascript
class IncreasingCounter {
  #count = 0;
  get value() {
    console.log('Getting the current value!');
    return this.#count;
  }
  increment() {
    this.#count++;
  }
}

const counter = new IncreasingCounter();
counter.value //Getting the current value!   0
counter.#count // 报错
counter.#count = 42 // 报错
```

2. 井号 `#` 是属性名的一部分，使用时必须带 `#`(即 `#a` 和 `a` 为两个不同的属性)

```javascript
class foo {
  a = "公有属性a";
  #a = "私有属性#a";

  getValue () {
    console.log(this.a)
    console.log(this.#a)
  }
}

var f = new foo();
f.getValue() //公有属性a   私有属性#a
console.log(f.a) //公有属性a
console.log(f.#a) //报错
```

3. 在私有方法和私有属性前加上 `static` 表示为静态的私有方法和私有属性

## 5. new.target 属性
用于返回 `new` 作用的构造函数
1. **只能在函数内使用**，一般用在**构造函数内**
2. 构造函数不是通过 `new` 命令或 `Reflect.construct()` 调用的，`new.target` 会返回 `undefined`

这个属性可以用来确定构造函数是**怎么调用的**，确保构造函数只能通过 `new` 命令调用
```javascript
function Person(name) {
  if (new.target !== undefined) {
    this.name = name;
  } else {
    throw new Error('必须使用 new 命令生成实例');
  }
}

// 另一种写法
function Person(name) {
  if (new.target === Person) {
    this.name = name;
  } else {
    throw new Error('必须使用 new 命令生成实例');
  }
}

var person = new Person('张三'); // 正确
var notAPerson = Person.call(person, '张三');  // 报错
```

3. Class 内部调用new.target，返回当前 Class

4. 子类继承父类时，new.target会返回子类。

```javascript
class Rectangle {
  constructor(length, width) {
    console.log(new.target === Rectangle);
    // ...
  }
}

class Square extends Rectangle {
  constructor(length, width) {
    super(length, width);
  }
}

var obj = new Square(3); // 输出 false
```
## 总结