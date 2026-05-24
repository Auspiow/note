# JS妙妙小知识（下）

## 函数进阶内容

### Rest 参数与 Spread 语法

#### Rest 参数

在 JavaScript 中，无论函数是如何定义的，你都可以在调用它时传入任意数量的参数。

```javascript
function sum(a, b) {
  return a + b;
}
alert( sum(1, 2, 3, 4, 5) );
```

虽然函数不会因为传入过多的参数而报错，但是只有前两个参数被求和了。

`...变量名`，这将会声明一个数组并指定其名称，其中存有剩余的参数。

把所有的参数都放到数组 `args` 中：

```javascript
function sumAll(...args) { // 数组名为 args
  let sum = 0;
  for (let arg of args) sum += arg;
  return sum;
}

alert( sumAll(1) ); // 1
alert( sumAll(1, 2, 3) ); // 6
```

我们也可以选择将第一个参数获取为变量，并将剩余的参数收集起来。

下面的例子把前两个参数获取为变量，并把剩余的参数收集到 `titles` 数组中：

```javascript
function showName(firstName, lastName, ...titles) {
  alert( firstName + ' ' + lastName ); // Julius Caesar
  alert( titles[0] ); // Consul
  alert( titles[1] ); // Imperator
  alert( titles.length ); // 2
}

showName("Julius", "Caesar", "Consul", "Imperator");
```

**Rest 参数必须放到参数列表的末尾**

下面这种用法没有意义，并且会导致错误：

```javascript
function f(arg1, ...rest, arg2) { // arg2 在 ...rest 后面？！
  // error
}
```

#### “arguments” 变量

有一个名为 `arguments` 的特殊类数组对象可以在函数中被访问，该对象以参数在参数列表中的索引作为键，存储所有参数。

```javascript
function showName() {
  alert( arguments.length );
  alert( arguments[0] );
  alert( arguments[1] );
  // for(let arg of arguments) alert(arg);
}

showName("Julius", "Caesar"); // 依次显示：2，Julius，Caesar
showName("Ilya"); // 依次显示：1，Ilya，undefined
```

在过去，JavaScript 中不支持 rest 参数语法，而使用 `arguments` 是获取函数所有参数的唯一方法。

但缺点是，尽管 `arguments` 是一个类数组，也是可迭代对象，但不支持数组方法，因此不能调用 `arguments.map(...)` 等方法。

**箭头函数没有 `"arguments"`**

如果我们在箭头函数中访问 `arguments`，访问到的 `arguments` 并不属于箭头函数，而是属于箭头函数外部的“普通”函数。

```javascript
function f() {
  let showArg = () => alert(arguments[0]);
  showArg();
}

f(1); // 1
```

箭头函数没有自身的 `this`，也没有特殊的 `arguments` 对象。

#### Spread 语法

例如，内建函数 Math.max 会返回参数中最大的值：

```javascript
alert( Math.max(3, 5, 1) ); // 5
```

如果我们有一个数组 `[3, 5, 1]`，我们该如何用它调用 `Math.max` 呢？

直接“原样”传入这个数组是不会奏效的，因为 `Math.max` 期望的是列表形式的数值型参数，而不是一个数组：

```javascript
let arr = [3, 5, 1];

alert( Math.max(arr) ); // NaN
```

**Spread 语法** 可以解决这个问题，看起来和 rest 参数很像，也使用 `...`，但是二者的用途完全相反。

当在函数调用中使用 `...arr` 时，它会把可迭代对象 `arr` 展开到参数列表中。

```javascript
let arr = [3, 5, 1];
alert( Math.max(...arr) ); // 5（spread 语法把数组转换为参数列表）
```

传入多个可迭代对象：

```javascript
let arr1 = [1, -2, 3, 4];
let arr2 = [8, 3, -8, 1];

alert( Math.max(...arr1, ...arr2) ); // 8
alert( Math.max(1, ...arr1, 2, ...arr2, 25) ); // 25
```

使用 spread 语法来合并数组：

