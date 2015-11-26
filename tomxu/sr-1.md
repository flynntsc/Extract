# 1. 编写高质量JavaScript代码的基本要点
### (1)书写可维护代码
### (2)尽量减少甚至避免全局变量 ###
### (3)置顶并单写var
```
function func() {
    var a = 1,
        b = 2,
        sum = a + b, //单个var内a,b可直接拿来计算
        myobject = {},
        i,
        j;
    // function body...
}
```
### (4)for循环的优化
```
// 原版
function looper() {
    var i = 0,
        max,
        myarray = [];
    // ...
    for (i = 0, max = myarray.length; i < max; i++) {
    // 使用myarray[i]做点什么
   }
}

// 进化版1；
var i, myarray = [];
for (i = myarray.length; i--;) { //省去一个变量；与0比较更快；减法更快;不用i>0；
    // 使用myarray[i]做点什么
}

// 进化版2
var myarray = [],
    i = myarray.length;
while (i–-) { //感觉更好理解；
   // 使用myarray[i]做点什么
}
```

### (5)for in的使用；
最好数组使用正常的for循环，对象使用for-in循环；

### (6)避免eval
```
// 反面示例
var property = "name";
alert(eval("obj." + property));

// 更好的
var property = "name";
alert(obj[property]); //对象的基础要透彻
```
```
// 反面示例
setTimeout("myFunc()", 1000);
setTimeout("myFunc(1, 2, 3)", 1000);

// 更好的
setTimeout(myFunc, 1000);
setTimeout(function () {
    myFunc(1, 2, 3);
}, 1000);
```
```
console.log(typeof un);    // "undefined"
console.log(typeof deux); // "undefined"
console.log(typeof trois); // "undefined"

var jsstring = "var un = 1; console.log(un);";
eval(jsstring); // logs "1"

jsstring = "var deux = 2; console.log(deux);";
new Function(jsstring)(); // logs "2"

jsstring = "var trois = 3; console.log(trois);";
(function () {
    eval(jsstring);
}()); // logs "3"

console.log(typeof un); // number
console.log(typeof deux); // "undefined" 函数内运行，不影响全局变量
console.log(typeof trois); // "undefined" 函数内运行，不影响全局变量
```
Function无论哪里运行，只认全局变量；Function等同于new Function

```
(function () {
   var local = 1;
   eval("local = 3; console.log(local)"); // logs "3"
   console.log(local); // logs "3"
}());

(function () {
   var local = 1;
   Function("console.log(typeof local);")(); // logs undefined
}());
```
### (7)parseInt记得加上另一个参数，保证稳定性
```
parseInt('08',10); // 记得10是十进制莫忘加；相比另两个速度最慢的；但可过滤'08hello'出数字
parseInt('08hello'，10); // 能得到8，另两个得不到满意数值;
+"08" // 结果是 8
Number("08") // 8
```


# 2. 揭秘函数表达式

```
// 此前，你可能会使用arguments.callee
var f = (function(x) {
    if (x <= 1) return 1;
    return x * arguments.callee(x - 1);
  })(10);
  
// 但在严格模式下，有可能就要使用命名函数表达式
var f = (function factorial(x) {
    if (x <= 1) return 1;
    return x * factorial(x - 1);
  })(10); //理解这种代码写法，比下面的优雅灵活很多
（方便理解：var f= function(x);而function = (function factorial(x){...});）
  
// 要么就退一步，使用没有那么灵活的函数声明
function factorial(x) {
    if (x <= 1) return 1;
    return x * factorial(x - 1);
}
factorial(10);
  ```


# 3. 全面解析Module模式
### 简介
1. 模块化，可重用
2. 封装了变量和function，和全局的namaspace不接触，松耦合
3. 只暴露可用public的方法，其它私有方法全部隐藏

```
var Calculator = function (eq) {
    //这里可以声明私有成员

    var eqCtl = document.getElementById(eq);

    return {
        // 暴露公开的成员
        add: function (x, y) {
            var val = x + y;
            eqCtl.innerHTML = val;
        }
    };
};
```

