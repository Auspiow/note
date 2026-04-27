# JS妙妙小知识

以下知识参考自 [现代 JavaScript 教程](https://zh.javascript.info/) 以及 [MDN开发者手册](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript)

其实我一开始直接看的MDN手册，但是觉得上面的知识比较“官方”，不易消化理解。后来遇到了“现代JavaScript教程”，将知识贯通了起来，于是有了这篇笔记。笔记的主要脉络也是与“现代JavaScript教程”保持一致，但是略有修改。

## 基础知识

### 变量

* var：老旧的变量声明方式。一般情况下不会使用它。变量的声明被提升为undefined。

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

`for...in` 

枚举属性名（key）

```js
for (variable in object) {
  statements
}
```

> 虽然使用 **for...in** 来迭代数组 Array元素听起来很诱人，但是它返回的东西除了数字索引外，还有可能是你自定义的属性名字。

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

for (let i in arr) {
  console.log(i); // 输出 "0", "1", "2", "foo"
}

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


