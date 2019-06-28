# 类与继承

JavaScript 中本无类，只有原型的概念，一切皆对象，对象是第一等公民。

## 简介

**Object** 是所有对象的基类，**所有对象都有 `[[Prototype]]` 来表示其原型**，推荐通过 `Object.getPrototypeOf(obj)` 来获取对象的原型，也可以通过属性(也可称为访问器方法，即 `Object.defineProperty(obj, key, descriptor)`，descriptor 中的 get 方法) `__proto__` 来访问。以下为各种情况下的 `__proto__` 值：

* 使用对象字面量创建的对象值为 `Object.prototype`。即当使用 `const obj = {name: 'troland'}`创建的对象
* 使用数组字面量创建的对象值为 `Array.prototype`。即当使用 `const ar = [1, 2]`创建的对象
* 函数的值为`Function.prototype`
* 所有采用 `new fun` 操作符创建的对象，`fun` 值为例如 `Function、Object、Array、Date` 等，其原型值均为 `fun.prototype`

可使用 `Object.create(sourceObject)` 以 `sourceObject` 作为新创建对象的原型。

函数比较特别，func 有 `func.prototoype` 原型对象，里面包含了一些关于原型链继承的信息比如`func.prototoype.__proto__` 指向原型链上的某个函数的原型。若不是继承类则是 `Object.prototype`。

## 继承

JavaScript 通过原型链继承来实现继承，ES6 以前并没有类似 Java 的父类子类概念。现在比较下 ES5 和 ES6 实现继承的方式：

