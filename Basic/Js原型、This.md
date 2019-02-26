##### 一、原型解析图片
##### 1、什么是原型
这个东西有点难懂，是真的难。
在JavaScript中，每个函数都有一个prototype属性，当一个函数被用作构造函数来创建实例时，该函数的prototype属性值将被作为原型赋值给所有对象实例（也就是设置实例的__proto__属性），也就是说，所有实例的原型引用的是函数的prototype属性。

- 首先原型是个特殊的对象，所有的"原型对象"都有一个constructor指向创建该原型实例的"构造函数对象"。
- 每个“函数对象”都有一个prototype属性。
- 而每一个"对象"都有一个__proto__指向创建该对象的"构造函数对象"的原型（prototype属性值）。
- "函数对象"和"原型对象"通过```prototype```和```constructor```相互关联。

通过上面的总结，可以把涉及到的东西，分为“普通实例对象”，“函数对象”，“原型对象”。
```
var person = function(){
    this.tmp = 1;
}
var demo = new person();
demo-> "普通实例对象"。
person-> "函数对象"。
根据上面的说法。
demo有一个属性叫__proto__（实际上他本来是[[prototype]]，但是并不允许被访问，所以有__proto__这个隐式原型来访问他）。
person有一个prototype属性。

在控制台中输出demo.__proto__以及person.prototype，都会有一个对象，这个对象就是"原型对象"，里面包含了一个constructor指向person。
还有一个__proto__指向创建这个原型对象的构造函数的原型（别忘了"原型对象本身也是一个对象"，原型中难理解的也就是这个点）。

demo.__proto__ -> person.prototype 
person.prototype.constructor -> person（函数对象）
(person函数对象和person原型，通过prototype和constructor互相关联)。
person 函数对象本身也是一个对象，所以他也有__proto__指向的是Function的原型。
```
综上所述：原型实际上就是一个对象，它存储了当前原型所属的构造"函数"，并且包含了创建该对象的构造函数的原型。
比如上面的person原型，就是包含了一个constructor指向function person，另外还包含了一个__proto__（隐式原型）指向的就是Object原型（因为当前原型对象是由function Object创建的）。

下图很好的说明了上面的总结。而其中Funtion和Object之间的原型关联看起来很复杂，下文再作总结。
![image](https://github.com/demoyf/img/raw/master/blog_img/%E5%8E%9F%E5%9E%8B.png)

##### 二、Function和Object之间的关系。
    Function和Object之间的关系，就像是鸡生蛋，蛋生鸡的关系。
首先function Object是由Function创建的（函数都是来自于Function），所以说function Object的__proto__是Function.prototype，而Function.prototype又是一个对象，所以他的__proto__指向的自然就是Object.prototype。那么讲道理Object.prototype也是一个对象，那么他的__proto__是不是应该指向自身呢，答案是不可以的，如果指向自身，那么就会造成一个闭环，陷入一个死循环。所以JavaScript中，Object.prototype的__proto__指向的是null。


##### 三、原型链
上文说得很清楚，每一个对象都有一个__proto__,指向创建它的构造函数的原型。
举个例子：
```
function Foo(){
    
}
var f = new Foo();
有了以下的一种链式的效果。
foo.__proto__ -> Foo.prototype : Foo.prototype.__proto__ -> Object.prototype.

这样一条链，称为原型链。那么它的作用是什么呢。
比如当我们在foo中调用某一属性或者方法的时候，例如toString，但是foo自身并没有toString，所以他就会想要去创造它的构造函数的原型Foo.prototype中去找（这个应该也是原型存在的意义），
但是Foo.prototype也没有toString，所以就通过Foo.prototype.__proto__去找创造这个原型的构造函数的原型Object.prototype，然后就在Object.prototype中找到了，之后使用它。
这样一个步骤必须要有可以支持查找的方法，所以就构造了原型链。
```


##### 四、创造一个对象的过程。
1. 新生成了一个对象
2. 链接到原型
3. 绑定 this
4. 返回新对象
```
function create() {
    // 创建一个空的对象
    let obj = new Object()
    // 获得构造函数
    let Con = [].shift.call(arguments);// argements 转数组 所以这个create实际上是需要一个参数作为构造函数的。
    // 链接到原型
    obj.__proto__ = Con.prototype;
    // 绑定 this，执行构造函数
    let result = Con.apply(obj, arguments)
    // 确保 new 出来的是个对象
    return typeof result === 'object' ? result : obj
}
```

从性能和可读性上面来看，```let obj = {a:1}```这样创建会好很多。


##### 五、instanceof？
instanceof实际上就是循着原型链，然后通过判断是否能找到一个原型的构造函数跟需要判断的函数相等。如果相等就返回true，不等就返回false。
```
function instanceof(left, right) {
    // 获得类型的原型
    let prototype = right.prototype
    // 获得对象的原型
    left = left.__proto__
    // 判断对象的类型是否等于类型的原型
    while (true) {
    	if (left === null)
    		return false
    	if (prototype === left)
    		return true
    	left = left.__proto__
    }
}
```

##### 六、this
除了箭头函数之外，this实际上指向的就是调用他的对象。

箭头函数的this，来自于他外部的作用域。

当然还有bind、call、apply等这些改变this的指向就不一一仔细记录了。

##### 七、执行上下文。
Es6之前（不包含ES6），js都是函数级作用域，所以每一个函数就是一个作用域。
并且Js采用的是词法作用域，所以变量这些东西在被定义的时候，就已经决定了他对应的作用域。
而当我们在当前作用域找不到对应的变量的时候，就会去上一级找，直到找到这个变量或者是报undefined
```
var a = 1;
function foo(){
    function demo(){
        console.log(a);
    }
    demo();
}
foo();
// demo 在执行的时候，就会找内部是否有a，找不到那么就去上一级foo内部找，还是找不到就去到最顶级window找，找到了然后输出了。

这样一个一层嵌一层的作用域，就形成了作用域链。
```

另外多说一个点，js在执行的过程中的非异步代码上下文环境实际上是一种栈式的结构。进入了某一个函数（上下文环境），就会把它存放到栈里，执行完毕之后出栈，继续执行。
比如首先是window，此时执行了foo 那么栈就变成了 window->foo（左是最底），之后又执行了demo，所以变成了window -> foo -> demo，demo执行完毕，出栈，就变成了window->foo，继续执行foo的内容，执行完毕，foo出栈，继续执行window，结束。


##### 七、变量提升。
Es6之前（不包含Es6），变量声明都是用的var，js解析引擎在执行代码的时候，会优先定义变量（不赋值），所以就算是在调用后声明的变量，也可以正常执行。
另外函数也是如此，也会提升
```
b();
c();//error
function b(){
   console.log(a); //undefined
}

var a = 1;
var c = function(){
    console.log(a);
}
```
c之所以会报错，是因为c是一个变量，声明虽然提升，但是在调用的时候还没有被赋值，是undefined，所以会报错。