
# ES6总结系列之 Symbol 篇
---
ES6 引入了一种**新的原始数据类型**`Symbol`，表示**独一无二**的值。可以从根本上防止**属性名**的冲突，`Symbol` 值通过**Symbol函数**生成

```javascript
let s = Symbol();

typeof s
// "symbol"
```
#### 1.`Symbol`是第七种数据类型，前六种为：`underfined`, `null`,`String`, `Boolean`, `Number`,  `Object`


#### 2.`Symbol`函数前不能使用`new`命令，否则会报错。这是因为生成的 `Symbol` 是一个原始类型的值，不是对象。


#### 3.`Symbol`函数可以接受一个字符串作为参数，表示对 `Symbol` 实例的描述，主要是为了在控制台显示，或者转为字符串时，比较容易区分。
```javascript
let s1 = Symbol('foo');
let s2 = Symbol('bar');

s1 // Symbol(foo)
s2 // Symbol(bar)

s1.toString() // "Symbol(foo)"
s2.toString() // "Symbol(bar)"
```


#### 4.Symbol参数只是描述，相同参数返回的值是不相等的
```javascript
// 没有参数的情况
let s1 = Symbol();
let s2 = Symbol();

s1 === s2 // false

// 有参数的情况
let s1 = Symbol('foo');
let s2 = Symbol('foo');

s1 === s2 // false
```


#### 5.`Symbol` 值不能与其他类型的值进行运算，会报错。
```javascript
let sym = Symbol('My symbol');

"your symbol is " + sym
// TypeError: can't convert symbol to string
```


#### 6.`Symbol` 的参数如果是一个对象，就会调用该对象的toString方法，将其转为字符串，然后才生成一个 Symbol 值。


```javascript
const obj = {
  toString() {
    return 'abc';
  }
};
const sym = Symbol(obj);
sym // Symbol(abc)
```


#### 7.`Symbol` 值可以显式转为字符串，也可以转为布尔值，但是不能转为数值。
```javascript
let sym = Symbol('My symbol');

String(sym) // 'Symbol(My symbol)'
sym.toString() // 'Symbol(My symbol)'
```
```javascript
let sym = Symbol();
Boolean(sym) // true
!sym  // false

if (sym) {
  // ...
}
```
```javascript

Number(sym) // TypeError
sym + 2 // TypeError
```


#### 8.读取描述一般要将symbol转化为字符串，ES2019提供了一个实例属性description，直接返回Symbol 的描述

```javascript

const sym = Symbol('foo');

sym.description // "foo"
```


#### 9.Symbol 值作为对象属性名时，不能用点运算符。
```javascript
const mySymbol = Symbol();
const a = {};

a.mySymbol = 'Hello!';
a[mySymbol] // undefined
a['mySymbol'] // "Hello!"
```
因为**点运算符后面总是字符串**，所以不会读取mySymbol作为标识名所指代的那个值，导致a的属性名实际上**是一个字符串**，而**不是一个 Symbol 值。**


#### 10.同理，对象的内部，使用 Symbol 值定义属性时，Symbol 值必须放在方括号之中。
```javascript
let s = Symbol();

let obj = {
  [s]: function (arg) { ... }
};

obj[s](123);
```


#### 11.Symbol 值作为属性名时，该属性还是公开属性，不是私有属性。


#### 12.`Symbol` 作为属性名，遍历对象的时候，该属性不会出现在`for...in`、`for...of`循环中，也不会被`Object.keys()`、`Object.getOwnPropertyNames()`、`JSON.stringify()`返回。有一个`Object.getOwnPropertySymbols()`方法，可以获取指定对象的所有 Symbol 属性名。该方法返回一个数组，成员是当前对象的所有用作属性名的 Symbol 值。
```javascript
const obj = {};
let a = Symbol('a');
let b = Symbol('b');

obj[a] = 'Hello';
obj[b] = 'World';

const objectSymbols = Object.getOwnPropertySymbols(obj);

objectSymbols
// [Symbol(a), Symbol(b)]
```


#### 13.有时，我们希望重新使用同一个 ``Symbol`` 值，`Symbol.for()`方法可以做到这一点。它接受一个字符串作为参数，然后搜索有没有以该参数作为名称的 `Symbol` 值。如果有，就返回这个 `Symbol` 值，否则就新建一个以该字符串为名称的 `Symbol` 值，并将其注册到全局。

```javascript
let s1 = Symbol.for('foo');
let s2 = Symbol.for('foo');

s1 === s2 // true
```

#### 14.Symbol.keyFor()方法返回一个*已登记* 的 Symbol 类型值的key。
```javascript
let s1 = Symbol.for("foo");
Symbol.keyFor(s1) // "foo"

let s2 = Symbol("foo");
Symbol.keyFor(s2) // undefined
```
`Symbol()`写法**没有**登记机制
比如，如果你调用`Symbol.for("cat")`30 次，每次都会返回**同一个** `Symbol` 值,但是调用`Symbol("cat")`30 次，会返回 **30 个不同**的 `Symbol` 值。


## 总结要点
1. `Symbol`是第七种数据类型
2. `Symbol`函数前**不能**使用`new`命令
3. `Symbol`函数接收的参数，主要是为了在**控制台**或**转化为字符串**时容易区分
4. `Symbol`参数**只是描述**，**相同**参数返回的值是**不相等**的
5. `Symbol` 值**不能**与其他类型的值进行运算，会报错
6. 对象作为`Symbol`参数时，会**先**调用该对象的`toString`**转为字符串**
7. `Symbol` 值可以**显式**转为字符串，也可以转为布尔值，但是**不能**转为数值。
8. 读取描述一般要将`symbol`**转化为字符串**，ES2019提供了一个**实例属性**description，直接返回Symbol 的描述
9. **读取**或**定义**一个`Symbol`值为**属性名**时，要用`中括号[]`，不能用`点.`
10. `Symbol(描述)`**没有**登记机制，`Symbol.for(描述)`**有**登记机制 
11. 可用`Object.getOwnPropertySymbols()`方法，获取指定对象的所有 `Symbol` 属性名。

___
- [个人Github]("https://github.com/YoursJoker" "欢迎stars")，欢迎star\^_^
- ES6总结系列参考自阮一峰《ECMAScript6入门》

