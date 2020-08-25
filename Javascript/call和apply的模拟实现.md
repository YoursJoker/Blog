# javascript系列之 call和apply的模拟实现 篇
## call和apply异同
同： 改变函数`this`指向
异： 传参列表不同（`call`传参是逐个传递，`apply`传参是以数组的方式传递）
<!--more-->
## call
### 用法例子
```javascript
var bar = {
    age: 18
}
function foo(name, sex) {
    console.log(name)
    console.log(sex)
    console.log(this.age)
}
foo.call(bar, 'Joker', 'male');//Joker,male,18
```
注意要点：
1. `call`改变了`foo`中的`this`指向
2. 函数`foo`执行了

### call的模拟实现
1. 将函数添加为所绑定目标的属性
2. 执行该属性
3. 删除该属性
```javascript
Function.prototype.myCall = function (target) {
    target = target || window
    //this 参数可以传 null,undefined，当为 null和undefined 的时候，视为指向 window
    argsLen = arguments.length;
    var args = [];
    for(var i = 1; i < argsLen; i++)
        args.push('arguments[' + i + ']')
    args = args.join(',');
    target.fn = this;//将函数添加为所绑定目标的属性
    var result = eval('target.fn(' + args + ')')//执行该属性
    delete target.fn;//删除该属性
    return result;
}
foo.myCall(bar, 'Joker', 'male');//Joker,male,18
```
使用ES6的rest参数和扩展运算符更为简便
```javascript
Function.prototype.myCall = function (target, ...args) {
    target = target || window;
    target.fn = this;
    var result = target.fn(...args)
    delete target.fn;
    return result;
}
foo.myCall(bar, 'Joker', 'male');//Joker,male,18
```
## apply
### 用法例子
```javascript
var bar = {
    age: 18
}
function foo(name, sex) {
    console.log(name)
    console.log(sex)
    console.log(this.age)
}
foo.apply(bar, ['Joker', 'male']);//Joker,male,18
```
同理：
1. `apply`改变了`foo`中的`this`指向
2. 函数`foo`执行了

### apply的模拟实现
1. 将函数添加为所绑定目标的属性
2. 执行该属性
3. 删除该属性
```javascript
Function.prototype.myApply = function (target, argsArr) {
    target = target || window
    //this 参数可以传 null,undefined，当为 null和undefined 的时候，视为指向 window
    target.fn = this;//将函数添加为所绑定目标的属性
    var result;
    if(!argsArr)
        result = target.fn();
    else{
        argsLen = argsArr.length;
        var args = [];
        for(var i = 0; i < argsLen; i++)
            args.push('argsArr[' + i + ']')
        args = args.join(',');
        result = eval('target.fn(' + args + ')')//执行该属性
    }
    delete target.fn;//删除该属性
    return result;
}
foo.myApply(bar, ['Joker', 'male']);//Joker,male,18
```
使用ES6的扩展运算符更为简便
```javascript
Function.prototype.myApply = function (target, args) {
    target = target || window;
    target.fn = this;
    var result;
    if(!args)
        result = target.fn();
    else
        result = target.fn(...args);
    delete target.fn;
    return result;
}
foo.myApply(bar, ['Joker', 'male']);//Joker,male,18
```
> call和apply的部分实现参考自冴羽的博客