#### 一、js内置数据类型
    js内置数据类型主要分为两大类，基本类型和对象类型，共计七种（ES6+）。
    值传递和引用传递，这个好像没啥好记录的，先不管，有需要再记录。

##### 1、基本类型

基本类型主要有以下几种：null、boolean、number、string、symbol、undefined。
数字都是属于浮点型，且NAN也属于number类型， 比较特殊的一点是，两个NAN之间并不相等。

##### 2、关于字面量
```
let a = 111;

/* 这里，111 属于字面量。
字面量：指的是如何表达这个值，一般在给变量赋值的时候，
等号的右边都可以被认为是字面量，字面量主要分为字符串、数组、对象、函数字面量。
比如上面的111 就属于字面量，字面量实际上就是可以直接使用且不可更改的值，
一般都是用变量去存储字面量，所以就是变量a存储了字面量111（字面量通常都是被变量描述，提升了可读性）。 */

```
为什么需要讲述字面量，因为 ```let a = 111``` 定义的时候，实际上a 变量还不是number类型，他在这个时候没有特指的类型。而当变量a在被使用的时候，比如 
```typeof a  or  a.toString(); ... ```，它在这个时候，才是一个number类型的变量。其他类型也是如此。

##### 3、对象类型

    对象即Object，js有一句话叫，万物皆对象，这句话实际上的由来，来自于所有的类型，原型链的最顶层，都指向Object。
    （关于原型/原型链，待记录 @todo）

对于js类型有了解的，应该知道null虽然是基本类型，但是它实际上是一个object。
原因是：在最初的js版本中，为了考虑性能使用了低位存储变量的类型信息，```000```开头的代表的是object，而null表示的是全0，所以null也就被判断为object，而后面版本为了区分未定义的变量类型，加入了undefined。

    （关于对象的深/浅 拷贝，待记录 @todo）

#### 二、类型判断方式
##### 1、Typeof

typeof 的结果是上述的七个类型之一，简单快捷，但是对于对象来说，简单的typeof并不适用。另外还有一个，```typeof function(){}``` 输出的是```"function"```,当然function不属于数据类型，不过多记录，只是稍微提一下。

##### 2、对象的类型判断方式

(1)、instanceof：最常用的对象判断方式（判断的类型必须已知，也就是判断对象是否是某个类型的子集（子集可能不是很准确）：eg ``` student instanceof person ```）

主要是直接顺着对象的原型链向上依次判断，如果能找到原型跟当前对象一致的，就返回true。

    （原型链中，记得记录instanceof @todo）

(2)、Object.prototype.toString：这个主要还是用来区分js内置的对象，对于自定义的实际上是判断不了的。
```
var getType = Object.prototype.toString;
getType.call([]);// [object Array]
getType.call({});// [object Object]
getType.call(function(){});// [object Function]

// 自定义对象
var person = funtion(){};
var demo = new person();
getType.call(demo); // [object Object]
console.log(demo instanceof person) // true 
```
(3)、constructor：构造器，指向的就是这个创建这个对象的类（类可能不是很准确）。不过如果是用这个判断的话，如果是有使用到函数式继承（原型继承）的类创建的对象，那么constructor可能指向的是父类。所以需要小心。


#### 三、类型转换
##### 1、隐式转换：
    在使用==做类型判断的时候，js会将类型不同的值转换之后再进行比较。
    而这个转换大体就是undefined、null、false、NAN、0、''、-0等会被转为false之外，其他所有的值，都为true。

##### 2、对象转基本类型
    对象在转基本类型的时候，是调用valueOf或者是toString，而这两个方法，都是可以重写的。
    因为如果自身存在这个函数，那么就不会去原型链中，找到对应的函数并且执行。
    而具体是调用哪一个方法，就看对象是倾向于那种类型，如果是Number，那么就调用ValueOf，如果是String，就调用toString。