```javascript
let arr = [3, 5, 1];
let arr2 = [8, 9, 15];
let merged = [0, ...arr, 2, ...arr2];

alert(merged); // 0,3,5,1,2,8,9,15
```

在上面的示例中，我们使用数组展示了 spread 语法，其实我们可以用 spread 语法这样操作任何可迭代对象。

例如，在这儿我们使用 spread 语法将字符串转换为字符数组：

```javascript
let str = "Hello";
alert( [...str] ); // H,e,l,l,o
```

Spread 语法内部使用了迭代器来收集元素，与 `for..of` 的方式相同。

对于这个特定任务，我们还可以使用 `Array.from` 来实现，因为该方法会将一个可迭代对象（如字符串）转换为数组：

```javascript
let str = "Hello";

// Array.from 将可迭代对象转换为数组
alert( Array.from(str) ); // H,e,l,l,o
```

运行结果与 `[...str]` 相同。

不过 `Array.from(obj)` 和 `[...obj]` 存在一个细微的差别：

- `Array.from` 适用于类数组对象也适用于可迭代对象。
- Spread 语法只适用于可迭代对象。

因此，对于将一些“东西”转换为数组的任务，`Array.from` 往往更通用。

#### 复制 array/object

使用 spread 语法也可以完成像 `Object.assign()` 一样的浅拷贝。 

```javascript
let arr = [1, 2, 3];
let arrCopy = [...arr];
alert(JSON.stringify(arr) === JSON.stringify(arrCopy)); // true

alert(arr === arrCopy); // false（它们的引用是不同的）

arr.push(4);
alert(arr); // 1, 2, 3, 4
alert(arrCopy); // 1, 2, 3
```



### 变量作用域，闭包

#### 代码块

如果在代码块 `{...}` 内声明了一个变量，那么这个变量只在该代码块内可见。

例如：

```javascript
{
  let message = "Hello";
  alert(message); // Hello
}
alert(message); // Error: message is not defined
```

对于 `if`，`for` 和 `while` 等，在 `{...}` 中声明的变量也仅在内部可见：

```javascript
if (true) {
  let phrase = "Hello!";
  alert(phrase); // Hello!
}

alert(phrase); // Error, no such variable!
```

对于 `for` 和 `while` 循环也是如此：

```javascript
for (let i = 0; i < 3; i++) {
  alert(i); // 0,1,2
}

alert(i); // Error, no such variable
```

`let i` 位于 `{...}` 之外。但是仍然被视为块的一部分。

#### 嵌套函数

如果一个函数是在另一个函数中创建的，该函数就被称为“嵌套”函数。

```javascript
function sayHiBye(firstName, lastName) {
  function getFullName() {
    return firstName + " " + lastName;
  }

  alert( "Hello, " + getFullName() );
  alert( "Bye, " + getFullName() );

}
```

这里创建的 **嵌套** 函数 `getFullName()` 是为了更加方便。它可以访问外部变量。

我们可以返回一个嵌套函数：作为一个新对象的属性或作为结果返回。之后可以在其他地方使用。不论在哪里调用，它仍然可以访问相同的外部变量。

下面的 `makeCounter` 创建了一个 “counter” 函数，该函数在每次调用时返回下一个数字：

```javascript
function makeCounter() {
  let count = 0;

  return function() {
    return count++;
  };
}

let counter = makeCounter();

alert( counter() ); // 0
alert( counter() ); // 1
alert( counter() ); // 2
```

#### 词法环境

##### Step 1. 变量

在 JavaScript 中，每个运行的函数，代码块 `{...}` 以及整个脚本，都有一个被称为 **词法环境（Lexical Environment）** 的内部（隐藏）的关联对象。

词法环境对象由两部分组成：

1. **环境记录（Environment Record）**：一个存储所有局部变量作为其属性（包括一些其他信息，例如 `this` 的值）的对象。
2. 对 **外部词法环境** 的引用，与外部代码相关联。

变量只是环境记录这个特殊的内部对象的一个属性，修改变量意味着改变词法环境的属性。

