# JS妙妙小知识

以下知识参考自 [现代 JavaScript 教程](https://zh.javascript.info/) 以及 [MDN开发者手册](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript)

一开始直接看的MDN手册，但是上面的知识比较“官方”，适合查阅但不易消化理解。后来遇到了“现代JavaScript教程”，将知识贯通了起来，于是有了这篇笔记。笔记的主要脉络也是与“现代JavaScript教程”保持一致，但是略有修改（精简）。

## 基础知识

### 变量

* var：老旧的变量声明方式，一般情况下不会使用它。变量的声明被提升为undefined。

* let：用于声明可重新赋值的块级作用域局部变量，会出现暂时性死区。

* const：声明一个块级作用域的只读命名常量。

> 暂时性死区（Temporal Dead Zone）
>
> 没有声明：ReferenceError；没有初始化：undefined
>
> ```js
> console.log(a); // ReferenceError
> let a = 10;
> 
> console.log(b); // undefined
> var b = 10;
> ```

JavaScript 的变量命名有两个限制：

1. 变量名称必须仅包含字母、数字、符号 `$` 和 `_`。
2. 首字符必须非数字。

其实也可以使用任何语言，包括西里尔字母（cyrillic letters）甚至是象形文字，以下的变量命名都是有效的

```js
let $ = 1;
let _ = 2;
let имя = 'abc';
let 我 = '...';
```



### 数据类型

JavaScript 中有八种基本的数据类型。七种原始数据类型（基本数据类型）：number，bigint，string，boolean，null，undefined，symbol。以及一种非原始数据类型（复杂数据类型）：object

#### Number

*number* 类型代表整数和浮点数。

除了常规的数字，还包括所谓的“特殊数值（special numeric values）”：`Infinity`、`-Infinity` 和 `NaN`。

`Infinity` 代表数学概念中的无穷大 ∞。是一个比任何数字都大的特殊值。

`NaN` 是粘性的。任何对 `NaN` 的进一步数学运算都会返回 `NaN`

```javascript
alert( 1 / 0 ); // Infinity
alert( Infinity ); // Infinity
alert( "not a number" / 2 ); // NaN
alert( NaN + 1 ); // NaN
```

#### BigInt

在 JavaScript 中，“number” 类型无法安全地表示大于 (2⁵³-1)（ 即9007199254740991），或小于 -(2⁵³-1)的整数。

*number* 类型其实可以存储更大的整数，但超出安全范围会出现精度问题

所有大于(2⁵³-1)的奇数都不能用 “number” 类型存储。

```javascript
console.log(9007199254740991 + 1); // 9007199254740992
console.log(9007199254740991 + 2); // 9007199254740992
```

可以通过将 `n` 附加到整数字段的末尾来创建 `BigInt` 值。

```javascript
// 尾部的 "n" 表示这是一个 BigInt 类型
const bigInt = 1234567890123456789012345678901234567890n;
```

> 目前 Firefox/Chrome/Edge/Safari 已经支持 BigInt 了，但 IE 还没有

#### String

在 JavaScript 中，有三种包含字符串的方式：双引号，单引号，反引号。

反引号是 **功能扩展** 引号。它们允许我们通过将变量和表达式包装在 `${…}` 中，来将它们嵌入到字符串中。例如：

```javascript
let name = "John";
// 嵌入一个变量
alert( `Hello, ${name}!` ); // Hello, John!
// 嵌入一个表达式
alert( `the result is ${1 + 2}` ); // the result is 3
```

`${…}` 内的表达式会被计算，计算结果会成为字符串的一部分。



### 数据类型的转换：

#### 字符串转换

`alert(value)` 可以将 `value` 转换为字符串类型，然后显示这个值。

也可以显式地调用 `String(value)` 来将 `value` 转换为字符串类型：

```javascript
let value = true;
alert(typeof value); // boolean
value = String(value);
alert(typeof value); // string
```

使用加号表达式自动把数字转换成字符串

```js
x = "answer is " + 42; // answer is 42
z = "37" + 7; // "377"
```

#### 数字型转换

主要通过`Number()`函数，还有 `+` 运算符，`parseInt()`和`parseFloat()`函数

`Number()`函数转换规则：

| 值                 | 变成……                                                       |
| :----------------- | :----------------------------------------------------------- |
| `undefined`        | `NaN`                                                        |
| `null`             | `0`                                                          |
| `true` and `false` | `1` and `0`                                                  |
| `string`           | 去掉首尾空白字符（空格、换行符、制表符等）后的纯数字字符串中含有的数字。如果剩余字符串为空，则转换结果为 `0`。当类型转换出现 error 时返回 `NaN`。 |

```javascript
alert(Number("   123   ")); // 123
alert(Number("123z"));      // NaN
alert(Number("an arbitrary string instead of a number");）// NaN
alert(Number(true));        // 1
alert(Number(false));       // 0
```

两个函数：`parseInt()`和`parseFloat()`

parseInt只返回整数，处理小数时用途有限

> parseInt 的最好带上进制参数，用于指定使用哪一种进制。

```js
parseInt("101", 2); // 5
```

使用`+`运算符

```js
alert((+"1.1") + (+"1.1")) //2.2
alert(+"1.1"+ +"1.1") //2.2
```

使用其他运算符时也会自动把字符串转换为数字

```js
alert("37" * 7); // 259
```

#### 布尔型转换

直观上为“空”的值（如 `0`、空字符串、`null`、`undefined` 和 `NaN`）将变为 `false`，其他值变成 `true`。

比如：

```javascript
alert( Boolean(1) ); // true
alert( Boolean(0) ); // false
alert( Boolean("hello") ); // true
alert( Boolean("") ); // false
```

> 一些编程语言（比如 PHP）视 `"0"` 为 `false`。
> 但在 JavaScript 中，非空的字符串总是 `true`。

```javascript
alert( Boolean("0") ); // true
alert( Boolean(" ") ); // 空格，也是 true（任何非空字符串都是 true）
```



### 浏览器交互

几个浏览器中与用户交互的函数：`alert`，`prompt` 和`confirm`。

#### alert

```javascript
alert("Hello");
```

弹出的这个带有信息的小窗口被称为 **模态窗**。“modal” 意味着用户不能与页面的其他部分进行交互，直到他们处理完窗口。

#### prompt

```javascript
result = prompt(title, [default]);
```

浏览器会显示一个带有文本消息的模态窗口，还有 input 框和确定/取消按钮。

title 是显示给用户的文本，default 可以指定 input 框的初始值

`prompt` 将返回用户在 `input` 框内输入的文本，如果用户取消了输入，则返回 `null`。

```javascript
let age = prompt('How old are you?', 100);
alert(`You are ${age} years old!`); // You are 100 years old!
```

#### confirm

```javascript
result = confirm(question);
```

`confirm` 函数显示一个带有 `question` 以及确定和取消两个按钮的模态窗口。

点击确定返回 `true`，点击取消返回 `false`。

```javascript
let isBoss = confirm("Are you the boss?");
alert( isBoss ); // 如果“确定”按钮被按下，则显示 true
```



### 基础运算符

#### 逗号运算符

逗号运算符 `,` 是最少见最不常使用的运算符之一。

逗号运算符能让我们处理多个表达式，使用 `,` 将它们分开。每个表达式都运行了，但是只有最后一个的结果会被返回。

```javascript
let a = (1 + 2, 3 + 4);

alert( a ); // 7（3 + 4 的结果）
```

这里，第一个表达式 `1 + 2` 运行了，但是它的结果被丢弃了。随后计算 `3 + 4`，并且该计算结果被返回。

> 逗号运算符的优先级非常低
>
> 请注意逗号运算符的优先级非常低，比 `=` 还要低，因此上面你的例子中圆括号非常重要。

为什么我们需要这样一个运算符，它只返回最后一个值呢？

具体应用场景：

```javascript
// 一行上有三个运算符
for (a = 1, b = 3, c = a * b; a < 10; a++) {
 ...
}
```

这样的技巧在许多 JavaScript 框架中都有使用。但是通常它并不能提升代码的可读性，使用它之前，我们要想清楚。

### 值的比较

当对不同类型的值进行比较时，JavaScript 会首先将其转化为数字（number）再判定大小。

#### 严格相等

普通的相等性检查 `==` 存在一个问题，它不能区分出 `0` 和 `false`：

```javascript
alert( 0 == false ); // true
```

也同样无法区分空字符串和 `false`：

```javascript
alert( '' == false ); // true
```

这是因为在比较不同类型的值时，处于相等判断符号 `==` 两侧的值会先被转化为数字。空字符串和 `false` 也是如此，转化后它们都为数字 0。

**严格相等运算符 `===` 在进行比较时不会做任何的类型转换。**

```javascript
alert( 0 === false ); // false，因为被比较值的数据类型不同
```

同样的，与“不相等”符号 `!=` 类似，“严格不相等”表示为 `!==`。

严格相等的运算符虽然写起来稍微长一些，但是它能够很清楚地显示代码意图，降低你犯错的可能性。

#### 对 null 和 undefined 进行比较

当使用 `null` 或 `undefined` 与其他值进行比较时，其返回结果常常出乎你的意料。

- 当使用严格相等 `===` 比较二者时

  它们不相等，因为它们属于不同的类型。

  ```js
  alert( null === undefined ); // false
  ```

- 当使用非严格相等 `==` 比较二者时

  JavaScript 存在一个特殊的规则，会判定它们相等。它们仅等于对方而不等于其他任何的值（只在非严格相等下成立）。

  ```js
  alert( null == undefined ); // true
  ```

- 当使用数学式或其他比较方法 `< > <= >=` 时：

  `null/undefined` 会被转化为数字：`null` 被转化为 `0`，`undefined` 被转化为 `NaN`。

#### 奇怪的结果：null vs 0

通过比较 `null` 和 0 可得：

```javascript
alert( null > 0 );  // false
alert( null == 0 ); // false
alert( null >= 0 ); // true
```

是的，上面的结果完全打破了你对数学的认识。在最后一行代码显示“`null` 大于等于 0”的情况下，前两行代码中一定会有一个是正确的，然而事实表明它们的结果都是 false。

为什么会出现这种反常结果，这是因为相等性检查 `==` 和普通比较符 `> < >= <=` 的代码逻辑是相互独立的。进行值的比较时，`null` 会被转化为数字，因此它被转化为了 `0`。这就是为什么（3）中 `null >= 0` 返回值是 true，（1）中 `null > 0` 返回值是 false。

另一方面，`undefined` 和 `null` 在相等性检查 `==` 中不会进行任何的类型转换，它们有自己独立的比较规则，所以除了它们之间互等外，不会等于任何其他的值。这就解释了为什么（2）中 `null == 0` 会返回 false。

#### 特立独行的 undefined

`undefined` 不应该被与其他值进行比较：

```javascript
alert( undefined > 0 ); // false
alert( undefined < 0 ); // false
alert( undefined == 0 ); // false
```



### 逻辑运算符

`||` 或

给定多个参与或运算的值：

```javascript
result = value1 || value2 || value3;
```

或运算符 `||` 做了如下的事情：

- 从左到右依次计算操作数。
- 处理每一个操作数时，都将其转化为布尔值。如果结果是 `true`，就停止计算，返回这个操作数的初始值。
- 如果所有的操作数都被计算过（也就是，转换结果都是 `false`），则返回最后一个操作数。

`&&` 与

给出多个参加与运算的值：

```javascript
result = value1 && value2 && value3;
```

与运算 `&&` 做了如下的事：

- 从左到右依次计算操作数。
- 在处理每一个操作数时，都将其转化为布尔值。如果结果是 `false`，就停止计算，并返回这个操作数的初始值。
- 如果所有的操作数都被计算过（例如都是真值），则返回最后一个操作数。

> 与运算 `&&` 的优先级比或运算 `||` 要高。

`!` 非

两个非运算 `!!` 有时候用来将某个值转化为布尔类型：

```javascript
alert( !!"non-empty string" ); // true
alert( !!null ); // false
```

也就是，第一个非运算将该值转化为布尔类型并取反，第二个非运算再次取反。最后我们就得到了一个任意值到布尔值的转化。

也可以用内建的 `Boolean` 函数：

```javascript
alert( Boolean("non-empty string") ); // true
alert( Boolean(null) ); // false
```

非运算符 `!` 的优先级在所有逻辑运算符里面最高，所以它总是在 `&&` 和 `||` 之前执行。



### 空值运算符

空值合并运算符（nullish coalescing operator）的写法为两个问号 `??`。

我们可以使用我们已知的运算符重写 `result = a ?? b`，像这样：

```javascript
result = (a !== null && a !== undefined) ? a : b;
```

`??` 的常见使用场景是提供默认值。

例如，在这里，如果 `user` 的值不为 `null/undefined` 则显示 `user`，否则显示 `匿名`：

```javascript
let user;
alert(user ?? "Anonymous"); // Anonymous
```

在下面这个例子中，我们将一个名字赋值给了 `user`：

```javascript
let user = "John";

alert(user ?? "Anonymous"); // John
```

我们还可以使用 `??` 序列从一系列的值中选择出第一个非 `null/undefined` 的值。

假设我们在变量 `firstName`、`lastName` 或 `nickName` 中存储着一个用户的数据。如果用户决定不填写相应的值，则所有这些变量的值都可能是未定义的。

让我们使用 `??` 运算符来实现这一需求：

```javascript
let firstName = null;
let lastName = null;
let nickName = "Supercoder";

// 显示第一个已定义的值：
alert(firstName ?? lastName ?? nickName ?? "匿名"); // Supercoder
```



### 循环

#### 省略语句段

`for` 循环的任何语句段都可以被省略。

例如，如果我们在循环开始时不需要做任何事，我们就可以省略 `begin` 语句段。

```javascript
let i = 0; // 我们已经声明了 i 并对它进行了赋值

for (; i < 3; i++) { // 不再需要 "begin" 语句段
  alert( i ); // 0, 1, 2
}
```

可以移除 `step` 语句段：

```javascript
let i = 0;

for (; i < 3;) {
  alert( i++ );
}
```

实际上可以删除所有内容，从而创建一个无限循环：

```javascript
for (;;) {
  // 无限循环
}
```

请注意 `for` 的两个 `;` 必须存在，否则会出现语法错误。

#### break/continue 标签（label）

一个 `label`提供了一个让你在程序中其他位置引用它的标识符。使用label可以帮助我们从多层循环中跳出。

```
label :
   statement
```

`break <labelName>` 语句跳出循环至标签处：

```javascript
outer: for (let i = 0; i < 3; i++) {
  for (let j = 0; j < 3; j++) {
    let input = prompt(`Value at coords (${i},${j})`, '');
    if (!input) break outer;
    // some functions
  }
}
alert('Done!');
```

上述代码中，`break outer` 向上寻找名为 `outer` 的标签并跳出当前循环。

我们还可以将标签移至单独一行：

```javascript
outer:
for (let i = 0; i < 3; i++) { ... }
```

>  冷知识：只有python没有label用法

#### for循环特殊用法

`for...in` ，详细用法参考 ["for...in"小章节](#forin)

枚举属性名（key）

```js
for (variable in object) {
  statements
}
```

`for...of`

在可迭代对象（包括Array、Map、Set、arguments等等）上创建了一个循环，对值的每一个独特属性调用一次迭代，不会遍历原型链。

```js
for (variable of object) {
  statement
}
```

示例：

```js
let arr = [3, 5, 7];
arr.foo = "hello";

for (let i of arr) {
  console.log(i); // 输出 "3", "5", "7"，没有"hello"
}
```



### 函数

函数的参数本质上是按值传递的，这个改变不会反映到全局或调用该函数的代码中。

但是有特殊情况，如果将对象的属性改变，这样的改变对函数外部是可见的。

对象的改变是可见的

```js
function myFunc(theObject) {
  theObject.make = "Toyota";
}

const mycar = {
  make: "Honda",
  model: "Accord",
  year: 1998,
};

console.log(mycar.make); // "Honda"
myFunc(mycar);
console.log(mycar.make); // "Toyota"
```

数组的改变对函数外部也是可见的

```js
function myFunc(theArr) {
  theArr[0] = 30;
}

const arr = [45];

console.log(arr[0]); // 45
myFunc(arr);
console.log(arr[0]); // 30
```



### 函数表达式

函数也可以由函数表达式创建，类比python中的lambda，但是js好在并没有改变名字，还是function

```js
let square = function (number) {
  return number * number;
};

console.log(square(4)); // 16
```

也可以为函数表达式提供名字，用于递归

```js
const factorial = function fac(n) {
  return n < 2 ? 1 : n * fac(n - 1);
};

console.log(factorial(3)); // 6
```

#### 函数是一个值

**无论函数是如何创建的，函数都是一个值。**

```javascript
function sayHi() {
  alert( "Hello" );
}

alert( sayHi ); // 显示函数代码
```

注意，最后一行代码并不会运行函数，因为 `sayHi` 后没有括号。在某些编程语言中，只要提到函数的名称都会导致函数的调用执行，但 JavaScript 可不是这样。

我们可以复制函数到其他变量：

```javascript
function sayHi() {
  alert( "Hello" );
}

let func = sayHi;    // 复制

func(); // Hello
sayHi(); // Hello
```

#### 回调函数

让我们多举几个例子，看看如何将函数作为值来传递以及如何使用函数表达式。

写一个包含三个参数的函数 `ask(question, yes, no)`：

```javascript
function ask(question, yes, no) {
  if (confirm(question)) yes()
  else no();
}

function showOk() {
  alert( "You agreed." );
}

function showCancel() {
  alert( "You canceled the execution." );
}

// 用法：函数 showOk 和 showCancel 被作为参数传入到 ask
ask("Do you agree?", showOk, showCancel);
```

在实际开发中，这样的函数是非常有用的。实际开发与上述示例最大的区别是，实际开发中的函数会通过更加复杂的方式与用户进行交互，而不是通过简单的 `confirm`。在浏览器中，这样的函数通常会绘制一个漂亮的提问窗口。但这是另外一件事了。

`ask` 的两个参数值 `showOk` 和 `showCancel` 可以被称为 **回调函数** 或简称 **回调**。

主要思想是我们传递一个函数，并期望在稍后必要时将其“回调”。`showOk` 是回答 “yes” 的回调，`showCancel` 是回答 “no” 的回调。

我们可以使用函数表达式来编写一个等价的、更简洁的函数：

```javascript
function ask(question, yes, no) {
  if (confirm(question)) yes()
  else no();
}

ask(
  "Do you agree?",
  function() { alert("You agreed."); },
  function() { alert("You canceled the execution."); }
);
```

这里直接在 `ask(...)` 调用内进行函数声明。这两个函数没有名字，所以叫 **匿名函数**。这样的函数在 `ask` 外无法访问（因为没有对它们分配变量），不过这正是我们想要的。

##### 回调地狱

> 参考自博客：https://blog.namichong.com/learn/web/javascript/what_is_callback_hell.html

回调地狱（callback hell）指的是在异步编程中，由于多次嵌套回调函数而导致代码变得复杂、难以维护的情况。

例如，以下是一个回调地狱的示例，用于读取一个文件，然后将文件内容转换成 JSON 格式，并将 JSON 格式的数据存储到数据库中：

```js
const fs = require('fs');

fs.readFile('file.txt', 'utf-8', function(err, data) {
  if (err) {
    console.log(err);
  } else {
    var json = JSON.parse(data);
    db.save(json, function(err) {
      if (err) {
        console.log(err);
      } else {
        console.log('Data saved successfully');
      }
    });
  }
});
```

使用 Promise 来解决：

```js
const fs = require('fs');

function readFile(filePath) {
  return new Promise((resolve, reject) => {
    fs.readFile(filePath, 'utf-8', (err, data) => {
      if (err) {
        reject(err);
      } else {
        resolve(data);
      }
    });
  });
}

readFile('file.txt')
  .then((data) => {
    var json = JSON.parse(data);
    return db.save(json);
  })
  .then(() => {
    console.log('Data saved successfully');
  })
  .catch((err) => {
    console.log(err);
  });
```



### 箭头函数

```javascript
let func = (arg1, arg2, ..., argN) => expression;
```

这里创建了一个函数 `func`，它接受参数 `arg1..argN`，然后使用参数对右侧的 `expression` 求值并返回其结果。

换句话说，它是下面这段代码的更短的版本：

```javascript
let func = function(arg1, arg2, ..., argN) {
  return expression;
};
```

让我们来看一个具体的例子：

```javascript
let sum = (a, b) => a + b;
alert( sum(1, 2) ); // 3
```

可以看到 `(a, b) => a + b` 表示一个函数接受两个名为 `a` 和 `b` 的参数。在执行时，它将对表达式 `a + b` 求值，并返回计算结果。

- 如果我们只有一个参数，还可以省略掉参数外的圆括号，使代码更短。

  ```javascript
  let double = n => n * 2;
  // 差不多等同于：let double = function(n) { return n * 2 }
  
  alert( double(3) ); // 6
  ```

- 如果没有参数，括号则是空的（但括号必须保留）：

  ```javascript
  let sayHi = () => alert("Hello!");
  
  sayHi();
  ```

箭头函数可以像函数表达式一样使用。

例如，动态创建一个函数：

```javascript
let age = prompt("What is your age?", 18);

let welcome = (age < 18) ?
  () => alert('Hello!') :
  () => alert("Greetings!");

welcome();
```

#### 多行的箭头函数

有时我们需要更复杂一点的函数，比如带有多行的表达式或语句。在这种情况下，我们可以使用花括号将它们括起来。主要区别在于，用花括号括起来之后，需要包含 `return` 才能返回值（就像常规函数一样）。

就像这样：

```javascript
let sum = (a, b) => {  // 花括号表示开始一个多行函数
  let result = a + b;
  return result; // 如果我们使用了花括号，那么我们需要一个显式的 “return”
};

alert( sum(1, 2) ); // 3
```

### 函数声明与函数表达式的区别

**函数表达式是在代码执行到达时被创建，并且仅从那一刻起可用。**

一旦代码执行到赋值表达式 `let sum = function…` 的右侧，此时就会开始创建该函数，并且可以从现在开始使用（分配，调用等）。

函数声明则不同。

**在函数声明被定义之前，它就可以被调用。**

函数声明的另外一个特殊的功能是它们的块级作用域。

**严格模式下，当一个函数声明在一个代码块内时，它在该代码块内的任何位置都是可见的。但在代码块外不可见。**



## 对象

我们可以用下面两种语法中的任一种来创建一个空的对象，通常用花括号（字面量）。

```javascript
let user = new Object(); // “构造函数” 的语法
let user = {};  // “字面量” 的语法
```

### 属性

可以在创建对象的时候，立即将一些属性以键值对的形式放到 `{...}` 中。

```javascript
let user = {     // 一个对象
  name: "John",  // 键 "name"，值 "John"
  age: 30        // 键 "age"，值 30
};
```

属性有键（或者也可以叫做“名字”或“标识符”），位于冒号 `":"` 的前面，值在冒号的右边。

可以使用点符号访问属性值，属性的值可以是任意类型：

```javascript
// 读取文件的属性：
alert( user.name ); // John
alert( user.age ); // 30
user.isAdmin = true;
```

用 `delete` 操作符移除属性：

```javascript
delete user.age;
```

我们也可以用多字词语来作为属性名，但必须给它们加上引号：

```javascript
let user = {
  name: "John",
  age: 30,
  "likes birds": true  // 多词属性名必须加引号
};
```

属性的名字如果不是有效的JS标识符，只能通过方括号标记访问。

```js
let myObj = new Object(),obj = new Object();

myObj.type = "Dot syntax";
myObj["date created"] = "String with space"; // space is not valid
myObj[obj] = "thisIsAObject"; // object is not valid
      
console.log(myObj)
/* {
  "type": "Dot syntax",
  "date created": "String with space",
  "[object Object]": "thisIsAObject"
} */
```

可以通过存储在变量中的字符串来访问属性：

```js
let propertyName = "make";
myCar[propertyName] = "Ford";
```

列表中的最后一个属性推荐以逗号结尾（虽然有点反常识）：

```javascript
let user = {
  name: "John",
  age: 30,
}
```

> 这叫做尾随（trailing）或悬挂（hanging）逗号。这样便于我们添加、删除和移动属性，因为所有的行都是相似的。

#### 计算属性

当创建一个对象时，我们可以在对象字面量中使用方括号，叫做计算属性。

```javascript
let fruit = prompt("Which fruit to buy?", "apple");

let bag = {
  [fruit]: 5, // 属性名是从 fruit 变量中得到的
};

alert( bag.apple ); // 5 如果 fruit="apple"
```

本质上，这种写法和下面的语法效果相同：

```javascript
let fruit = prompt("Which fruit to buy?", "apple");
let bag = {};
bag[fruit] = 5;
```

我们可以在方括号中使用更复杂的表达式：

```javascript
let fruit = 'apple';
let bag = {
  [fruit + 'Computers']: 5 // bag.appleComputers = 5
};
```

#### 属性值简写

在实际开发中，通常用已存在的变量当做属性名。

```javascript
function makeUser(name, age) {
  return {
    name: name,
    age: age,
    // ……其他的属性
  };
}

let user = makeUser("John", 30);
alert(user.name); // John
```

可以用 `name` 来代替 `name:name` 像下面那样：

```javascript
function makeUser(name, age) {
  return {
    name, // 与 name: name 相同
    age,  // 与 age: age 相同
    // ...
  };
}
```

也可以把属性名简写方式和正常方式混用：

```javascript
let user = {
  name,  // 与 name:name 相同
  age: 30
};
```

#### 属性名称限制

我们已经知道，变量名不能是编程语言的某个保留字，如 “for”、“let”、“return” 等……

但对象的属性名并不受此限制：

```javascript
// 这些属性都没问题
let obj = {
  for: 1,
  let: 2,
  return: 3
};

alert( obj.for + obj.let + obj.return );  // 6
```

简而言之，属性命名没有限制。属性名可以是任何字符串或者 symbol（一种特殊的标志符类型，将在后面介绍）。

其他类型会被自动地转换为字符串。

例如，当数字 `0` 被用作对象的属性的键时，会被转换为字符串 `"0"`：

```javascript
let obj = {
  0: "test" // 等同于 "0": "test"
};

// 都会输出相同的属性（数字 0 被转为字符串 "0"）
alert( obj["0"] ); // test
alert( obj[0] ); // test (相同的属性)
```

这里有个小陷阱：一个名为 `__proto__` 的属性。我们不能将它设置为一个非对象的值：

```javascript
let obj = {};
obj.__proto__ = 5; // 分配一个数字
alert(obj.__proto__); // [object Object] —— 值为对象，与预期结果不同
```

我们从代码中可以看出来，把它赋值为 `5` 的操作被忽略了。

#### 属性存在性测试

相比于其他语言，JavaScript 的对象有一个需要注意的特性：能够被访问任何属性。即使属性不存在也不会报错！

读取不存在的属性只会得到 undefined。

```javascript
let user = {};
alert( user.noSuchProperty === undefined ); // true
```

也可以用 `in` 进行判断

```javascript
"key" in object
```

例如：

```javascript
let user = { name: "John", age: 30 };

alert( "age" in user ); // true
alert( "blabla" in user ); // false
```

请注意，`in` 的左边必须是属性名。通常是带引号的字符串或者变量。

```javascript
let user = { age: 30 };

let key = "age";
alert( key in user ); // true，属性 "age" 存在
```

大部分情况下与 `undefined` 进行比较来判断就可以了。但有一个例外情况，那就是属性存在，但存储的值是 `undefined` 的时候：

```javascript
let obj = {
  test: undefined
};

alert( obj.test === undefined ); // true, but is not "true"
alert( "test" in obj ); // true
```

#### "for..in" 

为了遍历一个对象的所有键（key），可以使用一个特殊形式的循环：`for..in`。

```javascript
for (key in object) {
  // 对此对象属性中的每个键执行的代码
}
```

列出 `user` 所有的属性：

```javascript
let user = {
  name: "John",
  age: 30,
  isAdmin: true
};

for (let key in user) {
  alert( key );  // name, age, isAdmin
  alert( user[key] ); // John, 30, true
}
```

注意，所有的 “for” 结构体都允许我们在循环中定义变量，可以用其他属性名来替代 key。例如 `"for(let prop in obj)"` 也很常用。

虽然使用 **for...in** 来迭代数组 Array元素听起来很诱人，但是它返回的东西除了数字索引外，还有可能是你自定义的属性名字。

```js
let arr = [3, 5, 7];
arr.foo = "hello";

for (let i in arr) {
  console.log(i); // 输出 "0", "1", "2", "foo"
}
```

> 枚举属性的方法:
>
> for...in 循环 该方法依次访问一个对象及其原型链中所有可枚举的属性。
>
> Object.keys(o)该方法返回对象 `o` 自身包含（不包括原型中）的所有可枚举属性的名称的数组。
>
> Object.getOwnPropertyNames(o)该方法返回对象 `o` 自身包含（不包括原型中）的所有属性 (无论是否可枚举) 的名称的数组。

#### 属性的排序

属性有顺序吗？换句话说，如果我们遍历一个对象，我们获取属性的顺序是和属性添加时的顺序相同吗？这靠谱吗？

简短的回答是：“有特别的顺序”：整数属性会被进行排序，其他属性则按照创建的顺序显示。

例如，让我们考虑一个带有电话号码的对象：

```javascript
let codes = {
  "49": "Germany",
  "41": "Switzerland",
  "44": "Great Britain",
  // ..,
  "1": "USA"
};

for(let code in codes) {
  alert(code); // 1, 41, 44, 49
}
```

因为这些电话号码是整数，所以它们以升序排列。所以我们看到的是 `1, 41, 44, 49`。

**整数属性？那是什么？**

这里的“整数属性”指的是一个可以在不做任何更改的情况下与一个整数进行相互转换的字符串。

所以，`"49"` 是一个整数属性名，因为我们把它转换成整数，再转换回来，它还是一样的。但是 “+49” 和 “1.2” 就不行了：

```javascript
// Number(...) 显式转换为数字
// Math.trunc 是内建的去除小数部分的方法。
alert( String(Math.trunc(Number("49"))) ); // "49"，相同，整数属性
alert( String(Math.trunc(Number("+49"))) ); // "49"，不同于 "+49" ⇒ 不是整数属性
alert( String(Math.trunc(Number("1.2"))) ); // "1"，不同于 "1.2" ⇒ 不是整数属性
```

如果属性名不是整数，那它们就按照创建时的顺序来排序。

```javascript
let user = {
  name: "John",
  surname: "Smith"
};
user.age = 25; // 增加一个

for (let prop in user) {
  alert( prop ); // name, surname, age
}
```

所以，为了解决电话号码的问题，我们可以使用非整数属性名来欺骗程序。只需要给每个键名加 `"+"` 前缀就行了。

```javascript
let codes = {
  "+49": "Germany",
  "+41": "Switzerland",
  "+44": "Great Britain",
  // ..,
  "+1": "USA"
};

for (let code in codes) {
  alert( +code ); // 49, 41, 44, 1
}
```



### 对象引用和复制

对象与原始类型根本区别之一是，对象是“通过引用”存储和复制的，而原始类型：字符串、数字、布尔值等 —— 总是“作为一个整体”复制。

这里我们将 `message` 复制到 `phrase`：

```javascript
let message = "Hello!";
let phrase = message;
```

结果我们就有了两个独立的变量，每个都存储着字符串 `"Hello!"`。

但是，对象不是这样的。

**赋值了对象的变量存储的不是对象本身，而是对象在内存中的地址 —— 换句话说就是对该对象的“引用”。**

```javascript
let user = {
  name: "John"
};
```

实际中该对象被存储在内存中的某个位置，而变量 `user`保存的是对其的“引用”。

**当一个对象变量被复制 —— 引用被复制，而该对象自身并没有被复制。**

```javascript
let user = { name: "John" };

let admin = user; // 复制引用
```

现在我们有了两个变量，它们保存的都是对同一个对象的引用。

我们可以通过其中任意变量来访问该对象并修改它的内容：

```javascript
let user = { name: 'John' };
let admin = user;

admin.name = 'Pete'; // 通过 "admin" 引用来修改
alert(user.name); // 'Peter'
```

#### 通过引用来比较

仅当两个对象为同一对象时，两者才相等。

例如，这里 `a` 和 `b` 两个变量都引用同一个对象，所以它们相等：

```javascript
let a = {};
let b = a; // 复制引用

alert( a == b ); // true
alert( a === b ); // true
```

而这里两个独立的对象则并不相等，即使它们看起来很像（都为空）：

```javascript
let a = {};
let b = {}; // 两个独立的对象

alert( a == b ); // false
alert( a === b ); // false
```

#### 克隆与合并

复制一个对象：创建一个新对象，通过遍历已有对象的属性，并在原始类型值的层面复制它们。

```javascript
let user = {
  name: "John",
  age: 30
};

let clone = {}; // 新的空对象

for (let key in user) {
  clone[key] = user[key];
}

clone.name = "Pete"; // 改变其中的数据
alert( user.name ); // 'John'
```

我们也可以使用 `Object.assign` 方法来达成同样的效果。

```javascript
Object.assign(dest, [src1, src2, src3...])
```

- 第一个参数 `dest` 是指目标对象。
- 更后面的参数 `src1, ..., srcN`（可按需传递多个参数）是源对象。
- 该方法将所有源对象的属性拷贝到目标对象 `dest` 中。换句话说，从第二个开始的所有参数的属性都被拷贝到第一个参数的对象中。
- 调用结果返回 `dest`。

例如，我们可以用它来合并多个对象：

```javascript
let user = { name: "John" };

let permissions1 = { canView: true };
let permissions2 = { canEdit: true };

Object.assign(user, permissions1, permissions2);

// user = { name: "John", canView: true, canEdit: true }
```

如果被拷贝的属性的属性名已经存在，那么它会被覆盖：

```javascript
let user = { name: "John" };

Object.assign(user, { name: "Pete" });

alert(user.name); // user = { name: "Pete" }
```

我们也可以用 `Object.assign` 代替 `for..in` 循环来进行简单克隆：

```javascript
let user = {
  name: "John",
  age: 30
};

let clone = Object.assign({}, user);
```

它将 `user` 中的所有属性拷贝到了一个空对象中，并返回这个新的对象。

#### 深层克隆

属性可以是对其他对象的引用。

```javascript
let user = {
  name: "John",
  sizes: {
    height: 182,
    width: 50
  }
};

alert( user.sizes.height ); // 182
```

现在这样拷贝 `clone.sizes = user.sizes` 已经不足够了，因为 `user.sizes` 是个对象，所以是“浅拷贝”。

为了解决这个问题，并让 `user` 和 `clone` 成为两个真正独立的对象，我们应该使用一个拷贝循环来检查 `user[key]` 的每个值，如果它是一个对象，那么也复制它的结构。这就是所谓的“深拷贝”。

我们可以使用递归来实现它。或者为了不重复造轮子，采用现有的实现，例如 lodash 库的 _.cloneDeep(obj) 。

**使用 const 声明的对象也是可以被修改的**

通过引用对对象进行存储的一个重要的副作用是声明为 `const` 的对象可以被修改。

```javascript
const user = {
  name: "John"
};

user.name = "Pete";

alert(user.name); // Pete
```

只有当我们尝试将 `user=...` 作为一个整体进行赋值时才会报错。



### 垃圾回收

#### 可达性（Reachability）

JavaScript 中主要的内存管理概念是可达性。

简而言之，“可达”值是那些以某种方式可访问或可用的值。它们被存储在内存中。

这里列出固有的可达值的基本集合，这些值明显不能被释放。

- 当前执行的函数，它的局部变量和参数。
- 当前嵌套调用链上的其他函数、它们的局部变量和参数。
- 全局变量。

这些值被称作 **根（roots）**。

如果一个值可以从根通过引用或者引用链进行访问，则认为该值是可达的。

在 JavaScript 引擎中有一个被称作 垃圾回收器 的东西在后台执行，它监控所有对象的状态，删除掉那些已经不可达的。

这里是一个最简单的例子：

```javascript
// user 具有对这个对象的引用
let user = {
  name: "John"
};
```

这里的箭头描述了一个对象引用。全局变量 `user` 引用了对象 `{name："John"}`。

如果 `user` 的值被重写了，这个引用就没了：

```javascript
user = null;
```

现在 John 变成不可达的了。因为没有引用了，就不能访问到它了。垃圾回收器会认为它是垃圾数据并进行回收，然后释放内存。

那么如果把 `user` 的引用复制给 `admin`：

```javascript
let user = {
  name: "John"
};
let admin = user;
```

现在执行刚刚的那个操作：

```javascript
user = null;
```

然后对象仍然可以被通过 `admin` 这个全局变量访问到，因此它必须被保留在内存中。如果我们又重写了 `admin`，对象就会被删除。

#### 相互关联的对象

现在来看一个更复杂的例子。这是个家庭：

```javascript
function marry(man, woman) {
  woman.husband = man;
  man.wife = woman;

  return {
    father: man,
    mother: woman
  }
}

let family = marry({name: "John"}, {name: "Ann"});
```

`marry` 函数通过让两个对象相互引用使它们“结婚”了，并返回了一个包含这两个对象的新对象。

到目前为止，所有对象都是可达的。

```javascript
delete family.father;
delete family.mother.husband;
```

现在所有的对象仍然都是可达的。

但是，如果我们把这两个都删除，那么我们可以看到再也没有对 John 的引用了。所以，John 现在是不可达的，并且将被从内存中删除，同时 John 的所有数据也将变得不可达。

#### 无法到达的岛屿

几个对象相互引用，但外部没有对其任意对象的引用，这些对象也可能是不可达的，并被从内存中删除。

```javascript
family = null;
```

显而易见，John 和 Ann 仍然连着，都有传入的引用。但是，这样还不够。

前面说的 `family` 对象已经不再与根相连，没有了外部对其的引用，所以它变成了一座“孤岛”，并且将被从内存中删除。

#### 内部算法

垃圾回收的基本算法被称为 “mark-and-sweep”。

定期执行以下“垃圾回收”步骤：

- 垃圾收集器找到所有的根，并“标记”（记住）它们。
- 然后它遍历并“标记”来自它们的所有引用。
- 然后它遍历标记的对象并标记它们的引用。所有被遍历到的对象都会被记住，以免将来再次遍历到同一个对象。
- ……如此操作，直到所有可达的（从根部）引用都被访问到。
- 没有被标记的对象都会被删除。

一些优化建议：

- **分代收集（Generational collection）**—— 对象被分成两组：“新的”和“旧的”。在典型的代码中，许多对象的生命周期都很短：它们出现、完成它们的工作并很快死去，因此在这种情况下跟踪新对象并将其从内存中清除是有意义的。那些长期存活的对象会变得“老旧”，并且被检查的频次也会降低。
- **增量收集（Incremental collection）**—— 如果有许多对象，并且我们试图一次遍历并标记整个对象集，则可能需要一些时间，并在执行过程中带来明显的延迟。因此，引擎将现有的整个对象集拆分为多个部分，然后将这些部分逐一清除。这样就会有很多小型的垃圾收集，而不是一个大型的。这需要它们之间有额外的标记来追踪变化，但是这样会带来许多微小的延迟而不是一个大的延迟。
- **闲时收集（Idle-time collection）**—— 垃圾收集器只会在 CPU 空闲时尝试运行，以减少可能对代码执行的影响。

> 深入学习：
> https://jayconrod.com/posts/55/a-tour-of-v8-garbage-collection



### 对象方法

在 JavaScript 中，行为（action）由属性中的函数来表示。

#### 方法示例

```javascript
let user = {
  name: "John",
  age: 30
};

user.sayHi = function() {
  alert("Hello!");
};

user.sayHi(); // Hello!
```

这里使用函数表达式创建了一个函数，并将其指定给对象的 `user.sayHi` 属性。

作为对象属性的函数被称为 **方法**。

所以，在这我们得到了 `user` 对象的 `sayHi` 方法。

当然，也可以使用预先声明的函数作为方法，就像这样：

```javascript
let user = {
  // ...
};

// 首先，声明函数
function sayHi() {
  alert("Hello!");
}

// 然后将其作为一个方法添加
user.sayHi = sayHi;
user.sayHi(); // Hello!
```

#### 方法简写

在对象字面量中，有一种更短的（声明）方法的语法：

```javascript
user = {
  sayHi: function() {
    alert("Hello");
  }
};

// 方法简写
let user = {
  sayHi() {
    alert("Hello");
  }
};
```

如上所示，我们可以省略 `"function"`，只写 `sayHi()`。

虽然这种表示法还是有些不同。在对象继承方面有一些细微的差别，但目前它们并不重要。在几乎所有的情况下，更短的语法是首选的。

#### 方法中的 “this”

`user.sayHi()` 中的代码可能需要用到 `user` 的 name 属性。

为了访问该对象，方法中可以使用 `this` 关键字。

`this` 的值就是在点之前的这个对象，即调用该方法的对象。

```javascript
let user = {
  name: "John",
  age: 30,

  sayHi() {
    // "this" 指的是“当前的对象”
    alert(this.name);
  }

};

user.sayHi(); // John
```

在这里 `user.sayHi()` 执行过程中，`this` 的值是 `user`。

技术上讲，也可以在不使用 `this` 的情况下，通过外部变量名来引用它。但这样的代码是不可靠的。如果前面进行了复制与替换的话，它将访问到错误的对象。

#### “this” 不受限制

在 JavaScript 中，`this` 关键字与其他大多数编程语言中的不同。JavaScript 中的 `this` 可以用于任何函数，即使它不是对象的方法。

```javascript
function sayHi() {
  alert( this.name );
}
```

`this` 的值是在代码运行时计算出来的，它取决于代码上下文。

例如，这里相同的函数被分配给两个不同的对象，在调用中有着不同的 “this” 值：

```javascript
let user = { name: "John" };
let admin = { name: "Admin" };

function sayHi() {
  alert( this.name );
}

user.f = sayHi;
admin.f = sayHi;

// 这两个调用有不同的 this 值
user.f(); // John（this == user）
admin.f(); // Admin（this == admin）

admin['f'](); // Admin（可以使用点符号或方括号语法来访问这个方法）
```

这个规则很简单：如果 `obj.f()` 被调用了，则 `this` 在 `f` 函数调用期间是 `obj`。所以在上面的例子中 this 先是 `user`，之后是 `admin`。

**在没有对象的情况下调用：`this == undefined`**

```javascript
function sayHi() {
  alert(this);
}

sayHi(); // undefined
```

在非严格模式的情况下，`this` 将会是 **全局对象**（浏览器中的 `window`）。这是一个历史行为，`"use strict"` 已经将其修复了。

#### 箭头函数没有 “this”

箭头函数有些特别：它们没有自己的 `this`。如果我们在这样的函数中引用 `this`，`this` 值取决于外部“正常的”函数。

举个例子，这里的 `arrow()` 使用的 `this` 来自于外部的 `user.sayHi()` 方法：

```javascript
let user = {
  firstName: "Ilya",
  sayHi() {
    let arrow = () => alert(this.firstName);
    arrow();
  }
};

user.sayHi(); // Ilya
```

这是箭头函数的一个特性，当我们并不想要一个独立的 `this`，反而想从外部上下文中获取时，它很有用。



### 构造器和 "new"

常规的 `{...}` 语法允许创建一个对象。如果要创建很多类似的对象，可以使用构造函数和 `"new"` 操作符来实现。

#### 构造函数

构造函数在技术上是常规函数。不过有两个约定：

1. 它们的命名以大写字母开头。
2. 它们只能由 `"new"` 操作符来执行。

```javascript
function User(name) {
  this.name = name;
  this.isAdmin = false;
}

let user = new User("Jack");

alert(user.name); // Jack
alert(user.isAdmin); // false
```

这是构造器的主要目的 —— 实现可重用的对象创建代码。

让我们再强调一遍 —— 从技术上讲，任何函数（除了箭头函数，它没有自己的 `this`）都可以用作构造器。即可以通过 `new` 来运行，它会执行上面的算法。“首字母大写”是一个共同的约定，以明确表示一个函数将被使用 `new` 来运行。

**new function() { … }**

如果我们有许多行用于创建单个复杂对象的代码，我们可以将它们封装在一个立即调用的构造函数中，像这样：

```javascript
// 创建一个函数并立即使用 new 调用它
let user = new function() {
  this.name = "John";
  this.isAdmin = false;

  // ……用于用户创建的其他代码
};
```

这个构造函数不能被再次调用，因为它不保存在任何地方，只是被创建和调用。因此，这个技巧旨在封装构建单个对象的代码，而无需将来重用。

#### 构造器模式测试：new.target

在一个函数内部，我们可以使用 `new.target` 属性来检查它是否被使用 `new` 进行调用了。

对于常规调用，它为 undefined，对于使用 `new` 的调用，则等于该函数：

```javascript
function User() {
  alert(new.target);
}

User(); // undefined
new User(); // function User { ... }
```

它可以被用在函数内部，判断该函数是通过 `new` 调用的“构造器模式”，还是“常规模式”。

我们也可以让 `new` 调用和常规调用做相同的工作，像这样：

```javascript
function User(name) {
  if (!new.target) {
    return new User(name); // ……我会给你添加 new
  }

  this.name = name;
}

let john = User("John"); // 将调用重定向到新用户
alert(john.name); // John
```

这种方法有时被用在库中以使语法更加灵活。这样人们在调用函数时，无论是否使用了 `new`，程序都能工作。

不过，到处都使用它并不是一件好事，因为省略了 `new` 使得很难观察到代码中正在发生什么。而通过 `new` 我们都可以知道这创建了一个新对象。

#### 构造器的 return

通常，构造器没有 `return` 语句。它们的任务是将所有必要的东西写入 `this`，并自动转换为结果。

但是，如果这有一个 `return` 语句，那么规则就简单了：

- 如果 `return` 返回的是一个对象，则返回这个对象，而不是 `this`。
- 如果 `return` 返回的是一个原始类型，则忽略。

换句话说，带有对象的 `return` 返回该对象，在所有其他情况下返回 `this`。

例如，这里 `return` 通过返回一个对象覆盖 `this`：

```javascript
function BigUser() {
  this.name = "John";
  return { name: "Godzilla" };  // <-- 返回这个对象
}

alert( new BigUser().name );  // Godzilla
```

这里有一个 `return` 为空的例子：

```javascript
function SmallUser() {
  this.name = "John";
  return;
}

alert( new SmallUser().name );  // John
```

通常构造器没有 `return` 语句。这里我们主要为了完整性而提及返回对象的特殊行为。

**省略括号**（不是好的风格）

```javascript
let user = new User; // <-- 没有参数
// 等同于
let user = new User();
```

#### 构造器中的方法

构造函数可能有一些参数，这些参数定义了如何构造对象以及要放入什么。

当然，不仅可以将属性添加到 `this` 中，还可以添加方法。

```javascript
function User(name) {
  this.name = name;

  this.sayHi = function() {
    alert( "My name is: " + this.name );
  };
}

let john = new User("John");
john.sayHi(); // My name is: John
```



### 可选链 "?."

可选链 `?.` 是一种访问嵌套对象属性的安全的方式。即使中间的属性不存在，也不会出现错误。

#### 不存在的属性

假设有很多个 `user` 对象，大多数用户的地址都存储在 `user.address` 中，街道地址存储在 `user.address.street` 中，但有些用户没有提供这些信息。

在这种情况下，当我们尝试获取 `user.address.street`，而该用户恰好没提供地址信息，我们则会收到一个错误：

```javascript
let user = {}; // 一个没有 "address" 属性的 user 对象

alert(user.address.street); // Error!
```

这是预期的结果。JavaScript 的工作原理就是这样的。因为 `user.address` 为 `undefined`，尝试读取 `user.address.street` 会失败，并收到一个错误。

但是在很多实际场景中，我们更希望得到的是 `undefined`（表示没有 `street` 属性）而不是一个错误。

在 Web 开发中，我们可以使用特殊的方法调用（例如 `document.querySelector('.elem')`）以对象的形式获取一个网页元素，如果没有这种对象，则返回 `null`。

```javascript
// 如果 document.querySelector('.elem') 的结果为 null，则这里不存在这个元素
let html = document.querySelector('.elem').innerHTML; // 会出现错误
```

同样，如果该元素不存在，则访问 `null` 的 `.innerHTML` 属性时会报错。在某些情况下，当元素的缺失是没问题的时候，我们希望避免出现这种错误，而是接受 `html = null` 作为结果。

可能最先想到的方案是在访问该值的属性之前，使用 `if` 或条件运算符 `?` 对该值进行检查，像这样：

```javascript
let user = {};

alert(user.address ? user.address.street : undefined);
```

这样可以，但是不够优雅。对于嵌套层次更深的属性，代码会变得更丑，因为需要更多的重复。

例如，让我们以相同的方式尝试获取 `user.address.street.name`。

```javascript
let user = {}; // user 没有 address 属性

alert(user.address ? user.address.street ? user.address.street.name : null : null);
```

这样就太难看了，并且这可能导致写出来的代码很难让别人理解。

这里有一种更好的实现方式，就是使用 `&&` 运算符：

```javascript
let user = {}; // user 没有 address 属性

alert( user.address && user.address.street && user.address.street.name ); // undefined（不报错）
```

依次对整条路径上的属性使用与运算进行判断，以确保所有节点是存在的（如果不存在，则停止计算），这是一种比较常见的用法，它比可选链的容错更高，但不是最优雅的。

这就是为什么可选链 `?.` 被加入到了 JavaScript 这门编程语言中。那就是彻底地解决以上所有问题！

#### 可选链

如果可选链 `?.` 前面的值为 `undefined` 或者 `null`，它会停止运算并返回 `undefined`。（短路效应）

换句话说，例如 `value?.prop`：

- 如果 `value` 存在，则结果与 `value.prop` 相同，
- 否则（当 `value` 为 `undefined/null` 时）则返回 `undefined`。

下面这是一种使用 `?.` 安全地访问 `user.address.street` 的方式：

```javascript
let user = {}; // user 没有 address 属性
alert( user?.address?.street ); // undefined
```

这里是一个结合 `document.querySelector` 使用的示例：

```javascript
let html = document.querySelector('.elem')?.innerHTML; // 如果没有符合的元素，则为 undefined
```

即使 对象 `user` 不存在，使用 `user?.address` 来读取地址也没问题：

```javascript
let user = null;

alert( user?.address ); // undefined
alert( user?.address.street ); // undefined
```

`?.` 语法使其前面的值成为可选值，但不会对其后面的起作用。

**不要过度使用可选链**

尽量将 `?.` 使用在一些东西可以不存在的地方，如果某个属性必须存在就不应该使用，否则看不到报错，导致调试更加困难。

**变量必须已声明**

如果未声明变量 `user`，那么 `user?.anything` 会触发一个错误：

```javascript
// ReferenceError: user is not defined
user?.address;
```

#### 其它变体

可选链 `?.` 不是一个运算符，而是一个特殊的语法结构。它还可以与函数和方括号一起使用。

在下面这段代码中，有些用户具有 `admin` 方法，而有些没有：

```javascript
let userAdmin = {
  admin() {
    alert("I am admin");
  }
};

let userGuest = {};

userAdmin.admin?.(); // I am admin
userGuest.admin?.(); // nothing happen
```

在这两行代码中，我们首先使用点符号（`userAdmin.admin`）来获取 `admin` 属性，因为我们假定对象 `userAdmin` 存在，因此可以安全地读取它。

然后 `?.()` 会检查它左边的部分：如果 `admin` 函数存在，那么就调用运行它（对于 `userAdmin`）。否则（对于 `userGuest`）运算停止，没有报错。

 `?.[]` 也可以使用，它允许从一个可能不存在的对象上安全地读取属性。

此外，我们还可以将 `?.` 跟 `delete` 一起使用：

```javascript
delete user?.name; // 如果 user 存在，则删除 user.name
```

可以用 `?.` 来安全地读取或删除，但不能用来写入（不能用出现在赋值语句的左侧）



### symbol 类型

“symbol” 值表示唯一的标识符。

可以使用 `Symbol()` 来创建这种类型的值：

```javascript
let id = Symbol();
```

创建时，我们可以给 symbol 一个描述（也称为 symbol 名），这在代码调试时非常有用：

```javascript
// id 是描述为 "id" 的 symbol
let id = Symbol("id");
```

symbol 保证是唯一的。

```javascript
let id1 = Symbol("id");
let id2 = Symbol("id");
alert(id1 == id2); // false
```

JavaScript 的 symbol和 Ruby 或者其他有 “symbol” 的语言是不同的。

**symbol 不会被自动转换为字符串**

JavaScript 中的大多数值都支持字符串的隐式转换。例如，我们可以 `alert` 任何值，都可以生效。symbol 比较特殊，它不会被自动转换。

例如，这个 `alert` 将会提示出错：

```javascript
let id = Symbol("id");
alert(id); // 类型错误：无法将 symbol 值转换为字符串。
```

这是一种防止混乱的“语言保护”，因为字符串和 symbol 有本质上的不同，不应该意外地将它们转换成另一个。

如果我们真的想显示一个 symbol，我们需要在它上面调用 `.toString()`，如下所示：

```javascript
let id = Symbol("id");
alert(id.toString()); // Symbol(id)，现在它有效了
```

或者获取 `symbol.description` 属性，只显示描述（description）：

```javascript
let id = Symbol("id");
alert(id.description); // id
```

#### “隐藏”属性

symbol 允许我们创建对象的“隐藏”属性，代码的任何其他部分都不能意外访问或重写这些属性。

例如，如果我们使用的是属于第三方代码的 `user` 对象，我们想要给它们添加一些标识符。

我们可以给它们使用 symbol 键：

```javascript
let user = { // 属于另一个代码
  name: "John"
};

let id = Symbol("id");

user[id] = 1;

alert( user[id] ); // 我们可以使用 symbol 作为键来访问数据
```

使用 `Symbol("id")` 作为键，比起用字符串 `"id"` 来有什么好处呢？

由于 `user` 对象属于另一个代码库，所以向它们添加字段是不安全的，因为我们可能会影响代码库中的其他预定义行为。但 symbol 属性不会被意外访问到。第三方代码不会知道新定义的 symbol，因此将 symbol 添加到 `user` 对象是安全的。

标识符之间不会有冲突，因为 symbol 总是不同的，即使它们有相同的名字。

但如果我们出于同样的目的，使用字符串 `"id"` 而不是用 symbol，那么就会出现冲突：

```javascript
let user = { name: "John" };
// our script
user.id = "Our id value";
// another script
user.id = "Their id value"
// pong！无意中被另一个脚本重写了 id！
```

#### 对象字面量中的 symbol

如果我们要在对象字面量 `{...}` 中使用 symbol，则需要使用方括号把它括起来。

就像这样：

```javascript
let id = Symbol("id");

let user = {
  name: "John",
  [id]: 123
};
```

#### symbol 在 for…in 中会被跳过

symbol 属性不参与 `for..in` 循环。

`Object.keys(user)`也会忽略它们。这是一般“隐藏符号属性”原则的一部分。如果另一个脚本或库遍历我们的对象，它不会意外地访问到符号属性。

但是`Object.assign`会同时复制字符串和 symbol 属性：

```javascript
let id = Symbol("id");
let user = {
  [id]: 123
};

let clone = Object.assign({}, user);
alert( clone[id] ); // 123
```

#### 全局 symbol

正如我们所看到的，通常所有的 symbol 都是不同的，即使它们有相同的名字。但有时我们想要名字相同的 symbol 具有相同的实体。例如，应用程序的不同部分想要访问的 symbol `"id"` 指的是完全相同的属性。

为了实现这一点，这里有一个 **全局 symbol 注册表**。我们可以在其中创建 symbol 并在稍后访问它们，它可以确保每次访问相同名字的 symbol 时，返回的都是相同的 symbol。

要从注册表中读取（不存在则创建）symbol，请使用 `Symbol.for(key)`。

该调用会检查全局注册表，如果有一个描述为 `key` 的 symbol，则返回该 symbol，否则将创建一个新 symbol，并通过给定的 `key` 将其存储在注册表中。

```javascript
// 从全局注册表中读取
let id = Symbol.for("id"); // 如果该 symbol 不存在，则创建它
let idAgain = Symbol.for("id");
alert( id === idAgain ); // true
```

注册表内的 symbol 被称为 **全局 symbol**。如果我们想要一个应用程序范围内的 symbol，可以在代码中随处访问。

**这听起来像 Ruby**

#### Symbol.keyFor

我们已经看到，对于全局 symbol，`Symbol.for(key)` 按名字返回一个 symbol。相反，通过全局 symbol 返回一个名字，我们可以使用 `Symbol.keyFor(sym)`：

```javascript
// 通过 name 获取 symbol
let sym = Symbol.for("name");
let sym2 = Symbol.for("id");

// 通过 symbol 获取 name
alert( Symbol.keyFor(sym) ); // name
alert( Symbol.keyFor(sym2) ); // id
```

`Symbol.keyFor` 内部使用全局 symbol 注册表来查找 symbol 的键。所以它不适用于非全局 symbol。如果 symbol 不是全局的，它将无法找到它并返回 `undefined`。

但是，所有 symbol 都具有 `description` 属性。

```javascript
let globalSymbol = Symbol.for("name");
let localSymbol = Symbol("name");

alert( Symbol.keyFor(globalSymbol) ); // name
alert( Symbol.keyFor(localSymbol) ); // undefined

alert( localSymbol.description ); // name
```

#### 系统 symbol

JavaScript 内部有很多“系统” symbol，我们可以使用它们来微调对象的各个方面。

它们都被列在了众所周知的 symbol 表的规范中：

- `Symbol.hasInstance`

- `Symbol.isConcatSpreadable`

- `Symbol.iterator`

- `Symbol.toPrimitive`

  ……等等。



### 对象 —— 原始值转换

#### hint

类型转换在各种情况下有三种变体。它们被称为 “hint”，在 [规范](https://tc39.github.io/ecma262/#sec-toprimitive) 所述：

`"string"`

对象到字符串的转换，当我们对期望一个字符串的对象执行操作时，如 “alert”：

```js
// 输出
alert(obj);

// 将对象作为属性键
anotherObj[obj] = 123;
```

`"number"`

对象到数字的转换，例如当我们进行数学运算时：

```js
// 显式转换
let num = Number(obj);

// 数学运算（除了二元加法）
let n = +obj; // 一元加法
let delta = date1 - date2;

// 小于/大于的比较
let greater = user1 > user2;
```

还有像 `<` 和 `>` 这样的比较运算符

`"default"`

在少数情况下发生，当运算符“不确定”期望值的类型时。

例如，二元加法 `+` 可用于字符串（连接），也可以用于数字（相加）。因此，当二元加法得到对象类型的参数时，它将依据 `"default"` hint 来对其进行转换。

此外，如果对象被用于与字符串、数字或 symbol 进行 `==` 比较，这时到底应该进行哪种转换也不是很明确，因此使用 `"default"` hint。

```js
// 二元加法使用默认 hint
let total = obj1 + obj2;

// obj == number 使用默认 hint
if (user == 1) { ... };
```

**为了进行转换，JavaScript 尝试查找并调用三个对象方法：**

1. 调用 `obj[Symbol.toPrimitive](hint)` —— 带有 symbol 键 `Symbol.toPrimitive`（系统 symbol）的方法，如果这个方法存在的话，
2. 否则，如果 hint 是 `"string"` —— 尝试调用 `obj.toString()` 或 `obj.valueOf()`，无论哪个存在。
3. 否则，如果 hint 是 `"number"` 或 `"default"` —— 尝试调用 `obj.valueOf()` 或 `obj.toString()`，无论哪个存在。

#### Symbol.toPrimitive

我们从第一个方法开始。有一个名为 `Symbol.toPrimitive` 的内建 symbol，它被用来给转换方法命名，像这样：

```javascript
obj[Symbol.toPrimitive] = function(hint) {
  // 这里是将此对象转换为原始值的代码
  // hint = "string"、"number" 或 "default"
}
```

如果 `Symbol.toPrimitive` 方法存在，则它会被用于所有 hint，无需更多其他方法。

例如，这里 `user` 对象实现了它：

```javascript
let user = {
  name: "John",
  money: 1000,

  [Symbol.toPrimitive](hint) {
    alert(`hint: ${hint}`);
    return hint == "string" ? `{name: "${this.name}"}` : this.money;
  }
};

alert(user); // hint: string -> {name: "John"}
alert(+user); // hint: number -> 1000
alert(user + 500); // hint: default -> 1500
```

从代码中我们可以看到，根据转换的不同，`user` 变成一个自描述字符串或者一个金额。`user[Symbol.toPrimitive]` 方法处理了所有的转换情况。

#### toString/valueOf

如果没有 `Symbol.toPrimitive`，引擎将尝试寻找 `toString` 和 `valueOf` 方法：

- 对于 `"string"` hint：调用 `toString` 方法，如果它不存在，则调用 `valueOf` 方法。
- 其他 hint：调用 `valueOf` 方法，如果它不存在，则调用 `toString` 方法（对于数学运算，优先调用 `valueOf` 方法）。

这些方法必须返回一个原始值。如果 `toString` 或 `valueOf` 返回了一个对象，那么返回值会被忽略。

默认情况下，普通对象具有 `toString` 和 `valueOf` 方法：

- `toString` 方法返回一个字符串 `"[object Object]"`。
- `valueOf` 方法返回对象自身。

```javascript
let user = {name: "John"};

alert(user); // [object Object]
alert(user.valueOf() === user); // true
```

默认的 `valueOf` 返回对象本身，会被忽略，所以相当于不存在。

例如，这里的 `user` 执行和前面提到的那个 `user` 一样的操作，使用 `toString` 和 `valueOf` 的组合（而不是 `Symbol.toPrimitive`）：

```javascript
let user = {
  name: "John",
  money: 1000,

  // 对于 hint="string"
  toString() {
    return `{name: "${this.name}"}`;
  },

  // 对于 hint="number" 或 "default"
  valueOf() {
    return this.money;
  }
};

alert(user); // toString -> {name: "John"}
alert(+user); // valueOf -> 1000
alert(user + 500); // valueOf -> 1500
```

如果希望有一个地方处理所有原始转换，可以只实现 `toString`：

```javascript
let user = {
  name: "John",

  toString() {
    return this.name;
  }
};

alert(user); // toString -> John
alert(user + 500); // toString -> John500
```

如果没有 `Symbol.toPrimitive` 和 `valueOf`，`toString` 将处理所有原始转换。

#### 转换可以返回任何原始类型

关于所有原始转换方法，有一个重要的点需要知道，就是它们不一定会返回 “hint” 的原始值。

唯一强制性的事情是：这些方法必须返回原始值，而不是对象。

由于历史原因， `toString` 或 `valueOf` 返回对象并不会出现 error，但是这种值会被忽略。这是因为在 JavaScript 语言发展初期，没有很好的 “error” 的概念。

`Symbol.toPrimitive` 更严格，它必须返回一个原始值，否则就会出现 error。

#### 多次转换

我们已经知道，许多运算符和函数执行类型转换，例如乘法 `*` 将操作数转换为数字。

如果我们将对象作为参数传递，则会出现两个运算阶段：

1. 对象被转换为原始值（通过前面我们描述的规则）。
2. 如果还需要进一步计算，则生成的原始值会被进一步转换。

```javascript
let obj = {
  toString() {
    return "2";
  }
};
alert(obj * 2); // 4
```

二元加法在同样的情况下会将其连接成字符串，因为它更愿意接受字符串：

```javascript
let obj = {
  toString() {
    return "2";
  }
};
alert(obj + 2); // 22（"2" + 2）被转换为原始值字符串 => 级联
```



## 数据类型

### 原始类型的方法

JavaScript 允许我们像使用对象一样使用原始类型（字符串，数字等）。

原始类型和对象之间的关键区别：

原始值：是原始类型中的一种值（7 种）。
对象：能够存储多个值作为属性。

对象的一个优势是可以把函数作为对象的属性存储到对象中。

```javascript
let john = {
  name: "John",
  sayHi: function() {
    alert("Hi buddy!");
  }
};

john.sayHi(); // Hi buddy!
```

许多内建对象已经存在，例如那些处理日期、错误、HTML 元素等的内建对象。它们具有不同的属性和方法。

但是，这些特性（feature）都是有成本的！

#### 当作对象的原始类型

以下是 JavaScript 创建者面临的悖论：

- 人们可能想对诸如字符串或数字之类的原始类型执行很多操作。最好使用方法来访问它们。
- 原始类型必须尽可能的简单轻量。

而解决方案看起来多少有点尴尬：

1. 原始类型仍然是原始的。与预期相同，提供单个值
2. JavaScript 允许访问字符串，数字，布尔值和 symbol 的方法和属性。
3. 为了使它们起作用，创建了提供额外功能的特殊“对象包装器”，使用后即被销毁。

“对象包装器”对于每种原始类型都是不同的，它们被称为 `String`、`Number`、`Boolean`、`Symbol` 和 `BigInt`。因此，它们提供了不同的方法。

例如，字符串方法`str.toUpperCase()` 返回一个大写化处理的字符串。

```javascript
let str = "Hello";
alert( str.toUpperCase() ); // HELLO
```

 `str.toUpperCase()` 中实际发生的情况：

1. 字符串 `str` 是一个原始值。因此，在访问其属性时，会创建一个包含字符串字面值的特殊对象，并且具有可用的方法，例如 `toUpperCase()`。
2. 该方法运行并返回一个新的字符串（由 `alert` 显示）。
3. 特殊对象被销毁，只留下原始值 `str`。

所以原始类型可以提供方法，但它们依然是轻量级的。

JavaScript 引擎高度优化了这个过程。它甚至可能跳过创建额外的对象。但是它仍然必须遵守规范，并且表现得好像它创建了一样。

**构造器 `String/Number/Boolean` 仅供内部使用**

像 Java 这样的一些语言允许我们使用 `new Number(1)` 或 `new Boolean(false)` 等语法，明确地为原始类型创建“对象包装器”。

在 JavaScript 中是可以的，但极其 **不推荐**。因为这样会出问题。

```javascript
alert( typeof 0 ); // "number"
alert( typeof new Number(0) ); // "object"!
```

对象在 `if` 中始终为真，所以此处的 alert 将显示：

```javascript
let zero = new Number(0);
if (zero) { // zero 为 true，因为它是一个对象
  alert( "zero is truthy?!?" );
}
```

另一方面，调用不带 `new`（关键字）的 `String/Number/Boolean` 函数是可以的且有效的。它们将一个值转换为相应的类型：转成字符串、数字或布尔值（原始类型）。

```javascript
let num = Number("123"); // 将字符串转成数字
```

**null/undefined 没有任何方法**

特殊的原始类型 `null` 和 `undefined` 是例外。它们没有对应的“对象包装器”，也没有提供任何方法。从某种意义上说，它们是“最原始的”。



### 数字类型

在现代 JavaScript 中，数字（number）有两种类型：

1. JavaScript 中的常规数字以 64 位的格式 IEEE-754 存储，也被称为“双精度浮点数”。
2. BigInt 用于表示任意长度的整数。

#### 编写数字的更多方法

假如我们需要表示 10 亿。显然，可以这样写：

```javascript
let billion = 1000000000;
```

我们也可以使用下划线 `_` 作为分隔符：

```javascript
let billion = 1_000_000_000;
```

这里的下划线 `_` 扮演了“语法糖”的角色，使得数字具有更强的可读性。引擎会直接忽略数字之间的 `_`

在 JavaScript 中可以通过在数字后面附加字母 `"e"` 并指定零的个数来缩短数字：

```javascript
let billion = 1e9;  // 10 亿
alert( 7.3e9 );  // 73 亿
```

也可以使用 `"e"` 来表示很小的数字：

```javascript
let mcs = 1e-6; // 0.000001;
```

#### 十六进制，二进制和八进制数字

十六进制数字在 JavaScript 中被广泛用于表示颜色，编码字符等。

```javascript
alert( 0xff ); // 255
alert( 0xFF ); // 255
```

二进制和八进制数字系统很少使用，但也支持使用 `0b` 和 `0o` 前缀：

```javascript
let a = 0b11111111; // 255
let b = 0o377; // 255
alert( a == b ); // true
```

只有这三种进制支持这种写法。对于其他进制，应使用函数 `parseInt`。

#### toString(base)

方法 `num.toString(base)` 返回在给定 `base` 进制数字系统中 `num` 的字符串表示形式。

举个例子：

```javascript
let num = 255;

alert( num.toString(16) );  // ff
alert( num.toString(2) );   // 11111111
```

`base` 的范围可以从 `2` 到 `36`。默认情况下是 `10`。

base=36 是最大进制，数字可以是 `0..9` 或 `a..z`。所有拉丁字母都被用于了表示数字。当我们需要将一个较长的数字标识符转换成较短的时候，例如做一个短的 URL。可以简单地使用基数为 `36` 的数字系统表示：

```javascript
alert( 123456..toString(36) ); // 2n9c
```

**使用两个点来调用一个方法**

如果想直接在数字上调用方法，比如上面例子中的 `toString`，只需在后面放置两个点 `..`（一个点会被引擎错误地认为是小数部分）。

也可以写成 `(123456).toString(36)`。

#### 舍入

舍入（rounding）是使用数字时最常用的操作之一。

| 方法         | 核心逻辑                 | 3.1  | 3.6  | -1.1 | -1.9 |
| ------------ | ------------------------ | ---- | ---- | ---- | ---- |
| Math.floor() | 向下取整                 | 3    | 3    | -2   | -2   |
| Math.ceil()  | 向上取整                 | 4    | 4    | -1   | -1   |
| Math.round() | 四舍五入（取最近的整数） | 3    | 4    | -1   | -2   |
| Math.trunc() | 截断（直接扔掉小数部分） | 3    | 3    | -1   | -1   |

但是，如果我们想将数字舍入到小数点后 `n` 位，该怎么办？

有两种方式可以实现这个需求：

1. 乘除法

   例如，要将数字舍入到小数点后两位，我们可以将数字乘以 `100`，调用舍入函数，然后再将其除回。

   ```javascript
   let num = 1.23456;
   alert( Math.round(num * 100) / 100 ); // 1.23456 -> 123.456 -> 123 -> 1.23
   ```

2. 函数 toFixed(n) 将数字舍入到小数点后 `n` 位，并以字符串形式返回结果。如果小数部分比所需要的短，则在结尾添加零

   ```javascript
   let num = 12.34;
   alert( num.toFixed(1) ); // "12.3"
   alert( num.toFixed(5) ); // "12.34000"
   ```

   我们可以使用一元加号或 `Number()` 调用，将其转换为数字，例如 `+ num.toFixed(5)`。

#### 不精确的计算

在内部，数字是以 64 位格式 IEEE-754 表示的，所以正好有 64 位可以存储一个数字：其中 52 位被用于存储这些数字，其中 11 位用于存储小数点的位置，而 1 位用于符号。

如果一个数字真的很大，则可能会溢出 64 位存储，变成一个特殊的数值 `Infinity`：

```javascript
alert( 1e500 ); // Infinity
```

这可能不那么明显，但经常会发生的是，精度的损失。

没错，如果我们检查 `0.1` 和 `0.2` 的总和是否为 `0.3`，我们会得到 `false`。

```javascript
alert( 0.1 + 0.2 == 0.3 ); // false
alert( 0.1 + 0.2 ); // 0.30000000000000004
```

在十进制数字系统中，可以保证以 `10` 的整数次幂作为除数能够正常工作，但是以 `3` 作为除数则不能。同样的，在二进制中，可以保证以 `2` 的整数次幂作为除数时能够正常工作，但 `1/10` 就变成了一个无限循环的二进制小数。

使用二进制数字系统无法精确存储 0.1 或 0.2。IEEE-754 数字格式通过将数字舍入到最接近的可能数字来解决此问题。这些舍入规则通常不允许我们看到“极小的精度损失”，但是它确实存在。

我们可以看到：

```javascript
alert( 0.1.toFixed(20) ); // 0.10000000000000000555
```

当我们对两个数字进行求和时，它们的“精度损失”会叠加起来。

**不仅仅是 JavaScript**

许多其他编程语言也存在同样的问题。PHP，Java，C，Perl，Ruby 给出的也是完全相同的结果，因为它们基于的是相同的数字格式。

我们能解决这个问题吗？当然，最可靠的方法是借助方法 toFixed(n) 对结果进行舍入：

```javascript
let sum = 0.1 + 0.2;
alert( sum.toFixed(2) ); // "0.30"
alert( +sum.toFixed(2) ); // 0.3
```

我们可以将数字临时乘以 100（或更大的数字），将其转换为整数，进行数学运算，然后再除回。当我们使用整数进行数学运算时，误差会有所减少，但仍然可以在除法中得到：

```javascript
alert( (0.1 * 10 + 0.2 * 10) / 10 ); // 0.3
alert( (0.28 * 100 + 0.14 * 100) / 100); // 0.4200000000000001
```

乘/除法可以减少误差，但不能完全消除误差。

#### isFinite 和 isNaN

还记得这两个特殊的数值吗？

- `Infinity`（和 `-Infinity`）是一个特殊的数值，比任何数值都大（小）。
- `NaN` 代表一个 error。

它们属于 `number` 类型，但不是“普通”数字，因此，这里有用于检查它们的特殊函数：

- `isNaN(value)` 将其参数转换为数字，然后测试它是否为 `NaN`：

  ```javascript
  alert( isNaN(NaN) ); // true
  alert( isNaN("str") ); // true
  ```

  但是我们需要这个函数吗？我们不能只使用 `=== NaN` 比较吗？很不幸，这不行。值 “NaN” 是独一无二的，它不等于任何东西，包括它自身：

  ```javascript
  alert( NaN === NaN ); // false
  ```

- `isFinite(value)` 将其参数转换为数字，如果是常规数字而不是 `NaN/Infinity/-Infinity`，则返回 `true`：

  ```javascript
  alert( isFinite("15") ); // true
  alert( isFinite("str") ); // false，NaN
  alert( isFinite(Infinity) ); // false，Infinity
  ```

有时 `isFinite` 被用于验证字符串值是否为常规数字：

```javascript
let num = +prompt("Enter a number", '');
alert( isFinite(num) );
```

请注意，在所有数字函数中，包括 `isFinite`，空字符串或仅有空格的字符串均被视为 `0`。

**与 `Object.is` 进行比较**

有一个特殊的内建方法 `Object.is`，它类似于 `===` 一样对值进行比较，但它对于两种边缘情况更可靠：

1. 它适用于 `NaN`：`Object.is(NaN, NaN) === true`，这是件好事。
2. 值 `0` 和 `-0` 是不同的：`Object.is(0, -0) === false`，从技术上讲这是对的，因为在内部，数字的符号位可能会不同，即使其他所有位均为零。

在所有其他情况下，`Object.is(a, b)` 与 `a === b` 相同。

#### parseInt 和 parseFloat

使用加号 `+` 或 `Number()` 的数字转换是严格的。如果一个值不完全是一个数字，就会失败：

```javascript
alert( +"100px" ); // NaN
```

唯一的例外是字符串开头或结尾的空格，因为它们会被忽略。

但在现实生活中，我们经常会有带有单位的值，例如 CSS 中的 `"100px"` 或 `"12pt"`。并且，在很多国家，货币符号是紧随金额之后的，所以我们有 `"19€"`，并希望从中提取出一个数值。

这就是 `parseInt` 和 `parseFloat` 的作用。

它们可以从字符串中“读取”数字，直到无法读取为止。如果发生 error，则返回收集到的数字。函数 `parseInt` 返回一个整数，而 `parseFloat` 返回一个浮点数：

```javascript
alert( parseInt('100px') ); // 100
alert( parseFloat('12.5em') ); // 12.5

alert( parseInt('12.3') ); // 12，只有整数部分被返回了
alert( parseFloat('12.3.4') ); // 12.3，在第二个点出停止了读取
```

某些情况下，`parseInt/parseFloat` 会返回 `NaN`。当没有数字可读时会发生这种情况：

```javascript
alert( parseInt('a123') ); // NaN，第一个符号停止了读取
```

**parseInt(str, radix) 的第二个参数**

`parseInt()` 函数具有可选的第二个参数。它指定了数字系统的基数，因此 `parseInt` 还可以解析十六进制数字、二进制数字等的字符串：

```javascript
alert( parseInt('0xff', 16) ); // 255
alert( parseInt('ff', 16) ); // 255，没有 0x 仍然有效

alert( parseInt('2n9c', 36) ); // 123456
```

#### 其他数学函数

JavaScript 有一个内建的 Math 对象，它包含了一个小型的数学函数和常量库（和python中的用法基本一致）。



### 字符串

在 JavaScript 中，文本数据被以字符串形式存储，单个字符没有单独的类型。

字符串的内部格式始终是 UTF-16，它不依赖于页面编码。

#### 引号（Quotes）

字符串可以包含在单引号、双引号或反引号中：

```javascript
let single = 'single-quoted';
let double = "double-quoted";
let backticks = `backticks`;
```

单引号和双引号基本相同。反引号允许我们通过 `${…}` 将表达式嵌入到字符串中：

```javascript
function sum(a, b) {
  return a + b;
}
alert(`1 + 2 = ${sum(1, 2)}.`); // 1 + 2 = 3.
```

使用反引号的另一个优点是它们允许字符串跨行：

```javascript
let guestList = `Guests:
 * John
 * Pete
 * Mary
`;

alert(guestList); // 客人清单，多行
```

如果我们使用单引号或双引号来实现字符串跨行的话，则会出现错误：

```javascript
let guestList = "Guests: // Error: Unexpected token ILLEGAL
  * John";
```

单引号和双引号来自语言创建的古老时代，当时没有考虑到多行字符串的需要。反引号出现较晚，因此更通用。

#### 特殊字符

我们仍然可以通过使用“换行符（newline character）”，以支持使用单引号和双引号来创建跨行字符串。换行符写作 `\n`，用来表示换行：

```javascript
let guestList = "Guests:\n * John\n * Pete\n * Mary";
alert(guestList); // 一个多行的客人列表
```

例如，这两行描述的是一样的，只是书写方式不同：

```javascript
let str1 = "Hello\nWorld";
let str2 = `Hello
World`;

alert(str1 == str2); // true
```

转义字符：

| 字符                                    | 描述                                                         |
| :-------------------------------------- | :----------------------------------------------------------- |
| `\n`                                    | 换行                                                         |
| `\r`                                    | 在 Windows 文本文件中，两个字符 `\r\n` 的组合代表一个换行。而在非 Windows 操作系统上，它就是 `\n`。这是历史原因造成的，大多数的 Windows 软件也理解 `\n`。 |
| `\'`, `\"`                              | 引号                                                         |
| `\\`                                    | 反斜线                                                       |
| `\t`                                    | 制表符                                                       |
| `\b`, `\f`, `\v`                        | 退格，换页，垂直标签 —— 为了兼容性，现在已经不使用了。       |
| `\xXX`                                  | 具有给定十六进制 Unicode `XX` 的 Unicode 字符，例如：`'\x7A'` 和 `'z'` 相同。 |
| `\uXXXX`                                | 以 UTF-16 编码的十六进制代码 `XXXX` 的 Unicode 字符，例如 `\u00A9` —— 是版权符号 `©` 的 Unicode。它必须正好是 4 个十六进制数字。 |
| `\u{X…XXXXXX}`（1 到 6 个十六进制字符） | 具有给定 UTF-32 编码的 Unicode 符号。一些罕见的字符用两个 Unicode 符号编码，占用 4 个字节。这样我们就可以插入长代码了。 |

Unicode 示例：

```javascript
alert( "\u00A9" ); // ©
alert( "\u{20331}" ); // 佫，罕见的中国象形文字（长 Unicode）
alert( "\u{1F60D}" ); // 😍，笑脸符号（另一个长 Unicode）
```

如果我们想要在字符串中插入一个引号，我们也会使用它。

```javascript
alert( 'I\'m the Walrus!' ); // I'm the Walrus!
```

当然，只有与外部闭合引号相同的引号才需要转义。因此，作为一个更优雅的解决方案，我们可以改用双引号或者反引号：

```javascript
alert( `I'm the Walrus!` ); // I'm the Walrus!
```

#### 字符串长度

`length` 属性表示字符串长度：

```javascript
alert( `My\n`.length ); // 3
```

掌握其他编程语言的人，有时会错误地调用 `str.length()` 而不是 `str.length`。这是行不通的。请注意 `str.length` 是一个数字属性，而不是函数。后面不需要添加括号。

#### 访问字符

要获取在 `pos` 位置的一个字符，可以使用方括号 `[pos]` 或者调用 str.charAt(pos) 方法。第一个字符从零位置开始：

```javascript
let str = `Hello`;

alert( str[0] ); // H
alert( str.charAt(0) ); // H
alert( str[str.length - 1] ); // o
```

方括号是获取字符的一种现代化方法，而 `charAt` 是历史原因才存在的。

它们之间的唯一区别是，如果没有找到字符，`[]` 返回 `undefined`，而 `charAt` 返回一个空字符串：

```javascript
let str = `Hello`;

alert( str[1000] ); // undefined
alert( str.charAt(1000) ); // ''（空字符串）
```

我们也可以使用 `for..of` 遍历字符：

```javascript
for (let char of "Hello") {
  alert(char); // H,e,l,l,o（char 变为 "H"，然后是 "e"，然后是 "l" 等）
}
```

#### 字符串是不可变的

在 JavaScript 中，字符串不可更改。改变字符是不可能的。

我们证明一下为什么不可能：

```javascript
let str = 'Hi';
str[0] = 'h'; // error
alert( str[0] ); // 无法运行
```

通常的解决方法是创建一个新的字符串，并将其分配给 `str` 而不是以前的字符串。

```javascript
let str = 'Hi';

str = 'h' + str[1];  // 替换字符串
alert( str ); // hi
```

#### 改变大小写

`toLowerCase()` 和 `toUpperCase()` 方法可以改变大小写：

```javascript
alert( 'Interface'.toUpperCase() ); // INTERFACE
alert( 'Interface'.toLowerCase() ); // interface
```

或者我们想要使一个字符变成小写：

```javascript
alert( 'Interface'[0].toLowerCase() ); // 'i'
```

#### 查找子字符串

在字符串中查找子字符串有很多种方法。

##### str.indexOf

它从给定位置 `pos` 开始，在 `str` 中查找 `substr`，如果没有找到，则返回 `-1`，否则返回匹配成功的位置。

```javascript
let str = 'Widget with id';

alert( str.indexOf('Widget') ); // 0
alert( str.indexOf('widget') ); // -1，检索是大小写敏感的

alert( str.indexOf("id") ); // 1，"id" 在位置 1 处
```

可选的第二个参数允许我们从一个给定的位置开始检索。

例如，`"id"` 第一次出现的位置是 `1`。查询下一个存在位置时，我们从 `2` 开始检索：

```javascript
let str = 'Widget with id';

alert( str.indexOf('id', 2) ) // 12
```

如果我们对所有存在位置都感兴趣，可以在一个循环中使用 `indexOf`。每一次新的调用都发生在上一匹配位置之后：

```javascript
let str = 'As sly as a fox, as strong as an ox';

let target = 'as';

let pos = 0;
while (true) {
  let foundPos = str.indexOf(target, pos);
  if (foundPos == -1) break;
  alert( `Found at ${foundPos}` );
  pos = foundPos + 1; // 继续从下一个位置查找
}
```

相同的算法可以简写：

```javascript
let str = "As sly as a fox, as strong as an ox";
let target = "as";

let pos = -1;
while ((pos = str.indexOf(target, pos + 1)) != -1) {
  alert( pos );
}
```

##### str.lastIndexOf(substr, pos)

它从字符串的末尾开始搜索到开头，会以相反的顺序列出这些事件。

在 `if` 测试中 `indexOf` 有一点不方便。我们不能像这样把它放在 `if` 中：

```javascript
let str = "Widget with id";

if (str.indexOf("Widget")) {
    alert("We found it"); // 不工作！
}
```

上述示例中的 `alert` 不会显示，因为 `str.indexOf("Widget")` 返回 0。但是 if 认为 0 表示 false。因此应该像这样：

```javascript
let str = "Widget with id";

if (str.indexOf("Widget") != -1) {
    alert("We found it");
}
```

##### 按位（bitwise）NOT 技巧

它将数字转换为 32-bit 整数（如果存在小数部分，则删除小数部分），然后对其二进制表示形式中的所有位均取反。

实际上，这意味着一件很简单的事儿：对于 32-bit 整数，`~n` 等于 `-(n+1)`。

```javascript
alert( ~2 ); // -3，和 -(2+1) 相同
alert( ~-1 ); // 0，和 -(-1+1) 相同
```

因此，仅当 `indexOf` 的结果不是 `-1` 时，检查 `if ( ~str.indexOf("...") )` 才为真。人们用它来简写 `indexOf` 检查：

```javascript
let str = "Widget";

if (~str.indexOf("Widget")) {
  alert( 'Found it!' ); // 正常运行
}
```

通常不建议以非显而易见的方式使用语言特性，但这种特殊技巧在旧代码中仍被广泛使用，所以我们应该理解它。

> if (~str.indexOf(...)) 读作 “if found”。

确切地说，由于 `~` 运算符将大数字截断为 32 位，因此存在给出 `0` 的其他数字，最小的数字是 `~4294967295=0`。这使得这种检查只有在字符串没有那么长的情况下才是正确的。

##### includes，startsWith，endsWith

更现代的方法 str.includes(substr, pos) 根据 `str` 中是否包含 `substr` 来返回 `true/false`。

如果我们需要检测匹配，但不需要它的位置，那么这是正确的选择：

```javascript
alert( "Widget with id".includes("Widget") ); // true
alert( "Hello".includes("Bye") ); // false
```

`str.includes` 的第二个可选参数是开始搜索的起始位置：

```javascript
alert( "Widget".includes("id") ); // true
alert( "Widget".includes("id", 3) ); // false, 从位置 3 开始没有 "id"
```

方法 str.startsWith 和 str.endsWith 的功能与其名称所表示的意思相同：

```javascript
alert( "Widget".startsWith("Wid") ); // true，"Widget" 以 "Wid" 开始
alert( "Widget".endsWith("get") ); // true，"Widget" 以 "get" 结束
```

#### 获取子字符串

JavaScript 中有三种获取字符串的方法：`substring`、`substr` 和 `slice`。

| 方法                  | 选择方式……                            | 负值参数          |
| :-------------------- | :------------------------------------ | :---------------- |
| slice(start, end)     | 从 start 到 end（不含 end）           | 允许              |
| substring(start, end) | 从 start 到 end（不含 end）           | 负值被视为 0      |
| substr(start, length) | 从 start 开始获取长为 length 的字符串 | 允许 start 为负数 |

#### 比较字符串

所有的字符串都使用 UTF-16 编码。即：每个字符都有对应的数字代码。有特殊的方法可以获取代码表示的字符，以及字符对应的代码。

- `str.codePointAt(pos)`：返回在 `pos` 位置的字符代码

- `String.fromCodePoint(code)`：通过数字 `code` 创建字符，还可以用 `\u` 后跟十六进制代码，通过这些代码添加 Unicode 字符

现在我们看一下代码为 `65..220` 的字符（拉丁字母和一些额外的字符），方法是创建一个字符串：

```javascript
let str = '';

for (let i = 65; i <= 220; i++) {
  str += String.fromCodePoint(i);
}
alert( str );
// ABCDEFGHIJKLMNOPQRSTUVWXYZ[\]^_`abcdefghijklmnopqrstuvwxyz{|}~......
// ¡¢£¤¥¦§¨©ª«¬·®¯°±²³´µ¶·¸¹º»¼½¾¿ÀÁÂÃÄÅÆÇÈÉÊËÌÍÎÏÐÑÒÓÔÕÖ×ØÙÚÛÜ
```

##### 正确的比较

执行字符串比较的“正确”算法比看起来更复杂，因为不同语言的字母都不相同。所有现代浏览器（IE10- 需要额外的库 [Intl.JS](https://github.com/andyearnshaw/Intl.js/)) 都支持国际化标准 [ECMA-402](http://www.ecma-international.org/ecma-402/1.0/ECMA-402.pdf)。

调用 `str.localeCompare(str2)` 会根据语言规则返回一个整数，这个整数能指示字符串 `str` 在排序顺序中排在字符串 `str2` 前面、后面、还是相同：

- 如果 `str` 排在 `str2` 前面，则返回负数。
- 如果 `str` 排在 `str2` 后面，则返回正数。
- 如果它们在相同位置，则返回 `0`。

```javascript
alert( 'Österreich'.localeCompare('Zealand') ); // -1
```

#### 内部，Unicode

##### 代理对

所有常用的字符都是一个 2 字节的代码。大多数欧洲语言，数字甚至大多数象形文字中的字母都有 2 字节的表示形式。

但 2 字节只允许 65536 个组合，这对于表示每个可能的符号是不够的。所以稀有的符号被称为“代理对”的一对 2 字节的符号编码。

这些符号的长度是 `2`：

```javascript
alert( '𝒳'.length ); // 2，大写数学符号 X
alert( '😂'.length ); // 2，笑哭表情
alert( '𩷶'.length ); // 2，罕见的中国象形文字
```

注意，代理对在 JavaScript 被创建时并不存在，因此无法被编程语言正确处理！

我们实际上在上面的每个字符串中都有一个符号，但 `length` 显示长度为 `2`。

`String.fromCodePoint` 和 `str.codePointAt` 是几种处理代理对的少数方法。它们最近才出现在编程语言中。在它们之前，只有 String.fromCharCode 和 str.charCodeAt。这些方法实际上与 `fromCodePoint/codePointAt` 相同，但是不适用于代理对。

获取符号可能会非常麻烦，因为代理对被认为是两个字符：

```javascript
alert( '𝒳'[0] ); // 奇怪的符号……
alert( '𝒳'[1] ); // ……代理对的一块
```

请注意，代理对的各部分没有任何意义。因此，上述示例中的 alert 显示的实际上是垃圾信息。

技术角度来说，代理对也是可以通过它们的代码检测到的：如果一个字符的代码在 `0xd800..0xdbff` 范围内，那么它是代理对的第一部分。下一个字符（第二部分）必须在 `0xdc00..0xdfff` 范围中。这些范围是按照标准专门为代理对保留的。

```javascript
// charCodeAt 不理解代理对，所以它给出了代理对的代码

alert( '𝒳'.charCodeAt(0).toString(16) ); // d835，在0xd800和0xdbff之间
alert( '𝒳'.charCodeAt(1).toString(16) ); // dcb3, 在0xdc00和0xdfff之间
```

#### 变音符号与规范化

在许多语言中，都有一些由基本字符组成的符号，在其上方/下方有一个标记。

例如，字母 `a` 可以是 `àáâäãåā` 的基本字符。最常见的“复合”字符在 UTF-16 表中都有自己的代码。但不是全部，因为可能的组合太多。

为了支持任意组合，UTF-16 允许我们使用多个 Unicode 字符：基本字符紧跟“装饰”它的一个或多个“标记”字符。

例如，如果我们 `S` 后跟有特殊的 “dot above” 字符（代码 `\u0307`），则显示 Ṡ。

```javascript
alert( 'S\u0307' ); // Ṡ
```

如果我们需要在字母上方（或下方）添加额外的标记 —— 没问题，只需要添加必要的标记字符即可。

例如，如果我们追加一个字符 “dot below”（代码 `\u0323`），那么我们将得到“S 上面和下面都有点”的字符：`Ṩ`。

```javascript
alert( 'S\u0307\u0323' ); // Ṩ
```

这在提供良好灵活性的同时，也存在一个有趣的问题：两个视觉上看起来相同的字符，可以用不同的 Unicode 组合表示。

```javascript
let s1 = 'S\u0307\u0323'; // Ṩ，S + 上点 + 下点
let s2 = 'S\u0323\u0307'; // Ṩ，S + 下点 + 上点

alert( `s1: ${s1}, s2: ${s2}` );

alert( s1 == s2 ); // false，尽管字符看起来相同（?!）
```

为了解决这个问题，有一个 “Unicode 规范化”算法，它将每个字符串都转化成单个“通用”格式。

它由 str.normalize() 实现。

```javascript
alert( "S\u0307\u0323".normalize() == "S\u0323\u0307".normalize() ); // true
```

实际情况下，`normalize()` 实际上将一个由 3 个字符组成的序列合并为一个：`\u1e68`（S 有两个点）。

```javascript
alert( "S\u0307\u0323".normalize().length ); // 1
alert( "S\u0307\u0323".normalize() == "\u1e68" ); // true
```

事实上，情况并非总是如此，因为符号 `Ṩ` 是“常用”的，所以 UTF-16 创建者把它包含在主表中并给它了对应的代码。



## 附录

### 运算符优先级

| Operator type          | Individual operators                     |
| :--------------------- | :--------------------------------------- |
| member                 | `. []`                                   |
| call / create instance | `() new`                                 |
| negation/increment     | `! ~ - + ++ -- typeof void delete`       |
| multiply/divide        | `* / %`                                  |
| addition/subtraction   | `+ -`                                    |
| bitwise shift          | `<< >> >>>`                              |
| relational             | `< <= > >= in instanceof`                |
| equality               | `== != === !==`                          |
| bitwise-and            | `&`                                      |
| bitwise-xor            | `^`                                      |
| bitwise-or             | `|`                                      |
| logical-and            | `&&`                                     |
| logical-or             | `||`                                     |
| conditional            | `?:`                                     |
| assignment             | `= += -= *= /= %= <<= >>= >>>= &= ^= |=` |
| comma                  | `,`                                      |

