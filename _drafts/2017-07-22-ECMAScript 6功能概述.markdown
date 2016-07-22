---
layout: post
title:  "ECMAScript 6功能概述"
date:   2016-07-22 15:21:03 +0800
tags: ECMAScript6,ES6,Javascript
---

ECMAScript 6也被称为ECMAScript 2015,是ECMAScript的最新标准,我们简称ES6.ES6是JavaScript距离2009年的ES5为止最重要的一次升级.主要的JavaScript引擎目前都在实现了这些新特性,可以[点击这里](http://kangax.github.io/compat-table/es6/)查看进度.

[点击这里](http://www.ecma-international.org/ecma-262/6.0/)查看ECMAScript 6的完整规范.

ES6包括以下新的特性：
- [arrows](#arrows)
- [classes](#classes)
- [enhanced object literals](#enhanced-object-literals)
- [template strings](#template-strings)
- [destructuring](#destructuring)
- [default + rest + spread](#default--rest--spread)
- [let + const](#let--const)
- [iterators + for..of](#iterators--forof)
- [generators](#generators)
- [unicode](#unicode)
- [modules](#modules)
- [module loaders](#module-loaders)
- [map + set + weakmap + weakset](#map--set--weakmap--weakset)
- [proxies](#proxies)
- [symbols](#symbols)
- [subclassable built-ins](#subclassable-built-ins)
- [promises](#promises)
- [math + number + string + array + object APIs](#math--number--string--array--object-apis)
- [binary and octal literals](#binary-and-octal-literals)
- [reflect api](#reflect-api)
- [tail calls](#tail-calls)

## ECMAScript 6 特性

### 箭头函数
可以通过`=>`快速使用箭头函数.他们在语法上相关特征类似于C #，java 8和CoffeeScript,他们都支持返回语句块和表达式的值.长得不像函数的样子,而且箭头函数中`this`总是绑定总是指向对象自身.

```javascript
// 表达式
var odds = evens.map(v => v + 1);
var nums = evens.map((v, i) => v + i);
var pairs = evens.map(v => ({even: v, odd: v + 1}));

// 语句块
nums.forEach(v => {
  if (v % 5 === 0)
    fives.push(v);
});

// 对象体内的this
var bob = {
  _name: "Bob",
  _friends: [],
  printFriends() {
    this._friends.forEach(f =>
      console.log(this._name + " knows " + f));
  }
}
```

更多信息: [MDN Arrow Functions](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Functions/Arrow_functions)
