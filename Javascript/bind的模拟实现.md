# javascript系列之 bind的模拟实现 篇

## 1. `bind`的基本用法

> `bind()` 方法创建一个新的函数，在 `bind()` 被调用时，这个新函数的 `this` 被指定为 `bind()` 的第一个参数，而其余参数将作为新函数的参数，供调用时使用。
--MDN

```javascript
//bind绑定 this 指向
var bar = {
    age: 18
}
function foo(name, sex) {
    console.log(name);
    console.log(sex);
    console.log(this.age)
}
var bindFoo = foo.bind(bar)
bindFoo('Joker', 'male');//"joker", "male", 18


//bind绑定参数
var bindFoo = foo.bind(bar, 'joker')
bindFoo();         //"joker", undefined, 18
bindFoo('male');   //"joker", "male", 18


//bind 返回的函数可以使用 new 操作符创建对象
//bind 时指定的 this 值会失效
//传入的参数依然生效
var bar = {
    age: 18
}
foo.prototype.color = 'red';
function foo(name, sex) {
    this.major = "internet"
    console.log(name);
    console.log(sex);
    console.log(this.age)
}
var bindFoo = foo.bind(bar, 'joker')
var newBindFoo = new bindFoo('male')//'joker', 'male', undefined
console.log(newBindFoo.color)//red
console.log(newBindFoo.major)//internet
```

## 2. `bind`的模拟实现

1. 合并参数
2. 判断是否作为构造函数
3. 修改this指向

```javascript
//第一版实现 合并参数， 修改this指向
Function.prototype.myBind = function (target) {
    var self = this;
    //存储调用bind的函数本身
    var args = Array.prototype.slice.call(arguments, 1);
    //将第一个参数除外转化为数组
    return function () {
        bindArgs = Array.prototype.slice.call(arguments);
        //将bind返回的函数的参数转为数组
        finalArgs = args.concat(bindArgs)
        //合并参数传递给原函数
        return self.apply(target, finalArgs);
        //apply修改this指向
    }
}
var bindFoo = foo.myBind(bar, 'joker');
bindFoo('male');//"joker", "male", 18
```

```javascript
//第二版实现构造函数功能(bind返回函数的new构造)
Function.prototype.myBind = function(target) {
    var self = this;
    var args = [].slice.call(arguments, 1);

    var fBound =  function () {
        var bindArgs = [].slice.call(arguments);
        var finalArgs = args.concat(bindArgs);
        return self.apply(this instanceof fBound ? this : self, finalArgs)
        //当为普通函数时，this指向window，this instanceof fBound 为false
        //当作为构造函数时，this为Object,为true,此时应指向新创建的实例
    }
    fBound.prototype = self.prototype;
    return fBound;
}
var bindFoo = foo.myBind(bar, 'joker')
var newBindFoo = new bindFoo('male')//'joker', 'male', undefined
console.log(newBindFoo.color)//red
console.log(newBindFoo.major)//internet

//第三版，第二版修改newBindFoo.prototype的值会改变原函数.prototype的值
//通过一个空函数来进行中转
Function.prototype.myBind = function(target) {
    var self = this;
    var args = [].slice.call(arguments, 1);

    var fBound =  function () {
        var bindArgs = [].slice.call(arguments);
        var finalArgs = args.concat(bindArgs);
        return self.apply(this instanceof fBound ? this : self, finalArgs)
    }
    function Empty () {};//使用空函数中转
    Empty.prototype = self.prototype;
    fBound.prototype = new Empty();
    return fBound;
}
var bindFoo = foo.myBind(bar, 'joker')
var newBindFoo = new bindFoo('male')//'joker', 'male', undefined
console.log(newBindFoo.color)//red
console.log(newBindFoo.major)//internet

```
