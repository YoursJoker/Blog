---
layout: set
title: Set和Map数据结构
date: 2020-08-06 10:28:35
tags: "ES6入门"
categories: "ES6入门"
---
# ES6总结系列之 Set和Map数据结构 篇
---
## 1. Set
### 基本用法
1)ES6 提供了新的数据结构 `Set`。它**类似**于数组，但是成员的值都是**唯一的**，**没有重复**的值。
2)`Set`函数可以接受一个**数组**（或者具有 `iterable` **接口**的**其他数据结构**）作为参数，用来初始化。
`Set`本身是一个**构造函数**，用来生成 `Set` 数据结构。
```javascript
// 例一
const set = new Set([1, 2, 3, 4, 4]);
[...set]
// [1, 2, 3, 4]

// 例二
const items = new Set([1, 2, 3, 4, 5, 5, 5, 5]);
items.size // 5

// 例三
const set = new Set(document.querySelectorAll('div'));
set.size // 56

// 类似于
const set = new Set();
document
 .querySelectorAll('div')
 .forEach(div => set.add(div));
set.size // 56
// "symbol"
```
3)采用的算法是同值比较（Same-value-zero equality），类似于精确相等运算符`===`,主要的区别是向 `Set` 加入值时认为`NaN`等于自身，而精确相等运算符认为`NaN`不等于自身。
```javascript
let set = new Set();
let a = NaN;
let b = NaN;
set.add(a);
set.add(b);
set // Set {NaN}

NaN === NaN //false
```
4)Array.from方法可以将 Set 结构转为数组。（数组去重）
```javascript
const items = new Set([1, 2, 3, 4, 5]);
const array = Array.from(items);

function dedupe(arr) {
  return Array.from(new Set(arr))
}
dedupe([1,1,2,3])//[1,2,3]
```

### 实例属性：
1)`Set.prototype.constructor`：构造函数，默认就是Set函数。
2)`Set.prototype.size`：返回Set实例的成员总数。 
### 实例方法：
1)`Set.prototype.add(value)`：返回 Set 结构本身。 
2)`Set.prototype.delete(value)`：返回布尔值
3)`Set.prototype.has(value)`：返回布尔值
4)`Set.prototype.clear()`：清除所有成员，没有返回值。
### 遍历方法：
**遍历顺序就是插入顺序**
1)`Set.prototype.keys()`：返回键名
2)`Set.prototype.values()`：返回键值
3)`Set.prototype.entries()`：返回键值对
4)`Set.prototype.forEach()`：使用回调函数遍历每个成员，第二个参数，表示绑定回调函数内部的this对象
5)`Set` 结构没有键名，只有键值（或者说键名和键值是同一个值），所以keys方法和values方法的**行为完全一致**。
```javascript
let set = new Set(['red', 'green', 'blue']);

for (let item of set.keys()) {
  console.log(item);
}
// red
// green
// blue

for (let item of set.values()) {
  console.log(item);
}
// red
// green
// blue

for (let item of set.entries()) {
  console.log(item);
}
// ["red", "red"]
// ["green", "green"]
// ["blue", "blue"]
```
6)`Set` 结构的实例默认可遍历，它的默认遍历器生成函数就是它的values方法。
```javascript
Set.prototype[Symbol.iterator] === Set.prototype.values
// true

这意味着，可以省略values方法，直接用for...of循环遍历 Set。
let set = new Set(['red', 'green', 'blue']);
for (let x of set) {
  console.log(x);
}
// red
// green
// blue
```
7)扩展运算符（...）内部使用for...of循环，所以也可以用于 Set 结构
```javascript
const set = new Set([1, 2, 3, 4, 4]);
[...set]// [1, 2, 3, 4]
```
## 2. WeakSet
### 基本用法
1)`WeakSet` 结构与 `Set` 类似，也是**不重复的值**的集合
2)`Set`函数可以接受一个**数组**（或者具有 `iterable` **接口**的**其他数据结构**）作为参数，用来初始化。
3)它与 `Set` 有两个区别:
① `WeakSet` 的**成员**只能是**对象**，而**不能是其他类型**的值
```javascript
const ws = new WeakSet();
ws.add(1)
// TypeError: Invalid value used in weak set
ws.add(Symbol())
// TypeError: invalid value used in weak set
```
② ``WeakSet`` 中的对象都是**弱引用**，即垃圾回收机制不考虑 `WeakSet` 对该对象的引用，也就是说，如果其他对象都不再引用该对象，那么垃圾回收机制会自动回收该对象所占用的内存，不考虑该对象还存在于 `WeakSet` 之中。
`WeakSet` 的成员是不适合引用的，因为它**会随时消失**。另外，由于 `WeakSet` 内部有多少个成员，**取决于**垃圾回收机制有没有运行，运行前后很**可能**成员个数是**不一样**的，而垃圾回收机制何时运行是不可预测的，因此 ES6 规定 `WeakSet` **不可遍历**。

### 实例方法
1)WeakSet.prototype.add(value)
2)WeakSet.prototype.delete(value) 
3)WeakSet.prototype.has(value)
4)没有clear,size,forEach
5)`WeakSet` 不能遍历，是因为成员都是弱引用，随时可能消失，遍历机制无法保证成员的存在，很可能刚刚遍历结束，成员就取不到了。WeakSet 的一个用处，是储存 DOM 节点，而不用担心这些节点从文档移除时，会引发内存泄漏。

