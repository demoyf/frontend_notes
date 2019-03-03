##### 普通场景的this
```
function f(){
    console.log(this.a);
}

var a = 1;



var obj {
    a:2,
    f:f
}

function d(){
    f();
}

var c = new obj();
f();
obj.f();
d();
输出结果：
1
2
1
```

接下来理性分析一波，通过上面的代码，了解下this的指向。

- 直接调用了f函数，无论在哪里，他始终都是指向window。
- obj调用了内部的f，this指向了obj。
- new obj，this被绑定在了c上。

>实际上上面的场景，都有一个共同点，就是谁调用了该函数，该函数内部的this就指向了谁。

有一个疑问就是，new 的时候，是不是已经绑定了this，还是说是在c.f调用函数的时候，再传入this？

##### 其他场景

1. 箭头函数中的this
```
var a = 1;
function a(){
    return ()=>{
        (()=>{
            console.log(this.a);
        })();
    }
}
a()();
 
输出
1
```

> 箭头函数，本身没有this，他的this，是来自于包裹箭头函数的函数，所以上面的代码，箭头函数的this，取决于a，而a在window下调用，所以this指向的是window。

2. bind、apply、call等强制改变this的。

```
function f(){
    console.log(this.a);    
}
var obj = {
    a:3
}
f.apply(obj);

输出
3
```

> apply、call、bind等，会将函数的this修改成为传入的对象，比如上面的f的this被强制改变成为obj，如果没有参数的话，就是window了。

另外一个问题，就是多次使用apply这些，会是什么样子的。
```
var a = 1;
var obj = {
    a:2
}
function f(){
    console.log(this.a);    
}

f.bind().bind(obj);
f();

输出
1
```

> 可以看到绑定的this，是第一次传进来的window。所以由此可以看出，多次改变函数的this，只有第一次生效。

![image](https://github.com/demoyf/frontend_notes/raw/master/note_img/16717eaf3383aae8.png)