举个例子，这段没有函数的简单的代码中只有一个词法环境：

![屏幕截图 2026-05-25 005722](./images/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE%202026-05-25%20005722.png)

这就是所谓的与整个脚本相关联的 **全局** 词法环境。

在上面的图片中，矩形表示环境记录（变量存储），箭头表示外部引用。全局词法环境没有外部引用，所以箭头指向了 `null`。

**词法环境是一个规范对象**

“词法环境”是一个规范对象（specification object）：它只存在于语言规范的“理论”层面，用于描述事物是如何工作的。我们无法在代码中获取该对象并直接对其进行操作。

但 JavaScript 引擎同样可以优化它，比如清除未被使用的变量以节省内存和执行其他内部技巧等，但显性行为应该是和上述的无差。

##### Step 2. 函数声明

**函数声明的初始化会被立即完成。**

当创建了一个词法环境时，函数声明会立即变为即用型函数（不像 `let` 那样直到声明处才可用）。

这就是为什么我们甚至可以在声明自身之前调用一个以函数声明（Function Declaration）的方式声明的函数。

正常来说，这种行为仅适用于函数声明，而不适用于我们将函数分配给变量的函数表达式，例如 `let say = function(name)...`。

##### Step 3. 内部和外部的词法环境

函数运行在调用刚开始时，会自动创建一个新的词法环境以存储这个调用的局部变量和参数。

例如，对于 `say("John")`，它看起来像这样（当前执行位置在箭头标记的那一行上）：

![屏幕截图 2026-05-25 010423](./images/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE%202026-05-25%20010423.png)

在函数调用期间，我们有两个词法环境：内部（用于函数调用）和外部（全局）：

- 内部词法环境与 `say` 的当前执行相对应。它具有一个单独的属性：`name`，函数的参数。
- 外部词法环境是全局词法环境。它具有 `phrase` 变量和函数本身。

**当代码要访问一个变量时会首先会搜索内部词法环境，然后搜索外部环境，然后搜索更外部的环境，以此类推，直到全局词法环境。**

如果在任何地方都找不到这个变量，那么在严格模式下就会报错（非严格模式下，未定义的变量赋值会创建一个全局变量）。

##### Step 4. 返回函数

让我们回到 `makeCounter` 这个例子。

```javascript
function makeCounter() {
  let count = 0;
  return function() {
    return count++;
  };
}
let counter = makeCounter();
```

在每次 `makeCounter()` 调用的开始，都会创建一个新的词法环境对象，以存储该 `makeCounter` 运行时的变量。

![屏幕截图 2026-05-25 011702](./images/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE%202026-05-25%20011702.png)

所有函数都有名为 `[[Environment]]` 的隐藏属性，该属性保存了对创建该函数的词法环境的引用。

因此，`counter.[[Environment]]` 有对 `{count: 0}` 词法环境的引用。这就是函数记住它创建于何处的方式，与函数被在哪儿调用无关。`[[Environment]]` 引用在函数创建时被设置并永久保存。

现在，当 `counter()` 中的代码查找 `count` 变量时，它首先搜索自己的词法环境，然后是外部 `makeCounter()` 的词法环境，并且在哪里找到就在哪里修改。

如果我们调用 `counter()` 多次，`count` 变量将在同一位置增加到 `2`，`3` 等。

#### 闭包

开发者通常应该都知道“闭包”这个通用的编程术语。

闭包是指一个函数可以记住其外部变量并可以访问这些变量。在某些编程语言中，这是不可能的，或者应该以一种特殊的方式编写函数来实现。但如上所述，在 JavaScript 中，所有函数都是天生闭包的（只有一个例外）。

也就是说：JavaScript 中的函数会自动通过隐藏的 `[[Environment]]` 属性记住创建它们的位置，所以它们都可以访问外部变量。

在面试时，前端开发者通常会被问到“什么是闭包？”，正确的回答应该是闭包的定义，并解释清楚为什么 JavaScript 中的所有函数都是闭包的，以及可能的关于 `[[Environment]]` 属性和词法环境原理的技术细节。

