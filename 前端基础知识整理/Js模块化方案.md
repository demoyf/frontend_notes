##### 一、AMD(Asynchronous Module Definition)：异步模块定义
AMD是RequestJs对模块化定义的规范，AMD是一个概念，而RequestJs就是对这个概念的实现，它的模块化处理，主要是推崇依赖前置，也就是在定义的时候就将依赖的文件传入。
具体使用方法可以看下方的RequestJs代码
```
// 创建lib.js 无依赖的模块  路径是package/lib
define(function(){
    return {
        log:function(val){
            console.log(val)
        }
    }
})

// 引入该模块
define(['package/lib'],function(lib){
    function foo(){
        lib.log("123");
    }
    foo();
})

//上面就是一个定义模块的小例子

//如果需要引用网络的js的话。可以先配置好对应的路径，下面的代码就是在本地调用文件失败的时候，去网络获取。
require.config({
    "jquery":[js/jquery,"http://libs.baidu.com/jquery/2.0.3/jquery"]
})

require(["jquery"],function($){
    $(function(){
        
    })
})

```

##### 二、CMD(Common Module Definition) :公共模块定义
CMD是SeaJs对于模块化定义的规范，推崇的是就近依赖原则，也就是说在使用的时候才去引入。
使用方法：
```
// 定义一个lib  路径package/lib.js
define(function(require,exports,module){
    var foo = function(){
        console.log("123");
    }
    
    exports.foo = foo;
});

// 定义别名
seajs.config({
    alias:{
        "lib":"../package/lib.js"
    }
})

// 使用
define(function(require,exports,module){
    var tmp = require("lib");
    tmp.foo();
})


```

##### 三、CommomJs

