# JS逆向

> 这是技术学习笔记，请勿传播或商用

## 浏览器调试技巧

### 使用断点

#### 断点

开发人员可以通过在源代码中设置断点来控制程序执行流程。一旦程序执行到设置的断点位置，它就会在那里暂停，使得开发人员可以逐步执行代码、观察变量的值，并进行其他调试操作。

ccd #### 断点调试控制按钮

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

##### 实现机制

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