#### 垃圾收集

通常，函数调用完成后，会将词法环境和其中的所有变量从内存中删除。因为现在没有任何对它们的引用了。与 JavaScript 中的任何其他对象一样，词法环境仅在可达时才会被保留在内存中。

但是，如果有嵌套函数在函数结束后仍可达，则它将具有引用词法环境的 `[[Environment]]` 属性。

在下面这个例子中，即使在（外部）函数执行完成后，它的词法环境仍然可达。因此，此词法环境仍然有效。

```javascript
function f() {
  let value = 123;
  return function() {
    alert(value);
  }
}

let g = f(); // g.[[Environment]] 存储了对相应 f() 调用的词法环境的引用
```

请注意，如果多次调用 `f()`，并且返回的函数被保存，那么所有相应的词法环境对象也会保留在内存中。下面代码中有三个这样的函数：

```javascript
function f() {
  let value = Math.random();
  return function() { alert(value); };
}

let arr = [f(), f(), f()];
```

当词法环境对象变得不可达时，它就会死去（就像其他任何对象一样）。换句话说，它仅在至少有一个嵌套函数引用它时才存在。

在下面的代码中，嵌套函数被删除后，其封闭的词法环境（以及其中的 `value`）也会被从内存中删除：

```javascript
function f() {
  let value = 123;

  return function() {
    alert(value);
  }
}

let g = f(); // 当 g 函数存在时，该值会被保留在内存中
g = null; // ……现在内存被清理了
```

##### 实际开发中的优化

理论上当函数可达时，它外部的所有变量也都将存在。但在实际中，JavaScript 引擎会试图优化它。它们会分析变量的使用情况，如果从代码中可以明显看出有未使用的外部变量，那么就会将其删除。

**在 V8（Chrome，Edge，Opera）中的一个重要的副作用是，此类变量在调试中将不可用。**

打开 Chrome 浏览器的开发者工具，并尝试运行下面的代码。

当代码执行暂停时，在控制台中输入 `alert(value)`。

```javascript
function f() {
  let value = Math.random();
  function g() {
    debugger; // 输入 alert(value):No such variable!
  }
  return g;
}

let g = f();
g();
```

理论上，它应该是可以访问的，但引擎把它优化掉了。

这可能会导致有趣的（如果不是那么耗时的）调试问题。

```javascript
let value = "Surprise!";
function f() {
  let value = "the closest value";
  function g() {
    debugger; // 输入 alert(value):value is not defined
  }
  return g;
}
let g = f();
g();
```



### 全局对象

全局对象提供可在任何地方使用的变量和函数。默认情况下，这些全局变量内建于语言或环境中。

在浏览器中，它的名字是 “window”，对 Node.js 而言，它的名字是 “global”。`globalThis` 作为全局对象的标准名称加入到了 JavaScript 中。

假设我们的环境是浏览器，我们将在这儿使用 “window”。如果脚本可能会用来在其他环境中运行，则最好使用 `globalThis`。

全局对象的所有属性都可以被直接访问：

```javascript
alert("Hello");
window.alert("Hello");
```

在浏览器中，使用 `var` 声明的全局函数和变量会成为全局对象的属性。

```javascript
var gVar = 5;
alert(window.gVar); // 5（成为了全局对象的属性）
```

函数声明（特指在主代码流中具有 `function` 关键字的语句，而不是函数表达式）也有这样的效果。

如果我们使用 `let`，就不会发生这种情况：

```javascript
let gLet = 5;
alert(window.gLet); // undefined
```

如果一个值非常重要，以至于你想使它在全局范围内可用，那么可以直接将其作为属性写入：

```javascript
window.currentUser = {
  name: "John"
};

alert(currentUser.name);  // John
alert(window.currentUser.name); // John
```

也就是说，一般不建议使用全局变量。全局变量应尽可能的少。与使用外部变量或全局变量相比，函数获取“输入”变量并产生特定“输出”的代码设计更加清晰，不易出错且更易于测试。

#### 使用 polyfills

