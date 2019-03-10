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
CommonJs是另外一种模块化思路，主要是将单个文件作为一个模块，每一个模块具有独立的作用域，在需要使用到这个模块的时候，将它引入，这个思想实际上有点类似于上面所说的SeaJs。CommonJs的初衷是Js有标准模块，可以被更好的管理，实现像JAVA、PHP这些语言一样具备大型应用开发的能力。这个规范一开始应用于Node中，而前端的webpack也支持了CommonJs。

在CommonJs规范中：
- 一个文件就是一个模块，拥有单独的作用域。
- 普通方式定义的变量、函数、对象都属于该模块内。
- 通过require来加载模块。
- 通过exports和modul.exports来暴露模块中的内容。

小demo：
```
// 定义一个lib  
let obj = {
    foo(){
        console.log("123");
    }
}

module.exports = obj;

// 使用
let lib = require('../package/lib');
lib.foo();
```

##### 四、Es6新特性
Es6之后，规范了模块化的使用方式，所以AMD和CMD的模块化方案，实际上后续使用的比例也逐渐下降。ES6的新特性，实际上跟CommonJs大致相同，只是使用方法上有所不同。
小demo：
```
// 定义一个lib
export default {
    foo(){
        console.log("123");
    }
}

// 使用
import lib form '../package/lib';
lib.foo();
```

##### 总结
以上就记录这么多，模块化的优点显而易见，无论是代码的维护，还是减少冗余，模块化都是一个非常好的选择，需要什么功能，直接调用相应的模块。
