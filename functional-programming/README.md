# Functional Programming


- [JS函数式编程指南](https://www.gitbook.com/book/llh911001/mostly-adequate-guide-chinese/details)
- [函数式编程离我们有多远？](https://www.h5jun.com/post/functional-how-far.html)
- [JavaScript 中的“纯函数”](https://www.h5jun.com/post/pure-function.html)
- [高阶函数对系统的“提纯”](https://www.h5jun.com/post/higher-order-function-play-with-pure-function.html)

***

# JS函数式编程指南

## 概念

纯函数是这样一种函数，即相同的输入，永远会得到相同的输出，而且没有任何可观察的副作用。

**纯函数的好处：**

- 可缓存性（Cacheable）

简单的一个实现

```
var memoize = function(f) {
  var cache = {};

  return function() {
    var arg_str = JSON.stringify(arguments);
    cache[arg_str] = cache[arg_str] || f.apply(f, arguments);
    return cache[arg_str];
  };
};
```

```
// 可以通过延迟执行的方式把不纯的函数转换为纯函数
// 返回了一个函数，当调用它的时候才会发请求
var pureHttpCall = memoize(function(url, params){
  return function() { return $.getJSON(url, params); }
});
```

- 可移植性／自文档化（Portable / Self-Documenting）

纯函数是完全自给自足的，它需要的所有东西都能轻易获得

```
// 不纯的
var signUp = function(attrs) {
  var user = saveUser(attrs);
  welcomeUser(user);
};

// 纯的
var signUp = function(Db, Email, attrs) {
  return function() {
    var user = saveUser(Db, attrs);
    welcomeUser(Email, user);
  };
};
```

- 可测试性（Testable）

纯函数让测试更加容易

- 合理性（Reasonable）
- 并行代码(咱不考虑)

## 柯里化（curry）

只传递给函数一部分参数来调用它，让它返回一个函数去处理剩下的参数。

```
// 可以一次性地调用 curry 函数，也可以每次只传一个参数分多次调用
var add = function(x) {
  return function(y) {
    return x + y;
  };
};

var increment = add(1);
var addTen = add(10);

increment(2);
// 3

addTen(2);
// 12
```

```
// 来享受下函数式
var curry = require('lodash').curry;

var match = curry(function(what, str) {
  return str.match(what);
});

var replace = curry(function(what, replacement, str) {
  return str.replace(what, replacement);
});

var filter = curry(function(f, ary) {
  return ary.filter(f);
});

var map = curry(function(f, ary) {
  return ary.map(f);
});

// 以上均统一使用了统一的一种模式，即策略性地把要操作的数据（String， Array）放到最后一个参数里（为什么呢？看完以下你就懂）

match(/\s+/g, "hello world");
// [ ' ' ]

match(/\s+/g)("hello world");// 等价，好理解
// [ ' ' ]

var hasSpaces = match(/\s+/g);
// function(x) { return x.match(/\s+/g) }

hasSpaces("hello world");
// [ ' ' ]

hasSpaces("spaceless");
// null

filter(hasSpaces, ["tori_spelling", "tori amos"]);
// ["tori amos"]

var findSpaces = filter(hasSpaces);
// function(xs) { return xs.filter(function(x) { return x.match(/\s+/g) }) }

findSpaces(["tori_spelling", "tori amos"]);
// ["tori amos"]

var noVowels = replace(/[aeiou]/ig);
// function(replacement, x) { return x.replace(/[aeiou]/ig, replacement) }

var censored = noVowels("*");
// function(x) { return x.replace(/[aeiou]/ig, "*") }

censored("Chocolate Rain");
// 'Ch*c*l*t* R**n'

// 这里表明的是一种“预加载”函数的能力，通过传递一到两个参数调用函数，就能得到一个记住了这些参数的新函数。
```

curry 的用处非常广泛，就像在 hasSpaces、findSpaces 和 censored 看到的那样，只需传给函数一些参数，就能得到一个新函数。

用 map 简单地把参数是单个元素的函数包裹一下，就能把它转换成参数为数组的函数。

```
var getChildren = function(x) {
  return x.childNodes;
};

var allTheChildren = map(getChildren);
```

只传给函数一部分参数通常也叫做局部调用（partial application），能够大量减少样板文件代码（boilerplate code）。考虑上面的 allTheChildren 函数，如果用 lodash 的普通 map 来写会是什么样的（注意参数的顺序也变了）：

```
var allTheChildren = function(elements) {
  return _.map(elements, getChildren);
};
```

通常我们不定义直接操作数组的函数，因为只需内联调用 map(getChildren) 就能达到目的。这一点同样适用于 sort、filter 以及其他的高阶函数（higher order function）（高阶函数：参数或返回值为函数的函数）。

## 代码组合

### 函数饲养

```
var compose = function(f,g) {
  return function(x) {
    return f(g(x));
  };
};


// 实例
var toUpperCase = function(x) { return x.toUpperCase(); };
var exclaim = function(x) { return x + '!'; };
var shout = compose(exclaim, toUpperCase);

shout("send in the clowns");
//=> "SEND IN THE CLOWNS!"
```

在 compose 的定义中，g 将先于 f 执行，因此就创建了一个`从右到左`的数据流(`控制参数流向`)。这样做的可读性远远高于嵌套一大堆的函数调用，如果不用组合，shout 函数将会是这样的：

```
var shout = function(x){
  return exclaim(toUpperCase(x));
};
```

让代码从右向左运行，而不是由内而外运行。所有组合的特性（便于理解）

```
// 结合律（associativity）
var associative = compose(f, compose(g, h)) == compose(compose(f, g), h);
// true


// 衍生重构
var loudLastUpper = compose(exclaim, toUpperCase, head, reverse);

// 或
var last = compose(head, reverse);
var loudLastUpper = compose(exclaim, toUpperCase, last);

// 或
var last = compose(head, reverse);
var angry = compose(exclaim, toUpperCase);
var loudLastUpper = compose(angry, last);

// 更多变种...
```

### pointfree

意思是函数无须提及将要操作的数据是什么样的。一等公民的函数、柯里化（curry）以及组合协作起来非常有助于实现这种模式。

```
// 非 pointfree，因为提到了数据：word
var snakeCase = function (word) {
  return word.toLowerCase().replace(/\s+/ig, '_');
};

// pointfree
var snakeCase = compose(replace(/\s+/ig, '_'), toLowerCase);
```

以上代码示范的是通过管道把数据在接受单个参数的函数间传递，无需word参数就能构造函数。

```
// 非 pointfree，因为提到了数据：name
var initials = function (name) {
  return name.split(' ').map(compose(toUpperCase, head)).join('. ');
};

// pointfree（注意从右到左顺序）
var initials = compose(join('. '), map(compose(toUpperCase, head)), split(' '));

initials("hunter stockton thompson");
// 'H. S. T'
```

pointfree 模式能够帮助我们减少不必要的命名，让代码保持简洁和通用。对函数式代码来说，pointfree 是非常好的石蕊试验，因为它能告诉我们一个函数是否是接受输入返回输出的小函数。比如，while 循环是不能组合的。

### debug

组合的一个常见错误是，在没有局部调用之前，就组合类似 map 这样接受两个参数的函数。

```
// 错误做法：我们传给了 `angry` 一个数组，根本不知道最后传给 `map` 的是什么东西。
var latin = compose(map, angry, reverse);

latin(["frog", "eyes"]);
// error


// 正确做法：每个函数都接受一个实际参数。
var latin = compose(map(angry), reverse);

latin(["frog", "eyes"]);
// ["EYES!", "FROG!"])
```

如果在 debug 组合的时候遇到了困难，那么可以使用下面这个实用的，但是不纯的 trace 函数来追踪代码的执行情况。

```
var trace = curry(function(tag, x){
  console.log(tag, x);
  return x;
});

var dasherize = compose(join('-'), toLower, split(' '), replace(/\s{2,}/ig, ' '));

dasherize('The world is a vampire');
// TypeError: Cannot read property 'apply' of undefined
```

### 范畴学

范畴学主要处理对象（object）、态射（morphism）和变化式（transformation）

以下是名为 id 的实用函数。这个函数接受随便什么输入然后原封不动地返回它：

```
var id = function(x){ return x; };

// identity
compose(id, f) == compose(f, id) == f;
// true
// 理解其中的无用性？
```

## 示例应用

### 声明式代码

```
// 命令式
var makes = [];
for (i = 0; i < cars.length; i++) {
  makes.push(cars[i].make);
}

// 声明式
var makes = cars.map(function(car){ return car.make; });


// 命令式
var authenticate = function(form) {
  var user = toUser(form);
  return logIn(user);
};

// 声明式
var authenticate = compose(logIn, toUser);
```

因为声明式代码不指定执行顺序，所以它天然地适合进行并行运算。它与纯函数一起解释了为何函数式编程是未来并行计算的一个不错选择——我们真的不需要做什么就能实现一个并行／并发系统。




