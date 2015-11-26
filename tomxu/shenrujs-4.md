# 16.闭包（Closures）

```
// 自运行再自运行返回的函数
(function functionValued() {
  return function () {
    alert('returned function is called');
  };
})()();
```

```
// 以自己为返回值的函数称为自复制函数（auto-replicative function 或者 self-replicative function）
(function selfReplicative() {
  return selfReplicative;
})();
```

```
var data = [];

for (var k = 0; k < 3; k++) {
  (data[k] = function () {
    alert(arguments.callee.x);
  }).x = k; // 将k作为函数的一个属性
}

// 结果也是对的
data[0](); // 0
data[1](); // 1
data[2](); // 2
```

```
var foo = {};

// 初始化
(function (object) {

  var x = 10;

  object.getX = function _getX() {
    return x;
  };

})(foo);

alert(foo.getX()); // 获得闭包 "x" – 10
```

```
// 函数销毁，重新运行 | 重新运行函数访问
function foox() {
var x = 1;
return{
firstClosure : function () { return ++x; },
secondClosure : function () { return --x; }}
}
console.log(foox().firstClosure()); // 2
console.log(foox().secondClosure()); // 0

// 推荐
function fooy() {
var y = 1;
return{
firstClosure : function () { return ++y; },
secondClosure : function () { return --y; }}
}
var obj = fooy();
console.log(obj.firstClosure()); // 2
console.log(obj.secondClosure()); // 1

// 个人理解
var z = 1;
function fooz() {
return{
firstClosure : function () { return ++z; },
secondClosure : function () { return --z; }}
}
console.log(fooz().firstClosure()); // 2
console.log(fooz().secondClosure());  // 1
``` 



# 17.面向对象编程之一般理论

```
x = {a: 10, b: 20};
y = {a: 40, c: 50};
y.[[Prototype]] = x; // x是y的原型
 
y.a; // 40, 自身特性
y.c; // 50, 也是自身特性
y.b; // 20 – 从原型中获取: y.b (no) -> y.[[Prototype]].b (yes): 20
 
delete y.a; // 删除自身的"а"
y.a; // 10 – 从原型中获取
 
z = {a: 100, e: 50}
y.[[Prototype]] = z; // 将y的原型修改为z
y.a; // 100 – 从原型z中获取
y.e // 50, 也是从从原型z中获取
 
z.q = 200 // 添加新属性到原型上
y.q // 修改也适用于y
```

```
x = {a: 10}
 
y = {b: 20}
y.[[Prototype]] = x
 
z = {c: 30}
z.[[Prototype]] = y
 
z.a // 10
 
// z.a 在原型链里查到:
// z.a (no) ->
// z.[[Prototype]].a (no) ->
// z.[[Prototype]].[[Prototype]].a (yes): 10
```

```
function test() {
  alert([this.a, this.b]);
}
 
test.call({a: 10, b: 20}); // 10, 20
test.call({a: 100, b: 200}); // 100, 200
```

# 18.面向对象编程之ECMAScript实现

> ECMAScript是一种面向对象语言，支持基于原型的委托式继承。

```
var foo = {x: 10};
 
// 冻结对象
Object.freeze(foo);
// 判断是否被冻结
console.log(Object.isFrozen(foo)); // true
// 不能修改
foo.x = 100;
// 不能扩展
foo.y = 200;
// 不能删除
delete foo.x;
 
console.log(foo); // {x: 10}
```

```
ar foo = {x : 10};
 
Object.defineProperty(foo, "y", {
  value: 20,
  writable: false, // 是否可写、false则只读
  configurable: false // 不可配置
});
 
// 不能修改
foo.y = 200;
 
// 不能删除
delete foo.y; // false
 
// 防止扩展/禁止扩展
Object.preventExtensions(foo);
// 是否可扩展
console.log(Object.isExtensible(foo)); // false
 
// 不能添加新属性
foo.z = 30;
 
console.log(foo); {x: 10, y: 20}
```

> 对象转换

```
var a = {
  valueOf: function () {
    return 100;
  },
  toString: function () {
    return '__test';
  }
};
 
// 这个操作里，toString方法自动调用
alert(a); // "__test"
 
// 但是这里，调用的却是valueOf()方法
alert(a + 10); // 110
 
// 但，一旦valueOf删除以后
// toString又可以自动调用了
delete a.valueOf;
alert(a + 10); // "_test10"
```

> 构造函数

