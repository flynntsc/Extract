# 11.执行上下文（Execution Contexts）

```
function MyFunc() {}; //定义一个空函数
var anObj = new MyFunc(); //使用new操作符，借助MyFun函数，就创建了一个对象

// 其实，可以把上面的代码改写成这种等价形式：
function MyFunc(){};
var anObj = {}; //创建一个对象
MyFunc.call(anObj); //将anObj对象作为this指针调用MyFunc函数
```

用 var anObject = new aFunction() 形式创建对象的过程实际上可以分为三步：第一步是建立一个新对象；第二步将该对象内置的原型对象设置为构造函数prototype引用的那个原型对象；第 三步就是将该对象作为this参数调用构造函数，完成成员设置等初始化工作。对象建立之后，对象上的任何访问和操作都只与对象自身及其原型链上的那串对象 有关，与构造函数再扯不上关系了。换句话说，构造函数只是在创建对象时起到介绍原型对象和初始化对象两个作用。


# 12.变量对象（Variable Object）

```
var a=10,
    date1=new Date();
for(var i=0;i<=1000000;i++){
 window.a;
}
console.info(new Date()-date1);//200

var date2=new Date();
for(var j=0;j<=1000000;j++){
  a;
}
console.info(new Date()-date2);//5
```
a和 window.a虽然是同一个值，但是在查找的时候，用a的话，是直接在当前作用域内就可以查到的（其实也是全局作用域，因为你就只有1级），但是用window.a就是说忽略当前作用域，直接去全局作用域查，相对来说各个浏览器对最后一关的全局作用域的查询性能来说

# 13.This? Yes, this!

### 函数代码中的this

* this就是由调用者决定的
* 在函数内，非严格模式，this ＝ window;严格模式下，this为undefined

```
// 谁调用就指向谁
var foo = {x: 10};
 
var bar = {
    x: 20,
    test: function () {
 
    alert(this === bar); // true
    alert(this.x); // 20
  }
};
 
// 在进入上下文的时候
// this被当成bar对象
// determined as "bar" object; why so - will
// be discussed below in detail
 
bar.test(); // true, 20
 
foo.test = bar.test;
// 不过，这里this依然不会是foo（因为还未调用运行）
// 尽管调用的是相同的function
// 以下开始运行了，this才变更
foo.test(); // false, 10
bar.test.bind(foo)();
bar.test.call(foo);
bar.test.apply(foo);
```

* 在一个函数上下文中，this由调用者提供，由调用函数的方式来决定。(上面代码)
* 如果调用括号()的左边是**引用类型**的值，this将设为引用类型值的base对象（base object）（下面代码）

```
var foo = {
  bar: function () {
    return this;
  }
};
 
foo.bar(); // foo
var test = foo.bar;
test(); // 又变为全局了！！！！！！
```
动态确定this值的经典例子

```
function foo() {
  console.info(this.bar);
}
 
var x = {bar: 10};
var y = {bar: 20};
 
x.test = foo;
y.test = foo;
 
x.test(); // 10
y.test(); // 20
```

#### 函数调用和非引用类型

```
(function () {
    alert(this); // null => global
})();
```

#### 作为构造器调用的函数中的this

```
function A() {
  alert(this); // "a"对象下创建一个新属性
  this.x = 10;
}
 
var a = new A();
alert(a.x); // 10
```
#### 函数调用中手动设置this

对于.apply，第二个参数必须是数组（或者是类似数组的对象，如arguments，反过来，.call能接受任何参数。两个方法必须的参数是第一个——this

```
var b = 10;
 
function a(c) {
  alert(this.b);
  alert(c);
}
 
a(20); // this === global, this.b == 10, c == 20
 
a.call({b: 20}, 30); // this === {b: 20}, this.b == 20, c == 30
a.apply({b: 30}, [40]) // this === {b: 30}, this.b == 30, c == 40
```


# 14.作用域链(Scope Chain)

```
var x = 10;
 
function foo() {
  alert(x);
}
 
(function () {
  var x = 20;
  foo(); // 10, but not 20
})();
```


# 15.函数（Functions）

### 函数类型

在ECMAScript 中有三种函数类型：函数声明，函数表达式和函数构造器创建的函数

> **函数声明FD**

```
console.info(foo); // 函数
 
function foo(x) {
  console.info(x);
}(1); // 这只是一个分组操作符，不是函数调用！
 
foo(10); // 这才是一个真正的函数调用，结果是10
```

> **函数表达式FE**

```
var foo = function () {
  ...
};
```


```
// 注意是1,后面的声明
1, function () {
  alert('anonymous function is called');
}();
 
// 或者这个
!function () {
  alert('ECMAScript');
}();

// 等同于

(function () {
  alert('ECMAScript');
}());
```

> **构造函数-用new**