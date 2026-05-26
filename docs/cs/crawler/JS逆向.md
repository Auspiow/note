# JS逆向

> 这是技术学习笔记，请勿传播或商用

## 浏览器调试技巧

### 使用断点

#### 断点

开发人员可以通过在源代码中设置断点来控制程序执行流程。一旦程序执行到设置的断点位置，它就会在那里暂停，使得开发人员可以逐步执行代码、观察变量的值，并进行其他调试操作。

#### 断点调试控制按钮

![image](./images/image.png)

| 图标 / 功能              | 快捷键    | 核心作用                             |
| ------------------------ | --------- | ------------------------------------ |
| 暂停 / 恢复 ( \|\| 或 ▶) |           |                                      |
| 跳过 (↷ Step over)       | F10 / F10 | 执行完当前行，不进入里面的函数内部   |
| 进入 (↓ Step into)       | F11 / F11 | 进入当前行调用的函数内部第一行       |
| 跳出 (↑ Step out)        | Shift+F11 | 执行完当前函数的余下代码，回到上一层 |
| 下一步 (→• Step)         | F9        | 挨个语句执行，遇到函数通常也会进去   |
| 启用/停用断点            | Ctrl+F8   | 临时让所有断点失效，或重新让它们生效 |

#### 断点方法

##### DOM事件断点定位加密位置

DOM事件断点是在浏览器的开发者工具中提供的一种调试工具，用于暂停JavaScript执行当特定类型的DOM事件。

使用DOM事件断点，你可以指定在哪些DOM事件上设置断点，比如点击（click）、改变（change）、加载（load）等。一旦设置了DOM事件断点，当相应的事件被触发时，浏览器会自动在触发事件的JavaScript代码行上暂停执行，从而允许你检查当前的程序状态、变量值，以及执行堆栈信息等。

执行的比较靠前，距离加密函数比较远

##### XHR断点定位加密位置

xhr断点是在浏览器的开发者工具中设置的一种调试工具，用于在进行网络请求时暂停JavaScript执行。

执行比较靠后，距离加密函数相对较近，可以根据栈快速定位

> 非xhr发送的接口就无法断点

#### 网站加载时间轴

加载HTML -> 加载JS -> 运行JS初始化 -> 用户触发某个事件 -> 调用某段JS -> 加密函数 -> 给服务器发信息（XHR-SEND）-> 接收到服务器数据 -> 解密函数（如果数据加密的话） -> 刷新网页渲染

### 调用堆栈

栈是一种先进后出的特殊线性表结构

调用栈是解析器的一种机制，可以在脚本调用多个函数时，通过这种机制，我们能够追踪到哪个函数正在执行，执行的函数体又调用了哪个函数。

* 当脚本要调用一个函数时，解析器把该函数添加到栈中并且执行这个函数。
* 任何被这个函数调用的函数会进一步添加到调用栈中，并且运行到它们被上个程序调用的位置。
* 当函数运行结束后，解释器将它从堆栈中取出，并在主代码列表中继续执行代码。

#### 断点案例

> https://oauth.d.cn/auth/goLogin.html

登录的时候看网络，搜索关键词。

搜索的时候有技巧，直接搜`pwd`往往会返回很多不相关的结果，可以尝试搜索其他参数名或者`pwd=`和`pwd:`（js的两种赋值语句）。

可以看到发送数据的代码

```js
// submit action
var pwdFormLogin = function(){
  var accountVal = $('input[name="account"]').val()
  var passwordVal = $('input[name="password"]').val()
  if(!accountVal){ new pwdLgnErr('请输入帐号'); return }
  if(!passwordVal){ new pwdLgnErr('请输入密码'); return }
  var rsaPwd;
  var validData = captchaObj.getValidate();
  if (!flag ||(flag && validData)) {
      rsaPwd = rsa(passwordVal);
  }else{
      new pwdLgnErr("请先完成图形验证")
      captchaObj.reset();
      return
  }
  var submitData;
  if(validData){
      submitData = {
          name: accountVal,
          pwd:rsaPwd, to:to,
          reqId:reqId,
          geetest_challenge : validData.geetest_challenge,
          geetest_validate : validData.geetest_validate,
          geetest_seccode :  validData.geetest_seccode
      }
  }else{
      submitData = {
          name: accountVal,
          pwd:rsaPwd, to:to,
          reqId:reqId
      }
  }
```