使用全局对象来测试对现代语言功能的支持。

例如，测试是否存在内建的 `Promise` 对象（在版本特别旧的浏览器中不存在）：

```javascript
if (!window.Promise) {
  alert("Your browser is really old!");
}
```

如果不支持，那么可以创建 “polyfills”：添加环境不支持但在现代标准中存在的功能。

```javascript
if (!window.Promise) {
  window.Promise = ... // 定制实现现代语言功能
}
```



### new Function

还有一种创建函数的方法。它很少被使用，但有些时候只能选择它。

创建函数的语法：

```javascript
let func = new Function ([arg1, arg2, ...argN], functionBody);
```

该函数是通过使用参数 `arg1...argN` 和给定的 `functionBody` 创建的。

下面这个例子可以帮助你理解创建语法。这是一个带有两个参数的函数：

```javascript
let sum = new Function('a', 'b', 'return a + b');
alert( sum(1, 2) ); // 3
```

这里有一个没有参数的函数，只有函数体：

```javascript
let sayHi = new Function('alert("Hello")');
sayHi(); // Hello
```

这种方法实际上是通过运行时通过参数传递过来的字符串创建的。

 `new Function` 允许任意字符串变为函数。例如，我们可以从服务器接收一个新的函数并执行它：

```javascript
let str = ... 动态地接收来自服务器的代码 ...

let func = new Function(str);
func();
```

使用 `new Function` 创建函数的应用场景非常特殊，比如在复杂的 Web 应用程序中，我们需要从服务器获取代码或者动态地从模板编译函数时才会使用。

#### 闭包

但是如果我们使用 `new Function` 创建一个函数，那么该函数的 `[[Environment]]` 并不指向当前的词法环境，而是指向全局环境。

因此，此类函数无法访问外部（outer）变量，只能访问全局变量。

```javascript
function getFunc() {
  let value = "test";
  let func = new Function('alert(value)');
  return func;
}

getFunc()(); // error: value is not defined
```

`new Function` 的这种特性看起来有点奇怪，不过在实际中却非常实用。

想象一下我们必须通过一个字符串来创建一个函数。在编写脚本时我们不会知道该函数的代码（这也就是为什么我们不用常规方法创建函数），但在执行过程中会知道了。我们可能会从服务器或其他来源获取它。

如果这个函数能够访问外部（outer）变量会怎么样？

问题在于，在将 JavaScript 发布到生产环境之前，需要使用 **压缩程序（minifier）** 对其进行压缩：通过删除多余的注释和空格等压缩代码，更重要的是，将局部变量命名为较短的变量。

例如，如果一个函数有 `let userName`，压缩程序会把它替换为 `let a`，剩余的局部变量也会被进行类似的替换。一般来说这样的替换是安全的，毕竟这些变量是函数内的局部变量，函数外的任何东西都无法访问它。在函数内部，压缩程序会替换所有使用了这些变量的代码。

如果 `new Function` 可以访问自身函数以外的变量，它也很有可能无法找到重命名过的变量，这是因为新函数的创建发生在代码压缩以后，变量名已经被替换了。

当我们需要向 `new Function` 创建出的新函数传递数据时，我们必须显式地通过参数进行传递。



### setTimeout 和 setInterval

有时我们并不想立即执行函数，而是等待特定一段时间之后再执行，所谓的“计划调用（scheduling a call）”。

- `setTimeout` 允许我们将函数推迟到一段时间间隔之后再执行。
- `setInterval` 允许我们重复运行一个函数，从一段时间间隔之后开始运行，之后以该时间间隔连续重复运行该函数。

这两个方法并不在 JavaScript 的规范中，但是所有浏览器以及 Node.js 都支持这两个方法。

#### setTimeout

```javascript
let timerId = setTimeout(func|code, [delay], [arg1], [arg2], ...)
```

- `func|code`：想要执行的函数或代码字符串。
- `delay`：执行前的延时，以毫秒为单位（1000 毫秒 = 1 秒），默认值是 0；
- `arg1`，`arg2`…：要传入被执行函数（或代码字符串）的参数列表（IE9 以下不支持）

