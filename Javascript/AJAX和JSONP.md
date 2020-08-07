## 1.AJAX
### 含义
Asynchronous JavaScript and XML(AJAX)
异步的 JavaScript 和 XML
AJAX 是一种在**无需**重新加载**整个网页**的情况下，能够**更新部分网页**的技术。
### 基本用法
#### 1.创建 `XMLHttpRequest` 实例
```javascript
var xhr = new XMLHttpRequest()
var xhr = new ActiveXObject("Microsoft.XMLHTTP") //（IE5 和 IE6）使用 ActiveX 对象
```


#### 2.使用 `open` 方法，初始化请求参数
```javascript
xhr.open(method, url, async)
```
规定请求的类型、URL 以及是否异步处理请求。
- method：请求的类型；`GET` 或 `POST`
- url：文件在服务器上的位置
- async：true（异步）或 false（同步），默认为 true


#### 3.使用 `send` 方法，发送请求
```javascript
send(string)
```
将请求发送到服务器。
string：仅用于 `POST` 请求

#### 4.监听 `onreadystatechange` ，接收返回的数据
每当 `readyState` 改变时，就会触发 `onreadystatechange` 事件
`readyState`从 0 到 4 发生变化
- 0: **未初始化**，尚未调用 `open()` 方法
- 1: 启动，已调用 `open()` 方法，但未调用 `send()` 方法
- 2: 发送，已调用 `send()` 方法，但**未收到响应**
- 3: 接收，接收到**部分**响应数据
- 4: 完成，接收到**全部**响应数据，并可以使用

`status`
成功状态码
- 200：服务器**成功**返回内容
- 301：**临时**重定向
- 302：**永久**重定向
- 304：请求资源**未被修改**，可以**使用**浏览器**缓存数据**

失败状态码
- 404：请求内容不存在
- 500：服务器暂时不可用

### 封装AJAX
```javascript


function ajaxFunc (method, url, data, callback) {
    var xhr = null;
    if(window.XMLHttpRequest) {
        xhr = new XMLHttpRequest()
    }else{
        xhr = new ActiveXObject('microsoft.XMLHttp')
    }

    method = method.toUpperCase();//转为大写字母方便判断
    if(method === 'GET') {
        xhr.open('GET', url + '?' + data, true);//GET请求 传递数据是拼接在url后面的
        xhr.send();
    }else if(method === 'POST') {
        xhr.open('POST', url, true);
        xhr.setRequestHeader('Content-type', 'application/x-www-form-urlencoded');//POST请求还需设置请求头
        xhr.send(data)//POST请求 传递数据是放在send里
    }
    xhr.onreadystatechange = function() {
        if(xhr.readyState == 4 && xhr.status == 200) {
            callback(xhr.responseText)
        }
    }
}
```
### GET、POST区别
1. 方式：get是将参数直接添加到**url后面**，而post是将参数放在请求的**表单体**内
2. 大小：get传送的数据量较小，**不能大于2KB**。post传送的数据量较大，一般被默认为**不受限制**。
3. 安全性：get安全性**非常低**，post安全性**较高**

### 安全限制
遵循浏览器的**同源策略**（协议、域名、端口一致），**不能跨域**

## 2.JSONP
可以实现**跨域**
只能用 `GET` 请求，并且要求返回 `JavaScript`
页面中先准备好doJson()处理函数，然后给页面动态加一个 `<script>` 节点，相当于动态读取外域的JavaScript资源，最后就等着接收回调了。

```javascript
var oScript = document.createElement('script');
//动态添加<script>节点

oScript.src = 'https://xxxx' + '&cb = doJson'; 
//其中cb参数则告诉服务器，我的本地回调函数叫做doJson，所以请把查询结果传入这个函数中进行调用。

document.body.appendChild(oScript);
// 把script标签加入head，此时调用开始

function doJson(data) {
  //处理代码...
}
```

## 3.AJAX和JSONP异同
1. 同：都是请求一个url，将返回的数据进行处理
2. 异：ajax 遵循同源策略，**不能**实现跨域，而json**可以跨域**
3. 异：ajax 能用get和post请求，而**jsonp只能用get**
4. 异：ajax的核心是通过 `XMLHttpRequest` 获取非本页内容，而jsonp的核心则是**动态添加**
