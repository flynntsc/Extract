# 精华

大多数编程语言皆有精华和糟粕，请取其精华而使用。

# 语法

建议 //注释 来代替 /**/注释，相对更安全。

false、null、undefined、空字符串‘’、数字0、数字NaN 为false,其他为true;


# 对象

对象通过引用来传递。永远不会被复制。

```
var x = stooge;
x.nickname = 'Curly';
var nick = stooge.nickname;
// 因为x和stooge是指向同一个对象的引用

var a = {},
    b = {},
    c = {};
// a、b和c每个都引用一个不同的空对象
var a = b = c = {};
// a,b和c都引用同一个空对象，当然实际别这样用
```

# 函数

### 尾递归

```
var factorial = function(i, a) {
    a = a || 1;
    return i < 2 ? a : factorial(i - 1, a * i);
}
console.log(factorial(4)); // 24
```

### 闭包

自运行函数，将返回的结果赋值给myObject，即包含两个方法的对象，独自享有访问value的特权。

```
const myObject = (() => {
    let value = 0;
    return {
        increment: (inc) => {
            value += typeof inc === 'number' ? inc : 1;
        },
        getValue: () => {
            return value;
        }
    }
})();
```

### 柯里化

函数也是值，柯里化把函数与传递给它的参数相结合，产生一个新的函数。

```
Function.prototype.curry = function() {
    let slice = Array.prototype.slice,
        args = slice.apply(arguments),
        that = this;
    return function() {
        return that.apply(null, args.concat(slice.apply(arguments)));
    }
}

function add(...num) {
    return num ? num : 1;
}
let add1 = add.curry(1);
console.log(add1(6)); // [1,6]
```

```
function foo(){
  // does something
}

// later on we want to enhance foos behavior... normally we would have to do this:

var _foo = foo
foo = function(){
  // do some enhanced functionality...
  _foo(); // now call the original
}

// but with wrap we could instead do this:

foo = foo.wrap(original){
  // do some enhanced functionality...
  original();  // now call the original
}
```

### 记忆

常用于单例模式，具有缓存/记忆功能。

```
var fibonacci = function() {
    var arr = [0, 1];
    var fib = function(n) {
        var result = arr[n];
        if (typeof result !== 'number') {
            result = fib(n - 1) + fib(n - 2);
            arr[n] = result;
        }
        return result;
    }
    return fib;
}();
console.log(fibonacci(2))
```

# 继承

### 对象说明符

包含多个参数时，可用对象来表现，更友好不惧顺序。

```
var myObject = maker({
	first:f,
	middle:m,
	last:l,
	state:s,
	city:c
})
```

### 原型

纯粹的原型模式中，对象 > 类。基于原型的继承相比基于类的继承更为简单：一个新对象可以继承一个旧对象。

```
var myMammal = {
    name: 'Name is myMammal',
    get: function() {
        return this.name;
    },
    say: function() {
        return this.saying || '';
    }
}
var myCat = Object.create(myMammal);
myCat.name = 'name is myCat';
myCat.saying = 'myCat saying';
```

### 函数化？？？

函数化构造器的模板：

spec对象包含构造器需要构造一个新实例的所有信息。my为继承中提供秘密共享的容器，选择性使用。内部函数可以访问spec,my,that以及其他私有变量。继承而又不改变/破坏原有对象。

```
var constructor = function(spec, my) {
    var that, //其他私有实例变量
        my = my || {};
    that = {
        // 一个新对象
    }
    return that;
}
```

### 部件