```
function A() {
    // 更新新创建的对象
    this.x = 10;
    // 到此还是刚新创建的对象
    console.info(this); // {x:'10'}
    // 但返回的是不同的对象,覆盖
    return [1, 2, 3];
}
 
var a = new A();
console.log(a.x, a); //undefined, [1, 2, 3]
```

```
function A() {}
A.prototype.x = 10;
console.info(A.prototype); // {x: 10}

var a = new A();
console.info(a.x); // 10 – 从原型上得到
 
// 设置.prototype属性为新对象
// 为什么显式声明.constructor属性将在下面说明
A.prototype = {
    constructor: A,
    y: 100
};
console.info(A.prototype); // {y: 100}

var b = new A();
// 对象"b"有了新属性
console.info(b.x); // undefined
console.info(b.y); // 100 – 从原型上得到
 
// 但a对象的原型依然可以得到原来的结果
console.info(a.x); // 10 - 从原型上得到
// 同一个构造函数A(),创建的两个创建对象的原型可以不同;
// 因为函数的prototype属性也可以不同
 
function B() {
    this.x = 10;
    return new Array();
}
 
// 如果"B"构造函数没有返回（或返回this）
// 那么this对象就可以使用，但是下面的情况返回的是array
var b = new B();
console.info(b.x); // undefined
console.info(Object.prototype.toString.call(b)); // [object Array]
```

> 属性构造函数(Property constructor)

```
// constructor属性在函数创建阶段被设置为函数的prototype属性
// constructor属性的值是函数自身的重要引用

function A() {}
var a = new A();
alert(a.constructor); // function A() {}, by delegation
alert(a.constructor === A); // true
```

```
function A() {}
A.prototype = {
  x: 10
};
// 等于直接修改了A.prototype指向
 
var a = new A();
alert(a.x); // 10
alert(a.constructor === A); // false!
```

```
// 手工恢复
function A() {}
A.prototype = {
  constructor: A,
  x: 10
};
 
var a = new A();
alert(a.x); // 10
alert(a.constructor === A); // true
```

```
// 在修改A.prototype原型之前的情况
a.[[Prototype]] ----> Prototype <---- A.prototype
 
// 修改之后
A.prototype ----> New prototype // 新对象会拥有这个原型
a.[[Prototype]] ----> Prototype // 引导的原来的原型上
```

```
function A() {}
A.prototype.x = 10;
 
var a = new A();
alert(a.x); // 10
 
A.prototype = {
  constructor: A,
  x: 20
  y: 30
};
 
// 对象a是通过隐式的[[Prototype]]引用从原油的prototype上获取的值
alert(a.x); // 10
alert(a.y) // undefined
 
var b = new A();
 
// 但新对象是从新原型上获取的值
alert(b.x); // 20
alert(b.y) // 30
```

有的文章说“动态修改原型将影响所有的对象都会拥有新的原型”是错误的，新原型仅仅在原型修改以后的新创建对象上生效。

这里的主要规则是：对象的原型是对象的创建的时候创建的，并且在此之后不能修改为新的对象，如果依然引用到同一个对象，可以通过构造函数的显式prototype引用，对象创建以后，只能对原型的属性进行添加或修改。

> 非标准的__proto__属性

```
function A() {}
A.prototype.x = 10;
 
var a = new A();
alert(a.x); // 10
 
var __newPrototype = {
  constructor: A,
  x: 20,
  y: 30
};
 
// 引用到新对象
A.prototype = __newPrototype;
 
var b = new A();
alert(b.x); // 20
alert(b.y); // 30
 
// "a"对象使用的依然是旧的原型
alert(a.x); // 10
alert(a.y); // undefined
 
// 显式修改原型
a.__proto__ = __newPrototype;
 
// 现在"а"对象引用的是新对象
alert(a.x); // 20
alert(a.y); // 30
```
> 对象独立于构造函数

```
function A() {}
A.prototype.x = 10;
 
var a = new A();
alert(a.x); // 10
 
// 设置A为null - 显示引用构造函数
A = null;
 
// 但如果.constructor属性没有改变的话，
// 依然可以通过它创建对象
var b = new a.constructor();
alert(b.x); // 10
 
// 隐式的引用也删除掉
delete a.constructor.prototype.constructor;
delete b.constructor.prototype.constructor;
 
// 通过A的构造函数再也不能创建对象了
// 但这2个对象依然有自己的原型
alert(a.x); // 10
alert(b.x); // 10
```

> instanceof操作符的特性