```
var calculator = new Calculator('eq');
calculator.add(2, 2);
```

每次用的时候都要new一下，也就是说每个实例在内存里都是一份copy，如果你不需要传参数或者没有一些特殊苛刻的要求的话，我们可以在最后一个}后面加上一个括号，来达到自执行的目的，这样该实例在内存中只会存在一份copy

```
var blogModule = (function () {
    var my = {}, privateName = "博客园";

    function privateAddTopic(data) {
        // 这里是内部处理代码
    }

    my.Name = privateName;
    my.AddTopic = function (data) {
        privateAddTopic(data);
    };

    return my;
} ());
```

> 扩展

```
var blogModule = (function (my) {
    my.AddPhoto = function () {
        //添加内部代码  
    };
    return my;
} (blogModule));

//此处my = blogModule = 'undefined'
```

> 松耦合扩展

```
var blogModule = (function (my) {

    // 添加一些功能   
    
    return my;
} (blogModule || {})); 
// 对blogModule继承并可不断增加扩展
```

> 紧耦合扩展

```
//松耦合扩展没办法重写你的一些属性或者函数，也不能在初始化的时候就是用Module的属性。
//紧耦合扩展限制了加载顺序，但是提供了我们重载的机会。

var blogModule = (function (my) {
    var oldAddPhotoMethod = my.AddPhoto;

    my.AddPhoto = function () {
        // 重载方法，依然可通过oldAddPhotoMethod调用旧的方法
    };

    return my;
} (blogModule));

// 达到了重载的目的，当然如果你想在继续在内部使用原有的属性，你可以调用oldAddPhotoMethod来用
```

> 克隆与继承

```
var blogModule = (function (old) {
    var my = {},
        key;

    // 筛选内置属性，排除原型链
    for (key in old) {
        if (old.hasOwnProperty(key)) {
            my[key] = old[key];
        }
    }

    var oldAddPhotoMethod = old.AddPhoto;
    my.AddPhoto = function () {
        // 克隆以后，进行了重写，当然也可以继续调用oldAddPhotoMethod
    };

    return my;
} (blogModule));
```

> 跨文件共享私有对象

```
var blogModule = (function (my) {
    var _private = my._private = my._private || {},
        
        _seal = my._seal = my._seal || function () {
            delete my._private;
            delete my._seal;
            delete my._unseal;
            
        },
        
        _unseal = my._unseal = my._unseal || function () {
            my._private = _private;
            my._seal = _seal;
            my._unseal = _unseal;
        };
        
    return my;
} (blogModule || {}));

// 任何文件都可以对他们的局部变量_private设属性，并且设置对其他的文件也立即生效。
// 一旦这个模块加载结束，应用会调用 blogModule._seal()"上锁"，这会阻止外部接入内部的_private。
// 如果这个模块需要再次增生，应用的生命周期内，任何文件都可以调用_unseal() ”开锁”，
// 然后再加载新文件。加载后再次调用 _seal()”上锁”。
```

> 子模块

```
blogModule.CommentSubModule = (function () {
    var my = {};
    // ...

    return my;
} ());

// 子模块也具有一般模块所有的高级使用方式，也就是说你可以对任意子模块再次使用上面的一些应用方法。
```

### 总结

上面的大部分方式都可以互相组合使用的，一般来说如果要设计系统，可能会用到松耦合扩展，私有状态和子模块这样的方式。另外，我这里没有提到性能问题，但我认为Module模式效率高，代码少，加载速度快。使用松耦合扩展允许并行加载，这更可以提升下载速度。不过初始化时间可能要慢一些，但是为了使用好的模式，这是值得的。


# 4. 立即调用的函数表达式

自执行 ＝ 立即调用的函数表达式

```
// 下面这个function在语法上是没问题的，但是依然只是一个语句
// 加上括号()以后依然会报错，因为分组操作符需要包含表达式
 
function foo(){ /* code */ }(); // SyntaxError: Unexpected token )
 
// 但是如果你在括弧()里传入一个表达式，将不会有异常抛出
// 但是foo函数依然不会执行
function foo(){ /* code */ }( 1 );
 
// 因为它完全等价于下面这个代码，一个function声明后面，又声明了一个毫无关系的表达式： 
function foo(){ /* code */ }
 
( 1 );
```