`sayHi()` 方法会在 1 秒后执行：

```javascript
function sayHi() {
  alert('Hello');
}

setTimeout(sayHi, 1000);
```

带参数的情况：

```javascript
function sayHi(phrase, who) {
  alert( phrase + ', ' + who );
}

setTimeout(sayHi, 1000, "Hello", "John"); // Hello, John
```

如果第一个参数位传入的是字符串，JavaScript 会自动为其创建一个函数。

```javascript
setTimeout("alert('Hello')", 1000);
```

但是，不建议使用字符串，我们可以使用箭头函数代替：

```javascript
setTimeout(() => alert('Hello'), 1000);
```

**传入一个函数，但不要执行它**

新手开发者有时候会误将一对括号 `()` 加在函数后面：

```javascript
setTimeout(sayHi(), 1000); // 错误！
```

 `setTimeout` 期望得到一个对函数的引用。而这里的 `sayHi()` 是在执行函数，所以实际上传入的是 **函数的执行结果**。在这个例子中，`sayHi()` 的执行结果是 `undefined`（也就是说函数没有返回任何结果），所以实际上什么也没有调度。

##### 用 clearTimeout 来取消调度

`setTimeout` 在调用时会返回一个“定时器标识符（timer identifier）”，在我们的例子中是 `timerId`，我们可以使用它来取消执行。

取消调度的语法：

```javascript
let timerId = setTimeout(...);
clearTimeout(timerId);
```

调度函数，紧接着取消了这次调度：

```javascript
let timerId = setTimeout(() => alert("never happens"), 1000);
alert(timerId); // 定时器标识符
clearTimeout(timerId);
alert(timerId); // 还是这个标识符
```

从 `alert` 的输出来看，在浏览器中，定时器标识符是数字。Node.js 返回的是定时器对象，包含一系列方法。

