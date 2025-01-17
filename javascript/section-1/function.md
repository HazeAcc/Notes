---
title: "函数"
date: "2019-08-13"
---

# JavaScript | 函数 <!-- omit in toc -->

介绍函数的基础语法和类型。

## 目录 <!-- omit in toc -->

- [函数](#函数)
  - [创建函数](#创建函数)
  - [函数声明与函数表达式的区别](#函数声明与函数表达式的区别)
- [函数种类](#函数种类)
  - [回调函数](#回调函数)
  - [匿名函数](#匿名函数)
  - [箭头函数](#箭头函数)
  - [闭包](#闭包)

<br>

---

<br>

## 函数

`JavaScript`支持提供的参数个数少于声明时所需的参数个数（类似python），未赋值的参数会以默认值`undefined`代入函数中。

支持设置默认值。

```js
function showMessage(from, text = "no text given") {
  alert( from + ": " + text );
}

showMessage("Haze"); // Haze: no text given
```

### 创建函数

函数是一种特殊的值。

创建函数有两种方法：

1. 函数声明

    ```js
    function sayHi() {
      alert( "Hello" );
    }
    ```

2. 函数表达式

    ```js
    let sayHi = function() {
      alert( "Hello" );
    }; // 注意这里有一个分号，因为它是表达式
    ```

函数省略 return 语句时自动返回 undefined，注意 return 语句必须一行写完（否则会在一行末尾处自动补上分号）。

### 函数声明与函数表达式的区别

- **函数表达式**在执行到达时创建并可用。
- **函数声明**可用于整个脚本/代码块。
- 当一个函数声明在一个代码块内运行时，它在该块内的任何地方都是可见的。
- 函数声明往往作用域更自由，函数表达式则更加定制化，适合实现特殊需求的时候（如根据不同的输入创建不同的函数体）。
- 两者都可以使用的时候**推荐使用函数声明**，因为其组织代码更方便、更容易查找。

## 函数种类

### 回调函数

```js
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

ask("Do you agree?", showOk, showCancel);
```

### 匿名函数

上面回调函数的[例子](#回调函数)可以简写为：

```js
function ask(question, yes, no) {
  if (confirm(question)) yes()
  else no();
}

ask(
  "Do you agree?",
  // ↓ 重点
  function() { alert("You agreed."); },
  function() { alert("You canceled the execution."); }
);
```

### 箭头函数

箭头函数是ES6特性。

语法：

```js
let func = (arg1, arg2, ...argN) => expression
```

上面的例子可以简写为：

```js
function ask(question, yes, no) {
  if (confirm(question)) yes()
  else no();
}

ask(
  "Do you agree?",
  () => alert("You agreed."),
  () => alert("You canceled the execution.")
);
```

只有一个参数时括号可以省略。

```js
let double = n => n * 2;
// same as
let _double = function(n) { return n * 2 };
```

两个及以上参数需要括号。

```js
let sum = (a, b) => a + b;
// same as
let _sum = function(a, b) {
  return a + b;
};
```

需要多个表达式或语句时则加上花括号并显式return。

```js
let sum = (a, b) => {
  let result = a + b;
  return result;
};
```

::: tip

注意匿名函数、箭头函数与普通的函数三者的`this`（后面会提到）的指向规则都不同，使用时需要多加注意。

:::

### 闭包

闭包是一种比较特殊的函数，其指的是有权访问父作用域的函数，即使在父函数关闭之后。

其能够将某全局变量实现局部私有化。即好似这样一种行为：

有这样一个闹钟对象，其具有一个属性 `counter` 和一个函数 `add`，每次调用 `add` 就会为 `counter` 加1。因为是对象的原因，`counter` 会保存 `add` 的次数。

但使用闭包的方式，无需创建对象即可实现。

```js
var add = (function () {
    var counter = 0;
    return function () {return counter += 1;}
})();

add();
add();
add();

// 计数器目前是 3 
```

变量 `add` 的赋值是自调用函数的返回值，这个自调用函数只运行一次。它设置计数器为零（0），并返回函数表达式。这样 `add` 成为了函数。最“精彩的”部分是它能够访问父作用域中的计数器 `counter`。

这被称为 JavaScript 闭包。它使函数拥有“私有”变量成为可能。计数器被这个匿名函数的作用域保护，并且只能使用 `add` 函数来修改。