```
// 下面2个括弧()都会立即执行

(function () { /* code */ } ()); // 推荐使用这个
(function () { /* code */ })(); // 但是这个也是可以用的

// 由于括弧()和JS的&&，异或，逗号等操作符是在函数表达式和函数声明上消除歧义的
// 所以一旦解析器知道其中一个已经是表达式了，其它的也都默认为表达式了
// 不过，请注意下一章节的内容解释

var i = function () { return 10; } ();
true && function () { /* code */ } ();
0, function () { /* code */ } ();

// 如果你不在意返回值，或者不怕难以阅读
// 你甚至可以在function前面加一元操作符号

!function () { /* code */ } ();
~function () { /* code */ } ();
-function () { /* code */ } ();
+function () { /* code */ } ();

// 还有一个情况，使用new关键字,也可以用，但我不确定它的效率
// http://twitter.com/kuvos/status/18209252090847232

new function () { /* code */ }
new function () { /* code */ } () // 如果需要传递参数，只需要加上括弧()
```

```
// 这个代码是错误的，因为变量i从来就没背locked住
// 相反，当循环执行以后，我们在点击的时候i才获得数值
// 因为这个时候i操真正获得值
// 所以说无论点击那个连接，最终显示的都是I am link #10（如果有10个a元素的话）

var elems = document.getElementsByTagName('a');

for (var i = 0; i < elems.length; i++) {

    elems[i].addEventListener('click', function (e) {
        e.preventDefault();
        alert('I am link #' + i);
    }, 'false');

}

// 这个是可以用的，因为他在自执行函数表达式闭包内部
// i的值作为locked的索引存在，在循环执行结束以后，尽管最后i的值变成了a元素总数（例如10）
// 但闭包内部的lockedInIndex值是没有改变，因为他已经执行完毕了
// 所以当点击连接的时候，结果是正确的

var elems = document.getElementsByTagName('a');

for (var i = 0; i < elems.length; i++) {

    (function (lockedInIndex) {

        elems[i].addEventListener('click', function (e) {
            e.preventDefault();
            alert('I am link #' + lockedInIndex);
        }, 'false');

    })(i);

}

// 你也可以像下面这样应用，在处理函数那里使用自执行函数表达式
// 而不是在addEventListener外部
// 但是相对来说，上面的代码更具可读性

var elems = document.getElementsByTagName('a');

for (var i = 0; i < elems.length; i++) {

    elems[i].addEventListener('click', (function (lockedInIndex) {
        return function (e) {
            e.preventDefault();
            alert('I am link #' + lockedInIndex);
        };
    })(i), 'false');

}
```

> Moudle模式


```
// 创建一个立即调用的匿名函数表达式
// return一个变量，其中这个变量里包含你要暴露的东西
// 返回的这个变量将赋值给counter，而不是外面声明的function自身

var counter = (function () {
    var i = 0;

    return {
        get: function () {
            return i;
        },
        set: function (val) {
            i = val;
        },
        increment: function () {
            return ++i;
        }
    };
} ());

// counter是一个带有多个属性的对象，上面的代码对于属性的体现其实是方法

counter.get(); // 0
counter.set(3);
counter.increment(); // 4
counter.increment(); // 5

counter.i; // undefined 因为i不是返回对象的属性
i; // 引用错误: i 没有定义（因为i只存在于闭包）
```


# 5. 强大的原型和原型链

### 原型

> 最初

```
var decimalDigits = 2,
    tax = 5;

function add(x, y) {
    return x + y;
}

function subtract(x, y) {
    return x - y;
}

//alert(add(1, 3));
```

> 原型使用1

```
var Calculator = function (decimalDigits, tax) {
    this.decimalDigits = decimalDigits;
    this.tax = tax;
};

Calculator.prototype = {
    add: function (x, y) {
        return x + y;
    },

    subtract: function (x, y) {
        return x - y;
    }
};
//alert((new Calculator()).add(1, 3));
```

