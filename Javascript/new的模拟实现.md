# javascript系列之 new的模拟实现 篇

> `new` 运算符创建一个**用户定义**的对象类型的实例或**具有构造函数的内置**对象的实例。

## 1. `new`的基本用法

使用 new 操作符后返回一个新对象，且该对象为对应构造函数的实例。该对象可以访问**构造函数**里的属性和**构造函数的原型**里的属性

#### 1. 构造函数无返回值时

```javascript
function Person (name, age) {
    this.name = name;
    this.age = age;
}
Person.prototype.sayName = function () {
    console.log("I am " + this.name);
}
var person = new Person("joker", 18);
console.log(person.name);//"joker"
console.log(person.age);//18
person.sayName();//"I am joker"
```

#### 2. 构造函数有返回值时

```javascript
//返回值为基本类型时
function Person (name, age) {
    this.name = name;
    this.age = age;
    return "asd";//返回值是基本类型
}
Person.prototype.sayName = function () {
    console.log("I am " + this.name);
}
var person = new Person("joker", 18);
console.log(person.name);//"joker"
console.log(person.age);//18
person.sayName();//"I am joker"

//返回值是一个对象时
function Person (name, age) {
    this.name = name;
    this.age = age;
    return {
        name: "retJoker",
        age: "ret18"
    }
}
Person.prototype.sayName = function () {
    console.log("I am " + this.name);
}
var person = new Person("joker", 18);
console.log(person.name);//"retjoker"
console.log(person.age);//"ret18"
person.sayName();//报错 “not a function"
```

1. 当返回值为基本类型时，**相当于没有返回值处理**
2. 当返回值为一个对象时，**只能访问返回对象的属性**，不能访问构造函数原型的属性

## 2. `new`的模拟实现

1. 创建新对象
2. 将新对象的原型指向构造函数的原型
3. 将构造函数中的this指向新对象，并根据参数设置新对象属性
4. 返回新对象

```javascript
function objectFactory () {
    var obj = new Object();//创建一个新对象
    Constructor = [].shift.call(arguments);//获取构造函数
    obj.__proto__ = Constructor.prototype;//将对象原型指向构造函数原型
    Constructor.apply(obj, arguments);//将构造函数中的this指向该对象，根据参数设置obj属性
    return obj;//返回对象
}
var person = objectFactory(Person, "joker", 18);
console.log(person.name);//"joker"
console.log(person.age);//18
person.sayName();//"I am joker"
```

5. 实现返回效果

```javascript
//返回效果实现
function objectFactory () {
    var obj = new Object();//创建一个新对象
    Constructor = [].shift.call(arguments);//获取构造函数
    obj.__proto__ = Constructor.prototype;//将对象原型指向构造函数
    var ret = Constructor.apply(obj, arguments);//取得构造函数的返回值
    return typeof ret == "object" ? ret : obj;//如果返回值是对象就返回该对象，否则返回构造函数的实例对象
}
```