![](https://user-images.githubusercontent.com/1475173/60313231-d89cb600-9990-11e9-987f-5f082cfb6a4e.png)

```
// ES5
function Person(name) {
  this.name = name
}
Person.prototype.describe = function () {
  return 'Person called ' + this.name
}

function Employee(name, title) {
  Person.call(this, name)
  this.title = title
}

Employee.prototype = Object.create(Person.prototype)
Employee.prototype.constructor = Employee
Employee.prototype.describe = function() {
  return Person.prototype.describe.call(this) +
    '(' + this.title + ')'
}
```

*ES5 中有多种实现原型继承的方法，这里只简单列出一种。*

```
// ES6
class Person {
  constructor(name) {
    this.name = name
  }
  describe() {
    return 'Person called ' + this.name
  }
}

class Employee extends Person {
  constructor(name, title) {
    super(name);
    this.title = title;
  }
  describe() {
    return super.describe() + ' (' + this.title + ')';
  }
}
```

在 ES6 中 constructor 构造函数内部必须首先使用 `super()` 方法，不然会报如下错误：

```
Uncaught ReferenceError: Must call super constructor in derived class before accessing 'this' or returning from derived constructor
```

**事实上 ES6 的 class 只是个语法糖，这意味着其并没有增加新的内容。**

函数有一个特殊的原型对象 `fun.prototype`，这也许是翻译的锅，当你说函数的原型的时候指的是 `fun.__proto__`，但是很多时候，其实是指的 `fun.prototype`，**这里的函数原型也是一个对象**。

来看一下 ES6 类继承转化为 ES5 的代码如下：

```
"use strict";

function _typeof(obj) {
  if (typeof Symbol === "function" && typeof Symbol.iterator === "symbol") {
    _typeof = function _typeof(obj) {
      return typeof obj;
    };
  } else {
    _typeof = function _typeof(obj) {
      return obj && typeof Symbol === "function" && obj.constructor === Symbol && obj !== Symbol.prototype ? "symbol" : typeof obj;
    };
  }
  return _typeof(obj);
}

function _possibleConstructorReturn(self, call) {
  if (call && (_typeof(call) === "object" || typeof call === "function")) {
    return call;
  }
  return _assertThisInitialized(self);
}

function _assertThisInitialized(self) {
  if (self === void 0) {
    throw new ReferenceError("this hasn't been initialised - super() hasn't been called");
  }
  return self;
}

function _get(target, property, receiver) {
  if (typeof Reflect !== "undefined" && Reflect.get) {
    _get = Reflect.get;
  } else {
    _get = function _get(target, property, receiver) {
      var base = _superPropBase(target, property);
      if (!base) return;
      var desc = Object.getOwnPropertyDescriptor(base, property);
      if (desc.get) {
        return desc.get.call(receiver);
      }
      return desc.value;
    };
  }
  return _get(target, property, receiver || target);
}

function _superPropBase(object, property) {
  while (!Object.prototype.hasOwnProperty.call(object, property)) {
    object = _getPrototypeOf(object);
    if (object === null) break;
  }
  return object;
}

function _getPrototypeOf(o) {
  _getPrototypeOf = Object.setPrototypeOf ? Object.getPrototypeOf : function _getPrototypeOf(o) {
    return o.__proto__ || Object.getPrototypeOf(o);
  };
  return _getPrototypeOf(o);
}

function _inherits(subClass, superClass) {
  if (typeof superClass !== "function" && superClass !== null) {
    throw new TypeError("Super expression must either be null or a function");
  }
  subClass.prototype = Object.create(superClass && superClass.prototype, {
    constructor: {
      value: subClass,
      writable: true,
      configurable: true
    }
  });
  if (superClass) _setPrototypeOf(subClass, superClass);
}

function _setPrototypeOf(o, p) {
  _setPrototypeOf = Object.setPrototypeOf || function _setPrototypeOf(o, p) {
    o.__proto__ = p;
    return o;
  };
  return _setPrototypeOf(o, p);
}

function _instanceof(left, right) {
  if (right != null && typeof Symbol !== "undefined" && right[Symbol.hasInstance]) {
    return right[Symbol.hasInstance](left);
  } else {
    return left instanceof right;
  }
}

function _classCallCheck(instance, Constructor) {
  if (!_instanceof(instance, Constructor)) {
    throw new TypeError("Cannot call a class as a function");
  }
}

function _defineProperties(target, props) {
  for (var i = 0; i < props.length; i++) {
    var descriptor = props[i];
    descriptor.enumerable = descriptor.enumerable || false;
    descriptor.configurable = true;
    if ("value" in descriptor) descriptor.writable = true;
    Object.defineProperty(target, descriptor.key, descriptor);
  }
}

function _createClass(Constructor, protoProps, staticProps) {
  if (protoProps) _defineProperties(Constructor.prototype, protoProps);
  if (staticProps) _defineProperties(Constructor, staticProps);
  return Constructor;
}

var Person =
  /*#__PURE__*/
  function() {
    function Person(name) {
      _classCallCheck(this, Person);

      this.name = name;
    }

    _createClass(Person, [{
      key: "describe",
      value: function describe() {
        return 'Person called ' + this.name;
      }
    }]);

    return Person;
  }();

var Employee =
  /*#__PURE__*/
  function(_Person) {
    _inherits(Employee, _Person);

    function Employee(name, title) {
      var _this;

      _classCallCheck(this, Employee);

      _this = _possibleConstructorReturn(this, _getPrototypeOf(Employee).call(this, name));
      _this.title = title;
      return _this;
    }

    _createClass(Employee, [{
      key: "describe",
      value: function describe() {
        return _get(_getPrototypeOf(Employee.prototype), "describe", this).call(this) + ' (' + this.title + ')';
      }
    }]);

    return Employee;
  }(Person);
```

从以上代码可得出如下结论：

- 子类的原型是父类即 `Employee.__proto__ === Person`
  为什么要这么做？这里主要是为了在实例化子类的时候调用父类的构造函数

- 子类的原型对象的原型为父类的原型即 `Employee.prototype.__proto__ === Person.prototype`

  这里相对来说比较容易理解，即原型链的继承

**这里相信蛮多小伙伴都感觉有点不能理解，什么原型对象的原型，对象的原型，有些绕，不过这里只要记住所有对象都有原型即内部的 `[[Prototype]]`即可，再想想原型链上的连接，慢慢来就会明白的。**

## 静态方法(即类方法)，静态数据(即类数据)及私有数据

静态方法代码如下：

```
// ES6
class Person {
  constructor(name) {
    this.name = name
  }
  static classMethod () {
  	alert('this is Person class Method')
  }
  describe() {
    return 'Person called ' + this.name
  }
}

// ES5
function Person(name) {
  this.name = name
}
Person.classMethod = function () {
	alert('this is Person class Method')
}
```

静态数据可使用如下代码：

```
// ES6
class Person {
  constructor(name) {
    this.name = name
  }
  static classMethod () {
  	alert('this is Person class Method')
  }
  static get COUNTRY() {
    return 'China'
  }
  describe() {
    return 'Person called ' + this.name
  }
}
Person.COUNTRY // 返回 China

// ES5
function Person(name) {
  this.name = name
}
Person.COUNTRY = 'China'
Person.classMethod = function () {
	alert('this is Person class Method')
}
```

静态数据若使用 babel 进行转换可使用 `yarn add -D @babel/plugin-proposal-class-properties`，.babelrc 如下：

```
{
	"plugins": ["@babel/plugin-proposal-class-properties"]
}
```

代码如下：

```
// ES6
class Person {
  constructor(name) {
    this.name = name
  }
  static classMethod () {
  	alert('this is Person class Method')
  }
  static _hands = 2;
  static showHands () {
  	return Person._hands
  }
}
```

*Java* 的变量类型如下：

```
            │ Class │ Package │ Subclass │ Subclass │ World
            │       │         │(same pkg)│(diff pkg)│ 
────────────┼───────┼─────────┼──────────┼──────────┼────────
public      │   +   │    +    │    +     │     +    │   +     
────────────┼───────┼─────────┼──────────┼──────────┼────────
protected   │   +   │    +    │    +     │     +    │         
────────────┼───────┼─────────┼──────────┼──────────┼────────
no modifier │   +   │    +    │    +     │          │    
────────────┼───────┼─────────┼──────────┼──────────┼────────
private     │   +   │         │          │          │    

 + : 可访问         空白 : 不可访问
```

所谓私有属性即私有属性不能直接被外界访问，只能由类里面的方法进行访问。

一般实例属性可以使用如下代码：

```
class Person {
  name;
  constructor(name) {
    this.name = name
  }
}
```

JavaScript 中若要实现私有属性，最好保证私有属性不会发生命名冲突。

```
const utils = (() => {
  var props = {
    name: 'cai'
  }
  
  return {
    getUserName: function () {
      return props.name
    }
  }
})()
```

可以使用以上方法来实现变量的私有性。保证函数变量命名不发生冲突除了使用类似 `_name` 这样的方式是不够的。可以使用 `WeakMap` 或者 `Symbol` 来实现变量不会发生冲突。

```
const _name = Symbol('name')
class Person {
  constructor(name) {
    this[_name] = name
  }
}
const person = new Person('cai')
person[Reflect.ownKeys(person)[0]] // 输出 cai
```

不能够直接使用 `person._name` 来访问实例私有属性，只能通过 `person[Reflect.ownKeys(person)[0]]` 来进行访问。

```
const _name = new WeakMap()
class Person {
  constructor(name) {
    _name.set(this, name)
  }
}
const person = new Person('cai')
_name.get(person) // 输出 cai
```

## 重载

关于方法重载(override)。这里有一点需要注意的是在 ES5 中实现原型继承的时候，不能直接写 `Employee.prototype = Person.prototype`，否则会导致修改子类原型方法会修改父类的同名方法，这是不科学的。

`Object.setPrototypeOf(Employee.prototype, Person.prototype)` 这里只是把 `Employee.prototype.__proto__` 指向了 `Person.prototype` 从而继承父类的方法，可以理解为只要是使用指针来进行赋值而不是直接使用赋值运算，那么对于子类的方法的修改就不会影响到父类原型上的方法。

## 类修饰符

由 Python 引申而来，是不是可以把修饰符理解为待出嫁的姑娘的嫁妆，出嫁前(调用类相关方法等前)要用来好好打扮一下。

在 TC39 委员修饰符提案中，JavaScript 修饰符处于 `stage-2` 阶段即草案阶段。

内置以下四种基础修饰符：

* `@wrap`：使用指定函数的返回值来替换掉指定的方法或者整个类
* `@register`：创建完类之后的回调
* `@expose`：创建完类之后调用指定函数的回调来访问私有字段或者私有方法
* `@initialize`：创建类实例的时候运行指定回调

创建修饰符方法如下：

* 使用 `decorator @foo { }` 创建修饰符
* 不可以把修饰符看成是值;它们必须应用于类中，可以进行修饰符的组合，导出，引入等
* 修饰符以 `@` 作为起始字符 ；`@decorator` 构成一个独立的命名空间
* 只能以几种固定的方式组合修饰符，这样就更加有利于代码的静态分析

安装 `yarn add -D @babel/core @babel/plugin-proposal-decorators `，**.babelrc ** 如下：

```
{
  "plugins": ["@babel/plugin-proposal-decorators"]
}
```

安装 `yarn add -D core-decorators`，[core-decorators](https://github.com/jayphelps/core-decorators) 里面有蛮多已经写好的修饰符，可以直接拿来用，或者自己创建所需的修饰符。

代码如下：

```
import { readonly } from 'core-decorators'
class Person {
  @readonly
  _hands = 2;
}

let student = new Person()
student._hands = 3
// cannot assign to read only property '_hands' of [object Object]
```

## 参考

* https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/proto
* https://exploringjs.com/es6/ch_classes.html#details-of-subclassing
* [http://www.ecma-international.org/ecma-262/6.0/#sec-class-definitions-static-semantics-early-errors](http://www.ecma-international.org/ecma-262/6.0/#sec-class-definitions-static-semantics-early-errors)
* https://stackoverflow.com/questions/215497/what-is-the-difference-between-public-protected-package-private-and-private-in
* https://github.com/jayphelps/core-decorators
* https://medium.com/google-developers/exploring-es7-decorators-76ecb65fb841
* [http://es6.ruanyifeng.com/#docs/decorator#Babel-%E8%BD%AC%E7%A0%81%E5%99%A8%E7%9A%84%E6%94%AF%E6%8C%81](http://es6.ruanyifeng.com/#docs/decorator#Babel-转码器的支持)
* https://github.com/tc39/proposal-decorators