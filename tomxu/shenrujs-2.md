# 6. S.O.L.I.D五大原则之单一职责SRP

### 实例代码

```
function Product(id, description) {
    this.getId = function () {
        return id;
    };
    this.getDescription = function () {
        return description;
    };
}

function Cart(eventAggregator) {
    var items = [];

    this.addItem = function (item) {
        items.push(item);
    };
}

(function () {
    var products = [new Product(1, "Star Wars Lego Ship"),
            new Product(2, "Barbie Doll"),
            new Product(3, "Remote Control Airplane")],
cart = new Cart();

    function addToCart() {
        var productId = $(this).attr('id');
        var product = $.grep(products, function (x) {
            return x.getId() == productId;
        })[0];
        cart.addItem(product);

        var newItem = $('<li></li>').html(product.getDescription()).attr('id-cart', product.getId()).appendTo("#cart");
    }

    products.forEach(function (product) {
        var newItem = $('<li></li>').html(product.getDescription())
                                    .attr('id', product.getId())
                                    .dblclick(addToCart)
                                    .appendTo("#products");
    });
})();
```
该代码声明了2个function分别用来描述product和cart，而匿名函数的职责是更新屏幕和用户交互，这还不是一个很复杂的例子，但匿名函数里却包含了很多不相关的职责
1. 有product的集合的声明
2. 有一个将product集合绑定到#product元素的代码，而且还附件了一个添加到购物车的事件处理
3. 有Cart购物车的展示功能
4. 有添加product item到购物车并显示的功能

### 重构代码

实现事件聚合的功能，该功能分为2部分

```
// 用于Handler回调的代码
function Event(name) {
    var handlers = [];

    this.getName = function () {
        return name;
    };

    this.addHandler = function (handler) {
        handlers.push(handler);
    };

    this.removeHandler = function (handler) {
        for (var i = 0; i < handlers.length; i++) {
            if (handlers[i] == handler) {
                handlers.splice(i, 1);
                break;
            }
        }
    };

    this.fire = function (eventArgs) {
        handlers.forEach(function (h) {
            h(eventArgs);
        });
    };
}

// 用来订阅和发布Event
function EventAggregator() {
    var events = [];

    function getEvent(eventName) {
        return $.grep(events, function (event) {
            return event.getName() === eventName;
        })[0];
    }

    this.publish = function (eventName, eventArgs) {
        var event = getEvent(eventName);

        if (!event) {
            event = new Event(eventName);
            events.push(event);
        }
        event.fire(eventArgs);
    };

    this.subscribe = function (eventName, handler) {
        var event = getEvent(eventName);

        if (!event) {
            event = new Event(eventName);
            events.push(event);
        }

        event.addHandler(handler);
    };
}
```

```
// Product对象
function Product(id, description) {
    this.getId = function () {
        return id;
    };
    this.getDescription = function () {
        return description;
    };
}
```

```
// Cart对象
function Cart(eventAggregator) {
    var items = [];

    this.addItem = function (item) {
        items.push(item);
        
        // 触发发布一个事件itemAdded，然后将item作为参数传进去
        eventAggregator.publish("itemAdded", item);
    };
}
```

```
// CartController主要是接受cart对象和事件聚合器，
// 通过订阅itemAdded来增加一个li元素节点，
// 通过订阅productSelected事件来添加product。
function CartController(cart, eventAggregator) {
    eventAggregator.subscribe("itemAdded", function (eventArgs) {
        var newItem = $('<li></li>').html(eventArgs.getDescription()).attr('id-cart', eventArgs.getId()).appendTo("#cart");
    });

    eventAggregator.subscribe("productSelected", function (eventArgs) {
        cart.addItem(eventArgs.product);
    });
}
```

```
// 获取数据（可以从ajax里获取），然后暴露get数据的方法
function ProductRepository() {
    var products = [new Product(1, "Star Wars Lego Ship"),
            new Product(2, "Barbie Doll"),
            new Product(3, "Remote Control Airplane")];

    this.getProducts = function () {
        return products;
    }
}
```

```
function ProductController(eventAggregator, productRepository) {
    var products = productRepository.getProducts();

    // 主要是发布触发productSelected事件
    function onProductSelected() {
        var productId = $(this).attr('id');
        var product = $.grep(products, function (x) {
            return x.getId() == productId;
        })[0];
        eventAggregator.publish("productSelected", {
            product: product
        });
    }

    // 主要是用于绑定数据到产品列表上
    products.forEach(function (product) {
        var newItem = $('<li></li>').html(product.getDescription())
                                    .attr('id', product.getId())
                                    .dblclick(onProductSelected)
                                    .appendTo("#products");
    });
}
```

