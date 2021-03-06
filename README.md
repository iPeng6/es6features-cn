# ES2015

## 简介

> 参考
>
> Babel Learn ES2015： https://babeljs.io/docs/en/learn

ES2015 是该语言的一次重大更新，自 2009 年被标准化的 ES5 以来的一次最主要更新。主要的 JavaScript 引擎也在实现这些特性。

查看[ES2015 标准](http://www.ecma-international.org/ecma-262/6.0/index.html)的所有规格

## ECMAScript 2015 特性

### Arrows and Lexical This

箭头函数是使用 => 语法的简写函数，支持表达式和带函数体的写法，与普通函数不同的是，箭头函数与上下文共享同一个词法 this, 如果箭头函数在另一个函数里面那么它将共享它父级函数的“arguments”变量

```js
// Expression bodies
var odds = evens.map(v => v + 1);
var nums = evens.map((v, i) => v + i);

// Statement bodies
nums.forEach(v => {
  if (v % 5 === 0) fives.push(v);
});

// Lexical this
var bob = {
  _name: 'Bob',
  _friends: [1],
  printFriends() {
    this._friends.forEach(f =>
      console.log(this._name + ' knows ' + f, this === bob),
    );
  },
};
bob.printFriends(); // returns: Bob knows 1 true

// Lexical arguments
function square() {
  let example = () => {
    let numbers = [];
    for (let number of arguments) {
      numbers.push(number * number);
    }

    return numbers;
  };

  return example();
}
square(2, 4, 7.5, 8, 11.5, 21); // returns: [4, 16, 56.25, 64, 132.25, 441]
```

### Classes

ES2015 classes 只是一种基于原型的面向对象模式的语法糖，简单方便的声明形式使得类模式更易使用，也增加了互操作性。类支持基于原型的继承、super 调用、实例方法、静态方法和构造函数。

```js
class Person {
  constructor(name) {
    this.name = name;
  }
  hello() {
    return 'Hello, I am ' + this.name + '.';
  }
}
class Actor extends Person {
  hello() {
    return super.hello() + ' I am an actor.';
  }
  static birth() {
    return new Person();
  }
  get fullName() {
    return `${this.firstName} ${this.lastName}`;
  }
  set age(years) {
    this.theAge = years;
  }
}
var tomCruise = new Actor('Tom Cruise');
tomCruise.hello();
```

### Enhanced Object Literals

增强的对象字面量，支持构造时指定原型`__proto__`, 属性`handle: handle`赋值简写，方法定义及 super 调用，使用计算属性名。这些加起来使得字面量跟类声明更相近，基于对象的设计也从这种便利中获益。

```js
var obj = {
  // 1. Sets the prototype. "__proto__" or '__proto__' would also work.
  __proto__: theProtoObj,
  // Computed property name does not set prototype or trigger early error for
  // duplicate __proto__ properties.
  ['__proto__']: somethingElse,
  // 2. Shorthand for ‘handler: handler’
  handler,
  // 3. Methods
  toString() {
    // 4. Super calls
    return 'd ' + super.toString();
  },
  // 5. Computed (dynamic) property names
  ['prop_' + (() => 42)()]: 42,
};
```

### Template Strings

模板字符串提供了构建字符串的语法糖。这类似于 Perl、Python 和其他语言中的字符串插值特性。此外，作为可选项，使用标签可以自定义字符串的构建行为，避免注入攻击，或者基于字符串构建高阶的数据结构。

```js
// Basic literal string creation
const basic = `This is a pretty little template string.`;

// Multiline strings
const multi = `In ES5 this is
 not legal.`;

// Interpolate variable bindings
var name = 'Bob',
  time = 'today';
`Hello ${name}, how are you ${time}?`;

// Unescaped template strings
String.raw`In ES5 "\n" is a line-feed.`;

// tag template

// Construct an HTTP request prefix is used to interpret the replacements and construction
tag`Hello ${a + b} world ${a * b}`;
// the same as
tag(['Hello ', ' world ', ''], a + b, a * b);

GET`http://foo.org/bar?a=${a}&b=${b}
    Content-Type: application/json
    X-Credentials: ${credentials}
    { "foo": ${foo},
      "bar": ${bar}}`(myOnReadyStateChangeHandler);
```

### Destructuring

解构允许使用模式匹配赋值，支持数组和对象。解构是会失败弱化的，类似对象查找过程 `foo['bar']`，如果未找到则置为 undefined 也可以指定默认值。

```js
// list matching
var [a, , b] = [1, 2, 3];
a === 1;
b === 3;

// object matching
var {
  op: a,
  lhs: { op: b },
  rhs: c,
} = getASTNode();

// object matching shorthand
// binds `op`, `lhs` and `rhs` in scope
var { op, lhs, rhs } = getASTNode();

// Can be used in parameter position
function g({ name: x }) {
  console.log(x);
}
g({ name: 5 });

// Fail-soft destructuring
var [a] = [];
a === undefined;

// Fail-soft destructuring with defaults
var [a = 1] = [];
a === 1;

// Destructuring + defaults arguments
function r({ x, y, w = 10, h = 10 }) {
  return x + y + w + h;
}
r({ x: 1, y: 2 }) === 23;
```

### Default + Rest + Spread

被调函数支持设置参数默认值，`...` 运算符可以将数组展开成连续的参数给函数调用，`...` 在定义函数时也可以将剩余的参数收集成一个数组，剩余参数 Rest 代替了`arguments`的使用，更直接的解决常见问题。

```js
// Default
function f(x, y = 12) {
  // y is 12 if not passed (or passed as undefined)
  return x + y;
}
f(3) == 15;

// Rest
function f(x, ...y) {
  // y is an Array
  return x * y.length;
}
f(3, 'hello', true) == 6;

// Spread
function f(x, y, z) {
  return x + y + z;
}
// Pass each elem of array as argument
f(...[1, 2, 3]) == 6;
```

### Let + Const

let 和 const 都是绑定构造的块级作用域。let 是新的 var。const 是单次赋值的。const 的静态限制禁止变量在赋值前使用。

```js
function f() {
  {
    let x;
    {
      // this is ok since it's a block scoped name
      const x = 'sneaky';
      // error, was just defined with `const` above
      x = 'foo';
    }
    // this is ok since it was declared with `let`
    x = 'bar';
    // error, already declared above in this block
    let x = 'inner';
  }
}
```

### Iterators + For..Of

Iterator 对象让 javascript 拥有了像 CLR IEnumerable 和 Java Iterable 一样自定义迭代器的能力。将 for..in 转换成基于迭代器的自定义遍历的 for..of 形式。不需要实现一个类似 LINQ 中惰性设计模式的数组。

```js
let fibonacci = {
  [Symbol.iterator]() {
    let pre = 0,
      cur = 1;
    return {
      next() {
        [pre, cur] = [cur, pre + cur];
        return { done: false, value: cur };
      },
    };
  },
};

for (var n of fibonacci) {
  // truncate the sequence at 1000
  if (n > 1000) break;
  console.log(n);
}

// Getting the iterator from an array returns an iterator of values
const a = [1, 2, 3];
let it = a[Symbol.iterator]();
console.log(it.next().value); //1
console.log(it.next().value); //2
console.log(it.next().value); //3

//get the index as well, using `entries()`
for (const [i, v] of ['a', 'b', 'c'].entries()) {
  console.log(i, v);
}
```

Iteration 基于 [duck-typed](https://en.wikipedia.org/wiki/Duck_typing) 的接口(以下使用 TypeScript 的语法，仅供解释用)

```js
interface IteratorResult {
  done: boolean;
  value: any;
}
interface Iterator {
  next(): IteratorResult;
}
interface Iterable {
  [Symbol.iterator](): Iterator
}
```

### Generators

Generators 使用 `function*` 和 `yield` 的语法简化了迭代器的书写。一个使用 `function*` 声明的函数返回一个 Generator 实例。Generators 也是迭代器的一种，但它拥有额外的 next 和 throw 方法。这允许值回到 generator 中，所以 yield 是一种返回（或抛出）值的表达式形式。

注意：可以用它来进行类似‘await’的异步编程，具体可以查看 ES7 的 [await](https://github.com/lukehoban/ecmascript-asyncawait) 提案

```js
var fibonacci = {
  [Symbol.iterator]: function*() {
    var pre = 0,
      cur = 1;
    for (;;) {
      var temp = pre;
      pre = cur;
      cur += temp;
      yield cur;
    }
  },
};

for (var n of fibonacci) {
  // truncate the sequence at 1000
  if (n > 1000) break;
  console.log(n);
}
```

这个 generator 接口定义是（使用 TypeScript 类型语法定义来解释）：

```ts
interface Generator extends Iterator {
  next(value?: any): IteratorResult;
  throw(exception: any);
}
```

Unicode

支持完整 Unicode 的非破坏性添加，包括字符串中新的 unicode 字面量和新的 RegExp `u` 模式来处理码位（字符在字符集中的位置），以及新的 APIs 在 [21bit 码位级别](https://zh.wikipedia.org/wiki/Unicode#.E7.BC.96.E7.A0.81.E6.96.B9.E5.BC.8F) 上处理字符串，
增加这些支持后可以使用 Javascript 构建全球化应用。

```js
// same as ES5.1
'𠮷'.length == 2;

// new RegExp behaviour, opt-in ‘u’
'𠮷'.match(/./u)[0].length == 2;

// new form
('\u{20BB7}' == '𠮷') == '\uD842\uDFB7';

// new String ops
'𠮷'.codePointAt(0) == 0x20bb7;

// for-of iterates code points
for (var c of '𠮷') {
  console.log(c);
}
```

### Modules

在 ES2015 之前，至少有三个主要的模块标准竞争，这些标准使社区支离破碎：

- AMD
- RequireJS
- CommonJS

ES2015 将这些标准化为通用的格式，在语言层面上得到了支持。运行时行为由宿主加载器定义，隐式异步模型 - 直到全部请求的模块均可用且经处理后，才会执行当前模块内的代码。

```js
// lib/math.js
export function sum(x, y) {
  return x + y;
}
export var pi = 3.141593;
```

```js
// app.js
import * as math from 'lib/math';
console.log('2π = ' + math.sum(math.pi, math.pi));
```

```js
// otherApp.js
import { sum, pi } from 'lib/math';
console.log('2π = ' + sum(pi, pi));
```

一些额外的新特性，包括 `export default` 以及 `export *`

```js
// lib/mathplusplus.js
export * from 'lib/math';
export var e = 2.71828182846;
export default function(x) {
  return Math.exp(x);
}
```

```js
// app.js
import exp, { pi, e } from 'lib/mathplusplus';
console.log('e^π = ' + exp(pi));
```

### Map + Set + WeakMap + WeakSet

用于实现常见算法的高效数据结构，WeakMaps 提供不会泄露的对象键(对象作为键名，而且键名指向对象)索引表 注：所谓的不会泄露，指的是对应的对象可能会被自动回收，回收后 WeakMaps 自动移除对应的键值对，有助于防止内存泄露

WeekSet 与 Set 区别：

1. WeakSet 的成员只能是对象，不能是其他类型的值
2. WeakSet 中的对象都是弱引用，如果其他对象都不再引用该对象，那么垃圾回收机制随时会回收该对象所占用的内存，由于垃圾回收的不可预测性，所以 ES6 规定 WeakSet 不可遍历。

WeekMap 与 Map 区别：

1. WeakMap 只接受对象作为键名（null 除外），不接受其他类型的值作为键名
2. WeakMap 的键名所指向的对象，不计入垃圾回收机制，注意弱引用的只是键名，而不是键值。键值依然是正常引用。

```js
// Sets
var s = new Set();
s.add('hello')
  .add('goodbye')
  .add('hello');
s.size === 2;
s.has('hello') === true;

// Maps
var m = new Map();
m.set('hello', 42);
m.set(s, 34);
m.get(s) == 34;

// Weak Maps
var wm = new WeakMap();
wm.set(s, { extra: 42 });
wm.size === undefined;

// Weak Sets
var ws = new WeakSet();
ws.add({ data: 42 });
// Because the added object has no other references, it will not be held in the set
// 因为加入的对象没有任何引用，它将不被保留在集合中，也就是可能会消失
```

### Proxies

代理可以创造一个具备宿主对象全部可用行为的对象。可用于拦截、对象虚拟化、日志/分析等

```js
// Proxying a normal object
var target = {};
var handler = {
  get: function(receiver, name) {
    return `Hello, ${name}!`;
  },
};

var p = new Proxy(target, handler);
p.world === 'Hello, world!';
```

```js
// Proxying a function object
var target = function() {
  return 'I am the target';
};
var handler = {
  apply: function(receiver, ...args) {
    return 'I am the proxy';
  },
};

var p = new Proxy(target, handler);
p() === 'I am the proxy';
```

所有运行时级别的元操作都有对应的陷阱（使得这些操作都可以被代理）

```js
var handler =
{
  // target.prop
  get: ...,
  // target.prop = value
  set: ...,
  // 'prop' in target
  has: ...,
  // delete target.prop
  deleteProperty: ...,
  // target(...args)
  apply: ...,
  // new target(...args)
  construct: ...,
  // Object.getOwnPropertyDescriptor(target, 'prop')
  getOwnPropertyDescriptor: ...,
  // Object.defineProperty(target, 'prop', descriptor)
  defineProperty: ...,
  // Object.getPrototypeOf(target), Reflect.getPrototypeOf(target),
  // target.__proto__, object.isPrototypeOf(target), object instanceof target
  getPrototypeOf: ...,
  // Object.setPrototypeOf(target), Reflect.setPrototypeOf(target)
  setPrototypeOf: ...,
  // for (let i in target) {}
  enumerate: ...,
  // Object.keys(target)
  ownKeys: ...,
  // Object.preventExtensions(target)
  preventExtensions: ...,
  // Object.isExtensible(target)
  isExtensible :...
}
```

### Symbols

Symbol 能够实现对象状态的访问控制，允许使用 string(与 ES5 相同)或 symbol 作为键来访问属性。Symbol 是一个新的原语类型，可选的 name 参数可以用于调试——但并不是标识符的一部分（哪怕一样的 name 的两个 Symbol 也是不等的）。Symbol 是独一无二的(如同 gensym（所产生的符号)，但不是私有的，因为它们可以通过类似 Object.getOwnPropertySymbols 的反射特性暴露出来。

```js
(function() {

  // module scoped symbol
  var key = Symbol("key");

  function MyClass(privateData) {
    this[key] = privateData;
  }

  MyClass.prototype = {
    doStuff: function() {
      ... this[key] ...
    }
  };

  // Limited support from Babel, full support requires native implementation.
  typeof key === "symbol"
})();

var c = new MyClass("hello")
c["key"] === undefined
```

### Subclassable Built-ins

ES2015 内建对象如 `Array`、`Date` 和 DOM `Elements` 可被子类化

```js
// User code of Array subclass
class MyArray extends Array {
  constructor(...args) {
    super(...args);
  }
}

var arr = new MyArray();
arr[1] = 12;
arr.length == 2;
```

### Math + Number + String + Object APIs

许多新加库包括 Math 库， Array 转换 helpers 和用于拷贝的 Object.assign

```js
Number.EPSILON;
Number.isInteger(Infinity); // false
Number.isNaN('NaN'); // false

Math.acosh(3); // 1.762747174039086
Math.hypot(3, 4); // 5
Math.imul(Math.pow(2, 32) - 1, Math.pow(2, 32) - 2); // 2

'abcde'.includes('cd'); // true
'abc'.repeat(3); // "abcabcabc"

Array.from(document.querySelectorAll('*')); // Returns a real Array
Array.of(1, 2, 3); // Similar to new Array(...), but without special one-arg behavior
[0, 0, 0].fill(7, 1); // [0,7,7]
[1, 2, 3].findIndex(x => x == 2); // 1
['a', 'b', 'c'].entries(); // iterator [0, "a"], [1,"b"], [2,"c"]
['a', 'b', 'c'].keys(); // iterator 0, 1, 2
['a', 'b', 'c'].values(); // iterator "a", "b", "c"

Object.assign(Point, { origin: new Point(0, 0) });
```

### Binary and Octal Literals

加入了对二进制(b)和八进制(o)字面量的支持

```js
0b111110111 === 503; // true
0o767 === 503; // true
```

### Promises

Promise 是用来进行异步编程的库，Promise 是对一个“将来可能会变得可用”的值的第一类表示，Promise 在现有的许多 JavaScript 库中使用

```js
function timeout(duration = 0) {
  return new Promise((resolve, reject) => {
    setTimeout(resolve, duration);
  });
}

var p = timeout(1000)
  .then(() => {
    return timeout(2000);
  })
  .then(() => {
    throw new Error('hmm');
  })
  .catch(err => {
    return Promise.all([timeout(100), timeout(200)]);
  });
```

### Reflect API

整个反射 API 暴露了对象运行时级别的元操作，这实际上与 Proxy API 刚好相反，它允许在 proxy 捕获时调用与 Proxy 接口相对应的元操作。在实现 proxies 时尤其有用。

```js
var O = { a: 1 };
Object.defineProperty(O, 'b', { value: 2 });
O[Symbol('c')] = 3;

Reflect.ownKeys(O); // ['a', 'b', Symbol(c)]

function C(a, b) {
  this.c = a + b;
}
var instance = Reflect.construct(C, [20, 22]);
instance.c; // 42
```

### Tail Calls

尾调用确保堆栈不会无限增长，在面对无限制输入时确保递归算法的安全。

```js
function factorial(n, acc = 1) {
    "use strict";
    if (n <= 1) return acc;
    return factorial(n - 1, n * acc);
}

// Stack overflow in most implementations today,
// but safe on arbitrary inputs in ES2015
factorial(100000)
```