## 3. Map
### 基本用法
1)`Map` 数据结构。它**类似**于对象，也是**键值对的集合**，但是“键”的范围**不限于字符串**，各种类型的值（包括对象）都可以当作键,也就是说`Object` 结构提供了“**字符串—值**”的对应，`Map` 结构提供了“**值—值**”的对应，是一种更完善的 `Hash` 结构实现
2)**数组**和任何**具有 Iterator 接口**、且每个**成员都是一个双元素**的数组的数据结构（详见《Iterator》一章）都可以当作Map构造函数的**参数**
```javascript
const map = new Map([
  ['name', '张三'],
  ['title', 'Author']
]); 

map.size // 2
map.has('name') // true
map.get('name') // "张三"
map.has('title') // true
map.get('title') // "Author"

const set = new Set([
  ['foo', 1],
  ['bar', 2]
]);
const m1 = new Map(set);
m1.get('foo') // 1

const m2 = new Map([['baz', 3]]);
const m3 = new Map(m2);
m3.get('baz') // 3
```
3)如果对同一个键多次赋值，后面的值将覆盖前面的值
```javascript
const map = new Map();

map
.set(1, 'aaa')
.set(1, 'bbb');

map.get(1) // "bbb"
```
4)`Map` 的**键**实际上是跟**内存地址绑定**的，只要**内存地址不一样**，就**视为两个键**
```javascript
const map = new Map();

map.set(['a'], 555);
map.get(['a']) // undefined
//只有对同一个对象的引用，Map 结构才将其视为同一个键

const k1 = ['a'];
const k2 = ['a'];

map
.set(k1, 111)
.set(k2, 222);

map.get(k1) // 111
map.get(k2) // 222
```
5)``Map`` 的键是一个简单类型的值（数字、字符串、布尔值），则只要两个值**严格相等**，`Map` 将其视为一个键，虽然(NaN===NaN //false)，但 `Map` 将其视为同一个键。
```javascript
let map = new Map();

map.set(-0, 123);
map.get(+0) // 123

map.set(true, 1);
map.set('true', 2);
map.get(true) // 1

map.set(undefined, 3);
map.set(null, 4);
map.get(undefined) // 3

map.set(NaN, 123);
map.get(NaN) // 123
```
### 实例属性
1)`Map.prototype.constructor`：构造函数，默认就是 `Map` 函数
2)`Map.prototype.size`：返回 `Map` 实例的成员总数

### 实例方法
1)Map.prototype.set(key, value)    
		返回当前Map对象
		对同一个键多次赋值，后面的值将覆盖前面的值
2)Map.prototype.get(key)   
		返回对应key的value，没有就返回undefined
3)Map.prototype.has(key) 
		true/false
4)Map.prototype.delete(key)
		true/false
5)Map.prototype.clear()
		没有返回值
### 遍历方法：
**遍历顺序就是插入顺序**
1)`Map.prototype.keys()`：返回**键名**的遍历器。
2)`Map.prototype.values()`：返回**键值**的遍历器。
3)`Map.prototype.entries()`：返回所有成员的遍历器。
4)`Map.prototype.forEach()`：遍历 `Map` 的所有成员。
5)`Map` 结构的默认遍历器接口（Symbol.iterator属性），就是entries方法。
```javascript
const map = new Map([
  ['F', 'no'],
  ['T',  'yes'],
]);

for (let key of map.keys()) {
  console.log(key);
}
// "F"
// "T"

for (let value of map.values()) {
  console.log(value);
}
// "no"
// "yes"

for (let item of map.entries()) {
  console.log(item[0], item[1]);
}
// "F" "no"
// "T" "yes"

// 或者
for (let [key, value] of map.entries()) {
  console.log(key, value);
}
// "F" "no"
// "T" "yes"

// 等同于使用map.entries()
for (let [key, value] of map) {
  console.log(key, value);
}
// "F" "no"
// "T" "yes"
```
### `Map` 数据结构转换
1)Map --> arr
a.扩展运算符
2)arr --> Map
a.arr直接传入Map   new Map(arr)
3)Map --> obj
a.创建一个空对象，for...of 遍历循环Map，逐一赋值
4)obj --> Map
a.for( let key of Object.keys(obj) ) { map.set(k,obj[k])}
b.new Map() 构造函数接受一个可迭代的entries。借助Object.entries方法你可以很容易的将Object转换为Map:
var obj = { foo: "bar", baz: 42 }; 
var map = new Map(Object.entries(obj));
console.log(map); 
// Map { foo: "bar", baz: 42 }
5)Map --> JSON
a.所有键名为字符串，可以转为对象在用JSON.stringify();
b.有键名不是字符串，可先转化为数组在用JSON.stringify();
6)JSON --> Map
a.所有键名为字符串，JSON.parse()转为对象后-->Map
JSON是一个数组，每个成员都是有两个成员的数组
new Map（JSON.parse(JSONstr)）

## 4.WeakMap
1)WeakMap结构与Map结构类似，也是用于生成键值对的集合。
2)与Map区别
a.WeakMap只接受**对象**作为键名(**null除外**)
b.键名所指向的对象，为**弱引用**，键值为正常引用
c.没有遍历操作，没有size属性	
d.只有四个方法：get(), set(), has(), delete()
___
- [个人Github]("https://github.com/YoursJoker" "欢迎stars")，欢迎star\^_^
- ES6总结系列参考自阮一峰《ECMAScript6入门》