```
function A() {}
A.prototype.x = 10;
 
var a = new A();
alert(a.x); // 10
 
alert(a instanceof A); // true
 
// 如果设置原型为null
A.prototype = null;
 
// ..."a"依然可以通过a.[[Prototype]]访问原型
alert(a.x); // 10
 
// 不过，instanceof操作符不能再正常使用了
// 因为它是从构造函数的prototype属性来实现的
alert(a instanceof A); // 错误，A.prototype不是对象
```

```
function B() {}
var b = new B();
 
alert(b instanceof B); // true
 
function C() {}
 
var __proto = {
  constructor: C
};
 
C.prototype = __proto;
b.__proto__ = __proto;
 
alert(b instanceof C); // true
alert(b instanceof B); // false
```

> 属性访问

```
// 1. 首先，从原始值1，通过new Number(1)创建包装对象
// 2. 然后toString方法是从这个包装对象上继承得到的
// 从原型里继承的，即Number.prototype。

1.toString(); // 语法错误！
 
(1).toString(); // OK
 
1..toString(); // OK
 
1['toString'](); // OK
```

> Object.create()

```
Object.create ||
Object.create = function (parent, properties) {
  function F() {}
  F.prototype = parent;
  var child = new F;
  for (var k in properties) {
    child[k] = properties[k].value;
  }
  return child;
}

// 用法
var foo = {x: 10};
var bar = Object.create(foo, {y: {value: 20}});
console.log(bar.x, bar.y); // 10, 20
```


# 19.求值策略

### ECMAScript实现

按值传递，只不过该值是引用地址的拷贝，理解为简单的赋值，只不过引用的是相同的值——也就是地址副本。

```
var foo = {x: 10, y: 20};
var bar = foo;
 
alert(bar === foo); // true
 
bar.x = 100;
bar.y = 200;
// 两个标识符（名称绑定）绑定到内存中的同一个对象， 共享这个对象
// foo = bar ={x:100,y:200}

alert([foo.x, foo.y]); // [100, 200]
```

```
// 重新赋值分配，绑定是新的对象标识符（新地址），而不影响已经先前绑定的对象 
bar = {z: 1, q: 2};
 
alert([foo.x, foo.y]); // [100, 200] – 没改变
alert([bar.z, bar.q]); // [1, 2] – 但现在引用的是新对象
```


# 20.《你真懂JavaScript吗？》答案详解

```
// 变量声明被提前，但变量赋值没有
if (!("a" in window)) {
    var a = 1;
}
console.info(a); // undefined

// 等同于
var a;
if (!("a" in window)) {
    a = 1;
}
alert(a);
```

```
var a = 1,
    b = function a(x) {
        x && a(--x);
    };
console.info(a); // 1
```

```
function a(x) {
    return x * 2;
}
var a;
console.info(a); // function a(x) {return x*2};
```

```
// VO的顺序是: 函数的形参 -> 函数申明 -> 变量申明
var a = 1,
    b = function (x) {
        x && b(--x);
    };
console.info(a); // 1
```
```
function a(x) {
    return x * 2;
}
var a;
console.info(a); // function (x) {return x*2};
```

```
function b(x, y, a) {
    arguments[2] = 10;
    console.info(a);
}
b(1, 2, 3); // 10、严格模式3
```

```
function a() {
    console.info(this);
}
a.call(null); // null
```

* 找出数字数组中最大的元素（使用Match.max函数）

```
Math.max.apply(Math, arr);
Math.max.apply(null, arr);
Math.max.apply(this, arr);
```

* 转化一个数字数组为function数组（每个function都弹出相应的数字）

```
arr.map(function(xld){
 return function(){return xld}
})
```

* 利用JavaScript打印出Fibonacci数（不使用全局变量）

```
function f(xld){ return xld < 2 ? 1 : f(xld - 1) + f(xld-2)}
```

* 实现如下语法的功能：var a = (5).plus(3).minus(6); //2

```
Number.prototype.plus = function(xld){return this + xld};
Number.prototype.minus = function(xld){return this - xld};
```

* 实现如下语法的功能：var a = add(2)(3)(4); //9

```
// 弱化版
function add(v) {
	return function(a) {
		return function(b) {
			return v+a+b;
		}
	}
}

// 高级版
function add(x){
     add.valueOf = add.toString = function(){return x}
     function add(y){ x+=y; return add}
     return add;
}
```
> [不同解法及说明](http://www.cnblogs.com/zichi/p/4362292.html)