直接定位rsa函数

```js
//密码加密
var rsa = function (arg) {
  setMaxDigits(130);
  var PublicExponent = "10001";
  var modulus = "be44aec4d73408f6b60e6fe9e3dc55d0e1dc53a1e171e071b547e2e8e0b7da01c56e8c9bcf0521568eb111adccef4e40124b76e33e7ad75607c227af8f8e0b759c30ef283be8ab17a84b19a051df5f94c07e6e7be5f77866376322aac944f45f3ab532bb6efc70c1efa524d821d16cafb580c5a901f0defddea3692a4e68e6cd";
  var key = new RSAKeyPair(PublicExponent, "", modulus);
  return encryptedString(key, arg);
};
```

使用xhr来做的话就更加公式化一点（不需要费劲心思查找字符串）

查看网络发现接口是`/auth/login`，添加到xhr断点，

调用堆栈里面查看ajax→send→m→pwdFromLogin→dispatch→r.handle，也能发现。



## HOOK

`HOOK`是一种钩子技术，在系统没有调用函数之前，钩子程序就先得到控制权，这时钩子函数既可以加工处理（改变）该函数的执行行为，也可以强制结束消息的传递。简单来说，修改原有的`JavaScript`代码就是`HOOK`。

**实现机制**

1. 客户端（浏览器）拥有`JavaScript`的最高解释权，可以决定在任何时候注入`JavaScript`，而服务器无法阻止或干预。服务端只能通过检测和混淆的手段，使`hook`难度加大，但是无法直接阻止。
2. 除了上面的必要条件之外，还有一个条件。就是`JavaScript`是一种弱类型语言，同一个变量可以多次定义、根据需要进行不同的赋值，而这种情况如果在其他强类型语言中则可能会报错，导致代码无法执行。`JavaScript`的这种特性，为我们`hook`代码提供了便利。

注意：`JavaScript`变量是有作用域的，只有当被`hook`的函数和`debugger`断点在同一个作用域的时候，才能`hook`成功。

### 绕过debugger

定时器debugger

```html
<!DOCTYPE html>
<body>
<h1 id="box"></h1>
</body>
<script>
    var temp = document.getElementById('box')

    function debug_func() {
        debugger;
    }

    setInterval(debug_func, 1000);
    temp.innerHTML = "hello";
</script>
</html>
```

控制台debugger

```html
<!DOCTYPE html>
<html lang="en">
<body>
hello
</body>
<script>
    function detectDevTools() {
        var threshold = 200;
        var widthThreshold = window.outerWidth - window.innerWidth > threshold;
        var heightThreshold = window.outerHeight - window.innerHeight > threshold;
        if (widthThreshold || heightThreshold) {
            debugger;
            console.log('控制台打开了...')
        }
    }

    setInterval(detectDevTools, 100)
</script>
</html>
```

1. 直接打上一律不在此处暂停的标签或者条件断点（false）

2. 直接hook函数（此法杀伤力过大，会影响程序的正常运行）

   ```js
   setInterval = (()=>{})
   ```

3. 替换文件

构造器断点

```html
<!DOCTYPE html>
<html>
<body>hello</body>
<script>
    function check() {
        function do_check() {
            (function(){}["constructor"]("debugger")());
            do_check(++arguments[0]);
        }
        do_check(0);
    }
    check();
</script>
</html>
```

绕过方法

```js
var _constructor = constructor;
Function.prototype.constructor = function(attr) {
  if (attr == "debugger") {
      return null;
  }
  return _constructor(attr);
}
```

> http://epub.cnipa.gov.cn/

过了前两个断点之后发现构造器断点，直接绕过即可

```js
// VMXXXX.js
(function anonymous(_$db) {
debugger;_$db(17794650147830)
})
```

按道理来说是要看一下堆栈的，但是全部加密有点太复杂了，之后再说。

### HOOK 定位数据加密位置

> https://www.hanghangcha.com/hhcreport

使用`/hhc/member/behavior/getActivity` xhr断点

send方法往下单步可以看到done的逻辑，注意到decrypt

```js
 u.a.ajax(m).done((function(e) {
    if (e.errCode)...
    else if ("hhc/tag" === m.url) {
        var t = h["a"].decrypt(e.data);
        e = JSON.parse(t),
        a(e)
    } else {
        var n = e, r = n.message;
        "次数达上限" === r && s["a"].commit("AI/SET_ISAIUSED", !0),
        a(e.data || e)
    }
}
```

