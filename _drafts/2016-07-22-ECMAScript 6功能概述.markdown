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

本文不打算介绍所有的新特性,只做简单介绍以便你能迅速入门.

## ECMAScript 6 特性

### 箭头函数

可以通过`=>`快速使用箭头函数.他们在语法上相关特征类似于C #，java 8和CoffeeScript,他们都支持返回语句块和表达式的值.长得不像函数的样子,而且箭头函数中`this`总是绑定总是指向对象自身.箭头函数的语法比函数表达式的短.

```javascript
// 表达式语法
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

单参数不需要括号或大括号,返回语句是隐式的.

```javascript
let foo = ["Hello", "World"];

// ES6
let bar = foo.map(x => x.length);
// ES5
var bar = foo.map(function(x) { return x.length; });
```

更多信息: [MDN Arrow Functions](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Functions/Arrow_functions)

### classes

在ES6以前，我们实现一个类的功能的话，需要首先创建一个构造函数，然后扩展这个函数的原型方法，就像这样：

```javascript
function Person(name, age, gender) {
  this.name   = name;
  this.age    = age;
  this.gender = gender;
}

Person.prototype.addAge = function () {
  this.age += 1;
};
```

继承父类的子类需要这样：

```javascript
function Personal(name, age, gender, occupation, hobby) {
  Person.call(this, name, age, gender);
  this.occupation = occupation;
  this.hobby = hobby;
}

Personal.prototype = Object.create(Person.prototype);
Personal.prototype.constructor = Personal;
Personal.prototype.addtAge = function () {
  return Person.prototype.addAge.call(this) += 20;
};
```

而在ES6中使用了语法糖来实现这些步骤:

```javascript
class Person {
  constructor(name, age, gender) {
    this.name   = name;
    this.age    = age;
    this.gender = gender;
  }

  addtAge() {
    this.age += 1;
  }
}
```

继承父类的子类只要简单的使用 extends 关键字就可以了：

```javascript
class Personal extends Person {
  constructor(name, age, gender, occupation, hobby){
    super(name,age,gender);
    this.occupation = occupation;
    this.hobby = hobby;
  }

  addtAge(){
    super.addtAge();
    this.age+=20;
    console.log(this.age);
  }

  showInfo(){
    for(let item of [this.name,this.age,this.gender,this.occupation,this.hobby]){
      console.log(item);
    }
  }
}
```

### let/const

除了 var 以外，我们现在多了两个新的标识符来声明变量的存储，它们就是 let 和 const。值得注意的是:不同于`var`,`let`和`const`语句不会造成声明提升。

```javascript
console.log(foo); // 输出undefined
console.log(bar); // 报错ReferenceError

var foo = 2;
let bar = 2;
```

`let`声明的变量，只在`let`命令所在的代码块内有效.

```javascript
{
  let a = 10;
  var b = 1;
}

a // ReferenceError: a is not defined.
b // 1
```

想要了解更多的信息可以去看一下阮一峰大神写的一本<<ECMAScript 6 入门>>,也可以点击[这里](http://es6.ruanyifeng.com/#docs/let)查看.

### modules

ES6之前，浏览器端的模块化代码，我们使用像Browserify,Webpack这样的库， 在 Node.js 中，我们则使用 require。 在ES6中，我们现在可以直接使用AMD 和 CommonJS这些模块了。

* 在nodejs中导出包.

```javascript
module.exports = 1;
module.exports = { foo: 'bar' };
module.exports = ['foo', 'bar'];
module.exports = function bar () {};
```

* 在ES6中导出包.

```javascript
export let name = 'David';
export let age  = 25;​​
```

还可以这样导出对象:

```javascript
// 这个文件名设置为api.js
let showInfo = (name) => console.log(name);
let age = (age) => console.log(age);

export {showInfo,age}
```

然后这样使用:

```javascript
// test.js和api.js同目录
import api from './test.js';

api.showInfo("quietboy"); //quietboy
api.age(23); //23
```

有时候你的`api.js`文件里的函数不止这两个,很多的时候会很难管理,这时候我们需要这样调整文件代码:

```javascript
// 这个文件名设置为api.js
let showInfo = (name) => console.log(name);
let age = (age) => console.log(age);

var api = {
  showInfo,
  age
};

export default api;//设置默认出口
```


本文最后更新于2016-7-25.