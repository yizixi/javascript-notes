### Ajax 总结

- AJAX = Asynchronous JavaScript and XML（异步的 JavaScript 和 XML）。

#### 先看一个基于 jQuery 的 ajax 示例，往下看，后边有原生写法。

```js
$.ajax({
    url: "http://request.url",
    data: {key:value},
    type: "post",
    datatype: "text",
    cache: true,
    async: true,
    data: "",
    timeout: 15000,
    beforeSend: function(){},
    success: function(data){},
    error: function(){},
    complete: function(){}
});
```

- Hint: 最简单的情况下，$.ajax() 可以不带任何参数直接使用。

------

#### 各属性解释
> - url `[String]`: 请求路径；

> - async `[Boolean]`: 默认为 true，代表异步。false 代表同步。 

> - timeout `[Number]`: 设置请求超时时间（毫秒）。此设置将覆盖全局设置。

> - type `[String]`: 默认值 'get', 请求方式（'post' 或 'get'）。注意：其它 HTTP 请求方法，如 PUT 和 DELETE 也可以使用，但仅部分浏览器支持。

> - chache `[Boolean]`: 是否在缓存中读取数据的读取。

    1. cache属性是true（默认值）时：在第一次请求完成之后，如果地址和参数不变化，第二次去请求，会默认获取缓存中的数据，不去读取服务器端的最新数据。

    2. cache属性是flase（默认值）时：每次读取的是最新的数据。

    3. ajax缓存只对GET方式的请求有效，因为浏览器认为POST请求提交的内容必定有变化，所以不走缓存。

> - dataType `[String]`: 

    1. 预期服务器返回的数据类型。如果不指定，jQuery 将自动根据 HTTP 包 MIME 信息来智能判断，比如 XML MIME 类型就被识别为 XML。在 1.4 中，JSON 就会生成一个 JavaScript 对象，而 script 则会执行这个脚本。随后服务器端返回的数据会根据这个值解析后，传递给回调函数。

    2.可用值:

    - "xml": 返回 XML 文档，可用 jQuery 处理。
    - "html": 返回纯文本 HTML 信息；包含的 script 标签会在插入 dom 时执行。
    - "script": 返回纯文本 JavaScript 代码。不会自动缓存结果。除非设置了 "cache" 参数。注意：在远程请求时(不在同一个域下)，所有 POST 请求都将转为 GET 请求。（因为将使用 DOM 的 script标签来加载）
    - "json": 返回 JSON 数据 。
    - "jsonp": JSONP 格式。使用 JSONP 形式调用函数时，如 "myurl?callback=?" jQuery 将自动替换 ? 为正确的函数名，以执行回调函数。
    - "text": 返回纯文本字符串

> - username `[String]`: 用于响应 HTTP 访问认证请求的用户名。

> - password `[String]`: 用于响应 HTTP 访问认证请求的密码。


#### 回调函数

> - beforeSend `[Function]`

1. 发送请求前可修改 XMLHttpRequest 对象的函数，如添加自定义 HTTP 头。XMLHttpRequest 对象是唯一的参数。

2. 这是一个 Ajax 事件。如果返回 false 可以取消本次 ajax 请求。

> - success `[Function]`: 请求成功后的回调函数。

> - error `[Function]`: 请求失败时调用此函数。

> - complete `[Function]`: 请求完成后回调函数 (请求成功或失败之后均调用)。

> - dataFilter `[Function]`: 在请求成功之后调用。传入返回的数据以及 "dataType" 参数的值。并且必须返回新的数据（可能是处理过的）传递给 success 回调函数。


#### 原生写法，原理解释

```js
function success(text) {
    var textarea = document.getElementById('test-response-text');
    textarea.value = text;
}

function fail(code) {
    var textarea = document.getElementById('test-response-text');
    textarea.value = 'Error code: ' + code;
}

var request = new XMLHttpRequest(); // 新建XMLHttpRequest对象

request.onreadystatechange = function () { // 状态发生变化时，函数被回调
    if (request.readyState === 4) { // 成功完成
        // 判断响应结果:
        if (request.status === 200) {
            // 成功，通过responseText拿到响应的文本:
            return success(request.responseText);
        } else {
            // 失败，根据响应码判断失败原因:
            return fail(request.status);
        }
    } else {
        // HTTP请求还在继续...
    }
}

// 发送请求:
request.open('GET', '/api/categories');
request.send();

alert('请求已发送，请等待响应...');

```

- 当创建了XMLHttpRequest对象后，要先设置onreadystatechange的回调函数。在回调函数中，通常我们只需通过readyState === 4判断请求是否完成，如果已完成，再根据status === 200判断是否是一个成功的响应。

- XMLHttpRequest对象的open()方法有3个参数，第一个参数指定是GET还是POST，第二个参数指定URL地址，第三个参数指定是否使用异步，默认是true，所以不用写。

- 注意，千万不要把第三个参数指定为false，否则浏览器将停止响应，直到AJAX请求完成。如果这个请求耗时10秒，那么10秒内你会发现浏览器处于“假死”状态。

- 最后调用send()方法才真正发送请求。GET请求不需要参数，POST请求需要把body部分以字符串或者FormData对象传进去。



#### 参考文档

- [W3school：Ajax()方法](http://www.w3school.com.cn/jquery/ajax_ajax.asp)

- [廖雪峰：JavaScript教程-Ajax](https://www.liaoxuefeng.com/wiki/001434446689867b27157e896e74d51a89c25cc8b43bdb3000/001434499861493e7c35be5e0864769a2c06afb4754acc6000)