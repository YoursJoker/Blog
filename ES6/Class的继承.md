# ES6总结系列之 Class的继承 篇

ES6之前我们经常通过**修改原型链**的方法来实现继承，ES6的 Class 提供了更为便利、清晰的方法来实现继承，让我们来看下吧

## 1. 基本用法

通过 `extends` 关键字来实现继承

<!--more-->

```javascript
class Point { /* ... */ }

class ColorPoint extends Point {
  constructor(x, y, color) {
    super(x, y); // 调用父类的constructor(x, y)
    this.color = color;
  }

  toString() {
    return this.color + ' ' + super.toString(); // 调用父类的toString()
  }
}
```

## 2. 注意要点
1. 子类的构造函数 construtor 方法中**必须调用 super 方法**，否则会报错

```javascript
class Point { /* ... */ }

class ColorPoint extends Point {
  constructor() {
  }
}

let cp = new ColorPoint(); // ReferenceError
```

原因是子类自己的 this 对象，必须**先通过父类的构造函数完成构造**

2. 子类的构造函数中，只有**调用了 super 之后，才可以使用 this**，否则会报错

3. 父类的静态方法，也会被子类继承

```javascript
class A {
  static hello() {
    console.log('hello world');
  }
}

class B extends A {
}

B.hello()  // hello world
```

## 3. super 关键字

`super` 关键字既可以**当函数使用**，也可以当**做对象使用**

1. 当函数使用时，**代表父类的构造函数**，且只能**用在子类的构造函数**中，用在其他地方会报错

```javascript
class A {}

class B extends A {
  constructor() {
    super();
  }
}
```
2.  当函数使用时 super 内部的 this **指的是B的实例**

super 虽然代表了父类A的构造函数，但是**返回**的是**子类B的实例**

3. super 作为对象的时，指向**父类的原型对象**

```javascript
class A {
  constructor() {
    this.p = 2;
  }
}

class B extends A {
  get m() {
    return super.p;
  }
}

let b = new B();
b.m // undefined
```
由于 super 指向父类的原型对象，所以**取不到**定义在**父类实例上的方法或属性**


4. super作为对象，用在**静态方法之中**，这时**super将指向父类**，而不是父类的原型对象。

```javascript
class Parent {
  static myMethod(msg) {
    console.log('static', msg);
  }

  myMethod(msg) {
    console.log('instance', msg);
  }
}

class Child extends Parent {
  static myMethod(msg) {
    super.myMethod(msg);
  }

  myMethod(msg) {
    super.myMethod(msg);
  }
}

Child.myMethod(1); // static 1

var child = new Child();
child.myMethod(2); // instance 2
```

## 4.类的 prototype 属性和__proto__属性

1. 类的 `__proto__` 属性，表示构造函数的继承，总是指向父类

2. 子类的 `prototype` 属性的 `__proto__` 属性，表示方法的继承，总是指向父类的 `prototype`

```javascript
class A {
}

class B extends A {
}

B.__proto__ === A // true
B.prototype.__proto__ === A.prototype // true
```
![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/dae10321057e431ba8d9f297e0d69ab0~tplv-k3u1fbpfcp-zoom-1.image)

类的继承的实现模式

```javascript
class A {
}

class B {
}

// B 的实例继承 A 的实例
Object.setPrototypeOf(B.prototype, A.prototype);
// 等同于B.prototype.__proto__ = A.prototype;

// B 继承 A 的静态属性
Object.setPrototypeOf(B, A);
// 等同于B.__proto__ = A;

const b = new B();
```

## 总结

1. 子类的构造函数 construtor 方法中**必须调用 super 方法**，否则会报错
2. 子类的构造函数中，只有**调用了 super 之后，才可以使用 this**，否则会报错
3. 父类的静态方法，也会被子类继承

#### super 关键字
1.  super 作为**函数**时，**代表父类的构造函数**，且只能**用在子类的构造函数**中，用在其他地方会报错
2.  super 作为**函数**时， super 内部的 this **指的是B的实例**
3. super 作为**对象**时，指向**父类的原型对象**
4. super 作为**对象**时，用在**静态方法之中**，这时**super将指向父类**，而不是父类的原型对象。

#### 类的 prototype 属性和__proto__属性

1. 类的 `__proto__` 属性，表示构造函数的继承，总是指向父类

2. 子类的 `prototype` 属性的 `__proto__` 属性，表示方法的继承，总是指向父类的 `prototype`

___
- [个人Github]("https://github.com/YoursJoker" "欢迎stars")，欢迎star\^_^
- ES6总结系列参考自阮一峰《ECMAScript6入门》