```
(function () {
    var eventAggregator = new EventAggregator(),
        cart = new Cart(eventAggregator),
        cartController = new CartController(cart, eventAggregator),
        productRepository = new ProductRepository(),
        productController = new ProductController(eventAggregator, productRepository);
})();
```

重构的结果就是写了一大堆的对象声明，但是好处是每个对象有了自己明确的职责，该展示数据的展示数据，改处理集合的处理集合，这样耦合度就非常低了。

### 总结

如果你的项目是个是个非常小的项目，代码也不是很多，那其实是没有必要重构得这么复杂，但如果你的项目是个很复杂的大型项目，或者你的小项目将来可能增长得很快的话，那就在前期就得考虑SRP原则进行职责分离了，这样才有利于以后的维护。





# 7.S.O.L.I.D五大原则之开闭原则OCP

### 前言

open for extension（对扩展开放）的意思是说当新需求出现的时候，可以通过扩展现有模型达到目的。而Close for modification（对修改关闭）的意思是说不允许对该实体做任何修改，说白了，就是这些需要执行多样行为的实体应该设计成不需要修改就可以实现各种的变化，坚持开闭原则有利于用最少的代码进行项目维护。

### 问题代码

```
// 问题类型
var AnswerType = {
    Choice: 0,
    Input: 1
};

// 问题实体
function question(label, answerType, choices) {
    return {
        label: label,
        answerType: answerType,
        choices: choices // 这里的choices是可选参数
    };
}

var view = (function () {
    // render一个问题
    function renderQuestion(target, question) {
        var questionWrapper = document.createElement('div');
        questionWrapper.className = 'question';

        var questionLabel = document.createElement('div');
        questionLabel.className = 'question-label';
        var label = document.createTextNode(question.label);
        questionLabel.appendChild(label);

        var answer = document.createElement('div');
        answer.className = 'question-input';

        // 根据不同的类型展示不同的代码：分别是下拉菜单和输入框两种（无法扩展）
        if (question.answerType === AnswerType.Choice) {
            var input = document.createElement('select');
            var len = question.choices.length;
            for (var i = 0; i < len; i++) {
                var option = document.createElement('option');
                option.text = question.choices[i];
                option.value = question.choices[i];
                input.appendChild(option);
            }
        }
        else if (question.answerType === AnswerType.Input) {
            var input = document.createElement('input');
            input.type = 'text';
        }

        answer.appendChild(input);
        questionWrapper.appendChild(questionLabel);
        questionWrapper.appendChild(answer);
        target.appendChild(questionWrapper);
    }

    return {
        // 遍历所有的问题列表进行展示
        render: function (target, questions) {
            for (var i = 0; i < questions.length; i++) {
                renderQuestion(target, questions[i]);
            };
        }
    };
})();

// 依赖AnswerType，累赘；
var questions = [
                question('Have you used tobacco products within the last 30 days?', AnswerType.Choice, ['Yes', 'No']),
                question('What medications are you currently using?', AnswerType.Input)
                ];

var questionRegion = document.getElementById('questions');
view.render(questionRegion, questions);
```
### 重构代码

```
重构后的最终代码

function questionCreator(spec, my) {
    var that = {};

    my = my || {};
    my.label = spec.label;

    my.renderInput = function() {
        throw "not implemented";
    };

    that.render = function(target) {
        var questionWrapper = document.createElement('div');
        questionWrapper.className = 'question';

        var questionLabel = document.createElement('div');
        questionLabel.className = 'question-label';
        var label = document.createTextNode(spec.label);
        questionLabel.appendChild(label);

        var answer = my.renderInput();

        questionWrapper.appendChild(questionLabel);
        questionWrapper.appendChild(answer);
        return questionWrapper;
    };

    return that;
}

function choiceQuestionCreator(spec) {

    var my = {},
        that = questionCreator(spec, my);

    my.renderInput = function() {
        var input = document.createElement('select');
        var len = spec.choices.length;
        for (var i = 0; i < len; i++) {
            var option = document.createElement('option');
            option.text = spec.choices[i];
            option.value = spec.choices[i];
            input.appendChild(option);
        }

        return input;
    };

    return that;
}

function inputQuestionCreator(spec) {

    var my = {},
        that = questionCreator(spec, my);

    my.renderInput = function() {
        var input = document.createElement('input');
        input.type = 'text';
        return input;
    };

    return that;
}

var view = {
    render: function(target, questions) {
        for (var i = 0; i < questions.length; i++) {
            target.appendChild(questions[i].render());
        }
    }
};

var questions = [
    choiceQuestionCreator({
    label: 'Have you used tobacco products within the last 30 days?',
    choices: ['Yes', 'No']
}),
    inputQuestionCreator({
    label: 'What medications are you currently using?'
})
    ];

var questionRegion = document.getElementById('questions');

view.render(questionRegion, questions);
```