或者继续往下走

```js
getOriginReports: function(t) {
    var e = this;
    this.$api.getOriginReports({
        filter: JSON.stringify(t)
    }).then((function(i) {
        var s = S["a"].decrypt(i);
```

跳转到decrypt函数

```js
decrypt: function(e, n) {
    var t = u["a"].state.clientEncrypt, c = t.substring(t.length - 1), r = "";
    if ("0" === c) {
        n = n || "".concat(o).concat(l).concat(h);
        var d = a.a.enc.Utf8.parse(n)
          , s = a.a.AES.decrypt(e, d, {
            mode: a.a.mode.ECB,
            padding: a.a.pad.Pkcs7
        });
        r = a.a.enc.Utf8.stringify(s).toString()
    } else if ("1" === c) {
        var f = i.a.sm4
          , p = {
            key: "MbzgvXzBWynQrtpy",
            mode: "cbc",
            iv: "kDrvPQfPIuArAzkF",
            cipherType: "base64"
        }
          , m = new f(p);
        r = m.decrypt(e)
    }
    return r
}
```

用hook也可以做到，通过对parse方法的hook定位解密的js

```js
(()=>{
    var _parse = JSON.parse;
    JSON.parse = function (value) {
        debugger;
        return _parse(value);
    }
})()
```

### HOOK Cookie

> http://q.10jqka.com.cn/

定位`cookie`中`v`值的加密位置

```txt
v=A-ZTaUtcsGSsK2TAhPnGqD1JN1dtxykk_Ape49CP01HmSIjJOFd6kcybrvij
```

使用Object.defineProperty()检测cookie

> https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Object/defineProperty

```js
(()=>{
    _cookie = document.cookie;
    Object.defineProperty(document, 'cookie', {
        set: function (val) {
            if (val.indexOf('v') != -1) {
                debugger;
            }
            _cookie = val;
        },
        get: function () {
            return _cookie;
        },
    });
})()
```

定位到setCookie的代码

```js
function W() {
    var t = n[161]
      , r = qn.update(); 
    // r=A-ZTaUtcsGSsK2TAhPnGqD1JN1dtxykk_Ape49CP01HmSIjJOFd6kcybrvij
    return Wn.setCookie(On, r, m + Rn + w + t, E, n[162]),
    $n.set(Pn, r),
    r
}
```

```js
qn.update()
'A2zZn51-KipC6T4W8lbs1kNLPUGbJRQiEssklcatete4zgJ_7jXgX2LZ9CwV'
```

### HOOK XHR

| 调用方式        | 核心特点     | 传参方式  | 主要目的                            |
| --------------- | ------------ | --------- | ----------------------------------- |
| my_func()       | 最基础的调用 | (a, b)    | 正常执行函数                        |
| (0, my_func)()  | 间接调用     | (a, b)    | 重置 this 指向，使其指向全局        |
| my_func.call()  | 改变 this    | 1, 2, 3   | 手动指定 this，参数明确时使用       |
| my_func.apply() | 改变 this    | [1, 2, 3] | 手动指定 this，参数是动态数组时使用 |

> https://www.qimai.cn/

```js
(()=>{
    let open = window.XMLHttpRequest.prototype.open;
    window.XMLHttpRequest.prototype.open = function (method, url, async) {
        if (url.indexOf("analysis") != -1) {
            debugger;
        }
        return open.apply(this, arguments);
    };
})()
```

### XMLHttpRequest 与拦截器

发送请求一般都会借助 jquery 完成，框架中内置了ajax方法完成网络请求，底层代码是 XMLHttpRequest

用 python 作为类比，发送网络请求一般会使用 requests 第三方库，其底层代码是 urllib3

> http://www.cninfo.com.cn/new/commonUrl?url=disclosure/list/notice#szseGem

```js
var xhr = new XMLHttpRequest();
xhr.open('post', 'http://www.cninfo.com.cn/new/disclosure', true)
xhr.setRequestHeader('accept', 'application/json;charset=UTF-8')
xhr.send('column=szse_latest&pageNum=2&pageSize=30&sortName=&sortType=&clusterFlag=true')
xhr.onreadystatechange = ()=>{
    console.log(xhr.response)
}
```