针对浏览器环境，定时器在 HTML5 的标准中有详细描述，详见 [timers section](https://www.w3.org/TR/html5/webappapis.html#timers)。

#### setInterval

`setInterval` 方法和 `setTimeout` 的语法相同：

```javascript
let timerId = setInterval(func|code, [delay], [arg1], [arg2], ...)
```

所有参数的意义也是相同的。不过与 `setTimeout` 只执行一次不同，`setInterval` 是每间隔给定的时间周期性执行。

想要阻止后续调用，我们需要调用 `clearInterval(timerId)`。

下面的例子将每间隔 2 秒就会输出一条消息。5 秒之后，输出停止：

```javascript
let timerId = setInterval(() => alert('tick'), 2000);
setTimeout(() => { clearInterval(timerId); alert('stop'); }, 5000);
```

**alert 弹窗显示的时候计时器依然在进行计时**

在大多数浏览器中，在显示 `alert/confirm/prompt` 弹窗时，内部的定时器仍旧会继续。

所以，在运行上面的代码时，如果在一定时间内没有关掉 `alert` 弹窗，那么在关闭弹窗后，下一个 `alert` 会立即显示。

#### 嵌套的 setTimeout

周期性调度有两种方式。

一种是使用 `setInterval`，另外一种就是嵌套的 `setTimeout`，就像这样：

```javascript
let timerId = setTimeout(function tick() {
  alert('tick');
  timerId = setTimeout(tick, 2000); // (*)
}, 2000);
```

嵌套的 `setTimeout` 要比 `setInterval` 灵活得多。采用这种方式可以根据当前执行结果来调度下一次调用。

例如要实现一个服务，每间隔 5 秒向服务器发送一个数据请求，但如果服务器过载了，那么就要降低请求频率，比如将间隔增加到 10、20、40 秒等。

```javascript
let delay = 5000;

let timerId = setTimeout(function request() {
  ...发送请求...

  if (request failed due to server overload) {
    delay *= 2; // 下一次执行的间隔是当前的 2 倍
  }
  timerId = setTimeout(request, delay);
}, delay);
```

**嵌套的 `setTimeout` 相较于 `setInterval` 能够更精确地设置两次执行之间的延时。**

下面来比较这两个代码片段。第一个使用的是 `setInterval`：

```javascript
let i = 1;
setInterval(function() {
  func(i++);
}, 100);
```

第二个使用的是嵌套的 `setTimeout`：

```javascript
let i = 1;
setTimeout(function run() {
  func(i++);
  setTimeout(run, 100);
}, 100);
```

**使用 `setInterval` 时，`func` 函数的实际调用间隔要比代码中设定的时间间隔要短！**

这也是正常的，因为 `func` 的执行所花费的时间“消耗”了一部分间隔时间。极端情况下，如果函数每次执行时间都超过 `delay` 设置的时间，那么每次调用之间将完全没有停顿。

**嵌套的 `setTimeout` 就能确保延时的固定（这里是 100 毫秒）。**

这是因为下一次调用是在前一次调用完成时再调度的。

**垃圾回收和 setInterval/setTimeout 回调（callback）**

当一个函数传入 `setInterval/setTimeout` 时，将为其创建一个内部引用，并保存在调度程序中。这样，即使这个函数没有其他引用，也能防止垃圾回收器（GC）将其回收。

```javascript
// 在调度程序调用这个函数之前，这个函数将一直存在于内存中
setTimeout(function() {...}, 100);
```

对于 `setInterval`，传入的函数也是一直存在于内存中，直到 `clearInterval` 被调用。

这里还要提到一个副作用。如果函数引用了外部变量（译注：闭包），那么只要这个函数还存在，外部变量也会随之存在。它们可能比函数本身占用更多的内存。因此，当我们不再需要调度函数时，最好取消它，即使这是个（占用内存）很小的函数。

#### 零延时的 setTimeout

这儿有一种特殊的用法：`setTimeout(func, 0)`，或者仅仅是 `setTimeout(func)`。

该函数被调度在当前脚本执行完成“之后”立即执行。

例如，下面这段代码会先输出 “Hello”，然后立即输出 “World”：

```javascript
setTimeout(() => alert("World"));
alert("Hello");
```

第一行代码“将调用安排到日程（calendar）0 毫秒处”。但是调度程序只有在当前脚本执行完毕时才会去“检查日程”，所以先输出 `"Hello"`，然后才输出 `"World"`。

**零延时实际上不为零（在浏览器中）**

在浏览器环境下，嵌套定时器的运行频率是受限制的。根据 [HTML5 标准](https://html.spec.whatwg.org/multipage/timers-and-user-prompts.html#timers) 所讲：“经过 5 重嵌套定时器之后，时间间隔被强制设定为至少 4 毫秒”。

让我们用下面的示例来看看这到底是什么意思。其中 `setTimeout` 调用会以零延时重新调度自身的调用。每次调用都会在 `times` 数组中记录上一次调用的实际时间。那么真正的延迟是什么样的？让我们来看看：

```javascript
let start = Date.now();
let times = [];

setTimeout(function run() {
  times.push(Date.now() - start); // 保存前一个调用的延时

  if (start + 100 < Date.now()) alert(times); // 100 毫秒之后，显示延时信息
  else setTimeout(run); // 否则重新调度
});

// 输出示例：
// 1,1,1,1,9,15,20,24,30,35,40,45,50,55,59,64,70,75,80,85,90,95,100
```

第一次，定时器是立即执行的（正如规范里所描述的那样），接下来我们可以看到 `9, 15, 20, 24...`。两次调用之间必须经过 4 毫秒以上的强制延时。

如果我们使用 `setInterval` 而不是 `setTimeout`，也会发生类似的情况：`setInterval(f)` 会以零延时运行几次 `f`，然后以 4 毫秒以上的强制延时运行。

这个限制来自“远古时代”，并且许多脚本都依赖于此，所以这个机制也就存在至今。

对于服务端的 JavaScript，就没有这个限制，并且还有其他调度即时异步任务的方式。

## 对象属性配置

## 原型和继承

## 类

## 错误处理

## Promise,async/await

## Generator