> 原型使用2

```
// 好处为封装私有的function，通过return的形式暴露出简单的使用名称，
// 以达到public/private的效果

Calculator.prototype = function () {
    var add = function (x, y) {
        return x + y;
    },

    subtract = function (x, y) {
        return x - y;
    };
    
 // 直接返回集合 
    return {
        add: add,
        subtract: subtract
    }
} ();
// 必须自运行函数

//alert((new Calculator()).add(11, 3));
```

> 分步声明

```
var BaseCalculator = function() {
    this.decimalDigits = 2;
};

BaseCalculator.prototype = {
    add: function(x, y) {
        return x + y;
    },
    subtract: function(x, y) {
        return x - y;
    }
};

var Calculator = function () {
    //为每个实例都声明一个税收数字
    this.tax = 5;
};

```

```
// 正常
Calculator.prototype = new BaseCalculator();
var calc = new Calculator();
alert(calc.add(1, 1)); // 2
//BaseCalculator 里声明的decimalDigits属性，在 Calculator里是可以访问到的
alert(calc.decimalDigits); // 2
```

```
// 禁止被引用
Calculator.prototype = BaseCalculator.prototype;

var calc = new Calculator();
alert(calc.add(1, 1)); // undefined
alert(calc.decimalDigits);// undefined
```

### 原型链

```
function Foo() {
    this.value = 42;
}
Foo.prototype = {
    method: function() {}
};

function Bar() {}

// 设置Bar的prototype属性为Foo的实例对象
Bar.prototype = new Foo();
Bar.prototype.foo = 'Hello World';

// 修正Bar.prototype.constructor为Bar本身
Bar.prototype.constructor = Bar;

var test = new Bar() // 创建Bar的一个新实例

// 原型链
test [Bar的实例]
    Bar.prototype [Foo的实例] 
        { foo: 'Hello World' }
        Foo.prototype
            {method: ...};
            Object.prototype
                {toString: ... /* etc. */};
```

### hasOwnProperty

是 JavaScript 中唯一一个处理属性但是不查找原型链的函数

```
var foo = {
    hasOwnProperty: function() {
        return false;
    },
    bar: 'Here be dragons'
};

foo.hasOwnProperty('bar'); // 总是返回 false

// 使用{}对象的 hasOwnProperty，并将其上下为设置为foo
{}.hasOwnProperty.call(foo, 'bar'); // true
```
推荐使用 hasOwnProperty，不要对代码运行的环境做任何假设，不要假设原生对象是否已经被扩展了。

### 评论内额外补充
> [原文地址：](http://www.mollypages.org/misc/js.mp)

* 所有的对象都有"[[prototype]]"属性（通过__proto__访问），该属性对应对象的原型
* 所有的函数对象都有"prototype"属性，该属性的值会被赋值给该函数创建的对象的"__proto__"属性
* 所有的原型对象都有"constructor"属性，该属性对应创建所有指向该原型的实例的构造函数
* 函数对象和原型对象通过"prototype"和"constructor"属性进行相互关联


![原型](http://www.mollypages.org/misc/jsobj.jpg)

```
function foo() { } ; var f1 = new foo();
f1.constructor === foo.prototype.constructor === foo  
//replace the default prototype object
foo.prototype = new Object();
//create another instance l
f1 = new foo();
//now we have:
f1.constructor === foo.prototype.constructor === Object
//so now we say:
foo.prototype.constructor = foo
//all is well again
f1.constructor === foo.prototype.constructor === foo
```

```
function foo() { } 
f1 = new foo();
f2 = new foo();
foo.prototype.x = "hello";

f1.x  => "hello"
f2.x  => "hello";

f1.x = "goodbye";   //setting f1.x hides foo.prototype.x

f1.x  => "goodbye"  //hides "hello" for f1 only
f2.x  => "hello"
  
delete f1.x
f1.x  => "hello";   //foo.prototype.x is visible again to f1.
```