1. 首先，questionCreator方法的创建，可以让我们使用模板方法模式将处理问题的功能delegat给针对每个问题类型的扩展代码renderInput上。
2. 其次，我们用一个私有的spec属性替换掉了前面question方法的构造函数属性，因为我们封装了render行为进行操作，不再需要把这些属性暴露给外部代码了。
3. 第三，我们为每个问题类型创建一个对象进行各自的代码实现，但每个实现里都必须包含renderInput方法以便覆盖questionCreator方法里的renderInput代码，这就是我们常说的策略模式。




# 8.S.O.L.I.D五大原则之里氏替换原则LSP

1. 派生类型必须可以替换它的基类型。
2. 尽量使用对象组合而不是类继承


# 9.根本没有“JSON对象”这回事！

```
// 这是JSON字符串，比如从AJAX获取字符串信息
var my_json_string = '{ "prop": "val" }';
 
// 将字符串反序列化成对象
var my_obj = JSON.parse( my_json_string );
 
alert( my_obj.prop == 'val' ); //  提示 true, 和想象的一样!
 
// 将对象序列化成JSON字符串
var my_other_json_string = JSON.stringify( my_obj );
```


# 10.JavaScript核心（晋级高手必读篇）

### 原型链

```
var a = {
  x: 10,
  calculate: function (z) {
    return this.x + this.y + z
  }
};
 
var b = {
  y: 20,
  __proto__: a
};
 
var c = {
  y: 30,
  __proto__: a
};
 
// 调用继承过来的方法
b.calculate(30); // 60
c.calculate(40); // 80
```

### 构造函数

```
// 构造函数
function Foo(y) {
  // 构造函数将会以特定模式创建对象：被创建的对象都会有"y"属性
  this.y = y;
}
 
// "Foo.prototype"存放了新建对象的原型引用
// 所以我们可以将之用于定义继承和共享属性或方法
// 所以，和上例一样，我们有了如下代码：
 
// 继承属性"x"
Foo.prototype.x = 10;
 
// 继承方法"calculate"
Foo.prototype.calculate = function (z) {
  return this.x + this.y + z;
};
 
// 使用foo模式创建 "b" and "c"
var b = new Foo(20);
var c = new Foo(30);
 
// 调用继承的方法
b.calculate(30); // 60
c.calculate(40); // 80
 
// 让我们看看是否使用了预期的属性
 
console.log(
 
  b.__proto__ === Foo.prototype, // true
  c.__proto__ === Foo.prototype, // true
 
  // "Foo.prototype"自动创建了一个特殊的属性"constructor"
  // 指向a的构造函数本身
  // 实例"b"和"c"可以通过授权找到它并用以检测自己的构造函数
 
  b.constructor === Foo, // true
  c.constructor === Foo, // true
  Foo.prototype.constructor === Foo // true
 
  b.calculate === b.__proto__.calculate, // true
  b.__proto__.calculate === Foo.prototype.calculate // true
 
);
```

![构造函数与对象之间的关系](http://pic002.cnblogs.com/images/2011/349491/2011123111482169.png '构造函数与对象之间的关系')

上述图示可以看出，每一个object都有一个prototype. 构造函数Foo也拥有自己的__proto__, 也就是Function.prototype, 而Function.prototype的__proto__指向了Object.prototype. 重申一遍，Foo.prototype只是一个显式的属性，也就是b和c的__proto__属性。

### this

```
// "foo"函数里的alert没有改变
// 但每次激活调用的时候this是不同的
 
function foo() {
  alert(this);
}
 
// caller 激活 "foo"这个callee，
// 并且提供"this"给这个 callee
 
foo(); // 全局对象
foo.prototype.constructor(); // foo.prototype
 
var bar = {
  baz: foo
};
 
bar.baz(); // bar
 
(bar.baz)(); // also bar
(bar.baz = bar.baz)(); // 这是一个全局对象
(bar.baz, bar.baz)(); // 也是全局对象
(false || bar.baz)(); // 也是全局对象
 
var otherFoo = bar.baz;
otherFoo(); // 还是全局对象
```