```
//举个例子说明下什么叫倾向于：
var obj = {
    toString(){
        return 'string';
    },
    valueOf(){
        return 'number';
    }
}

alert(obj); // 弹出string
console.log(obj + 1); // number1;
// 第一个log就是倾向于string，第二个则是倾向于number
```

另外还有一点，如果是有重写了toString，而没有重写Valueof，那么就会调用的是toString。

==（如果在调用valueOf的过程中，返回的不是基本数据类型，那么就会调用toString。反之亦然）==
```
var obj = {
    toString(){
        return {};
    },
    valueOf(){
        return 'number';
    }
}

alert(obj);
console.log(obj + 1);
```

转换的小demo
```
let a = {
    valueOf() {
        console.log(1);
    	return 5;
    },
    toString(){
        return "222";
    }
}

alert(a);
console.log(a + 1);

String.prototype.toString = function(){
    return "1";
}
alert(new String("A"));
// 输出结果是1，所以证明Stirng对象在转化的时候，是调用了toString。

// 当然还有一个最高优先级的Symbol.toPrimitive,在对象转基本类型的时候，这个的优先级最高、

let a = {
  valueOf() {
    return 0;
  },
  toString() {
    return '1';
  },
  [Symbol.toPrimitive]() {
    return 2;
  }
}

```

##### 3、四则运算符
    当使用到了加法运算符的时候，只要有一方是字符串，那么另一方也会被转换为字符串。而其他运算符，则默认都会把非Number类型的转换为Number。

```
1 + '1'; // 11
2 * '2';// 4
[1,2] + [1,2];// 1,22,1 
```
==解释一下，这里为什么数组调用的是toString，原因是valueOf返回的依然是一个数组，不是字符串，那么非基本数据类型，就会再次调用toString==

##### 4、==操作符
举个例子：x与y之间的对比
```
// typeof 相同
if(typeof(x) == typeof(y) && (x === undefined || x === null){
    return true;
};

// 注意，number的对比，只要有其中一方是NAN，那么一定是false。
if(typeof(x) == typeof(y) && typeof(x)=='number' && (x数值===y数值 || (x===+0 && y === -0) || (x===-0 && y===+0))){
    return true;
}

if(typeof(x) == typeof(y) && typeof(x)=='string' && x所有字符所在位置===y所有字符所在位 ){
    return true;
}

if(typeof(x) == typeof(y) && typeof(x)=='boolean' && ((x===true && y===true) || (x===false && y===false))){
    return true;
}

if(typeof(x) == typeof(y) && typeof(x)=='Object' && (x引用地址===y引用地址)){
    return true;
}

// typeof 不同

typeof不同的时候，js的==会做隐式转换。转换后的结果相同，那么还是会返回true。
null和undefined之间比较是true;

在两个不同类型之间（undefined除外、而null本身是一个Object）做比较的时候，都会被转为number再比较。（boolean true是>0,false 是0）

Object（null除外）会先被调用toString或者是valueOf。

undefined和null之间的==为true。
null或者undefined跟其他类型的==，都是false。
```
==一道很有意思的题目==
```
[] == ![];

上面这道题的结果是true，原因在于。
[] 本身是一个对象，在运用在运算符!(优先级高)里，他应该是true(对象)。![] 则变成了false。
所以 [] == ![] 变成了 [] == false;
两个类型不同，那么就需要转换为number，再做比较。
false转换为数字应该是0；
[] 转换就需要调用toSrting，结果是'';（数组的转换规则）// 这里的话，应该也是先尝试调用了valueOf，发现转换结果还是对象，就调用了toString。
'' 再转换成数字，就是0.
最终的结果就是0 == 0。
```


```
[]+{} => [Object Object];
{}+[] => 0;
console.log({}+[]) => log [Object Object];

之所以{}+[]是0，原因在于当{}出现在最前面的时候。js引擎会将它解释为代码执行块。所以它是独立的一个块，该语句执行结束之后，就剩下了+[] 就是0了。

```
