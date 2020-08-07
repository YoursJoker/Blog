
# ES6总结系列之 Promise对象 篇
---
## 1. 含义
### 何为 Promise?
`Promise` 是异步编程的一种解决方案
`Promise` 简单说就是一个容器，里面保存着某个未来才会结束的事件（通常是一个异步操作）的结果
### Promise 两个特点:
①对象的**状态不受外界影响**，只有异步操作的结果，可以决定当前是那种状态pending(进行中), fulfilled(已成功), rejected(已失败)
②状态**只改变一次**，状态转变只有两种可能，改变后称resolved(定型)
1)pending --> fulfilled
2)pending --> rejected
### Promise 三个缺点:
①无法取消Promise, 一旦新建它就会立即执行，**无法中途取消**
②如果不设置回调函数，Promise内部抛出的错误，不会反应到外部
③当处于pending状态时，无法得知**目前进展**到**哪一个阶段**（刚刚开始还是即将完成）


## 2. 基本用法
`Promise` 对象是一个**构造函数**，用来生成 `Promise` 实例
```javascript
const promise = new Promise(function(resolve, reject) {
  // ... some code
  if (/* 异步操作成功 */){
    resolve(value);
  } else {
    reject(error);
  }});
```
1. 创建实例
①`resolve`, `reject `是**两个函数**，有JavaScript引擎提供
②`resolve`作用，将Promise状态从“未完成”变为“成功”，即从pending转为resolved
③`reject `作用，从pending转为rejected
④`resolve`,`reject` 都可**向外传出参数**



2. 实例生成后，可以用then方法分别指定resolved状态和rejected状态的回调函数，第二个参数**可不传**
```javascript
promise.then(function(value) {//value为resolve(value)中传出的
  // success}, 
function(error) {//error为reject(error)中传出的
  // failure});
```

## 3. Promise.prototype.then()
`then` 方法**返回**的是一个新的Promise**实例**，可**链式调用**，并将**return的返回值**作**为参数**传入第二个回调函数
```javascript
getJSON("/posts.json").then(function(json) {
  return json.post;
}).then(function(post) {
  // ...
});
```

## 4. Promise.prototype.catch()
catch方法是.then(null, rejection)或.then(undefined, rejection)的别名，用于**指定发生错误时的回调函数**。
一般来说，**不要在then()方法里面定义 Reject 状态的回调函数**（即then的第二个参数），总是使用catch方法。**理由是**第二种写法可以捕获前面then方法执行中的错误，也更接近同步的写法（try/catch）
```javascript
// badpromise
  .then(function(data) {
    // success  
  }, function(err) {
    // error  
  });
  
// goodpromise
  .then(function(data) {
     //cb    // success  
  })	
  .catch(function(err) {
    // error  
  });
```

## 5. Promise.prototype.finally()
finally()方法用于指定**不管** Promise 对象最后**状态如何**，**都会执行**的操作。该方法是 ES2018 引入标准的。
```javascript
promise
.then(result => {···})
.catch(error => {···})
.finally(() => {···});
```
___
- [个人Github]("https://github.com/YoursJoker" "欢迎stars")，欢迎star\^_^
- ES6总结系列参考自阮一峰《ECMAScript6入门》


