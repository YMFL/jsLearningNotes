## 创建对象
#### 工厂模式：
```jsx harmony
function createPerson(name, age, job){
    var o = new Object();
    o.name = name;
    o.age = age;
    o.job = job;
    o.sayName = function(){
    console.log(this.name);
    };
    return o;
}
var person1 = createPerson("Nicholas", 29, "Software Engineer");
var person2 = createPerson("Greg", 27, "Doctor");
```
优点：解决了创建多个相似对象的问题
缺点：没有解决对象识别的问题（即怎样知道一个对象的类型）
#### 构造模式
```jsx harmony
function Person(name, age, job){
    this.name = name;
    this.age = age;
    this.job = job;
    this.sayName = function(){
    console.log(this.name);
    };
}
var person1 = new Person("Nicholas", 29, "Software Engineer");
var person2 = new Person("Greg", 27, "Doctor");
```
与工厂模式的差异：<br /> 
1.没有显式创建对象；<br /> 
2.直接将属性和方法赋值给this对象；<br /> 
3.没有return语句<br /> 
按照惯例，构造函数始终都应该以一个大写字母开头，而非构造函数则应该以一个小写字母开头。构造函数也是函数，只是用来创建对象。
创建Person新实例，必须使用new操作符，以这种方式调用构造函数实际上会经历以下4个步骤：<br /> 
（1）创建一个新的对象；<br /> 
（2）将构造函数的作用域赋给新对象（this指向这个新对象）；<br /> 
（3）执行构造函数中的代码（为新对象添加属性和方法）；<br /> 
（4）返回新对象<br /> 
创建的对象，有一个constructor（构造函数）属性，该属性指向Person：
```jsx harmony
console.log(person1.constructor == Person); //true
console.log(person2.constructor == Person);
```
构造函数的问题：每个方法毒药重新创建一遍，如person1和person2中的sayName()方法；
#### 原型模式
```jsx harmony
function Person(){}
Person.prototype.name = "Nicholas";
Person.prototype.age = 29;
Person.prototype.job = "Software Engineer";
Person.prototype.sayName = function(){
    console.log(this.name);
};
var person1 = new Person();
person1.sayName(); //"Nicholas"
var person2 = new Person();
person2.sayName(); //"Nicholas"
console.log(person1.sayName == person2.sayName); //true
```
理解原型对象：
无论什么时候，只要创建了一个新函数，就会根据一组特定的规则为该函数创建一个prototype属性，这个属性指向函数的原型对象。
所有原型对象都会自动获得一个constructor(构造函数)的属性，这个属性包含一个指向prototype属性所在函数的指针。
可以通过isPrototypeOf()方法来确定对象之间是否存在这种关系:
```jsx harmony
console.log(Person.prototype.isPrototypeOf(person1)); //true
console.log(Person.prototype.isPrototypeOf(person2)); //true
```
ECMAScript 5 增加了一个新方法，叫Object.getPrototypeOf()，在所有支持的实现中，这个
方法返回[[Prototype]]的值。例如：
```jsx harmony
console.log(Object.getPrototypeOf(person1) == Person.prototype); //true
console.log(Object.getPrototypeOf(person1).name); //"Nicholas"
```
通过使用hasOwnProperty()方法，什么时候访问的是实例属性，什么时候访问的是原型属性就
一清二楚了。
```jsx harmony
function Person(){}
Person.prototype.name = "Nicholas";
Person.prototype.age = 29;
Person.prototype.job = "Software Engineer";
Person.prototype.sayName = function(){
    console.log(this.name);
};
var person1 = new Person();
var person2 = new Person();
console.log(person1.hasOwnProperty("name")); //false
person1.name = "Greg";
console.log(person1.name); //"Greg"——来自实例
console.log(person1.hasOwnProperty("name")); //true
console.log(person2.name); //"Nicholas"——来自原型
console.log(person2.hasOwnProperty("name")); //false
delete person1.name;
console.log(person1.name); //"Nicholas"——来自原型
console.log(person1.hasOwnProperty("name")); //false
```
更加简单的原型语法：
```jsx harmony
function Person(){
}
Person.prototype = {
    name : "Nicholas",
    age : 29,
    job: "Software Engineer",
    sayName : function () {
        console.log(this.name);
    }
};
```
动态原型模式:
```jsx harmony
function Person(name, age, job){
    //属性
    this.name = name;
    this.age = age;
    this.job = job;
    //方法
    if (typeof this.sayName != "function"){
        Person.prototype.sayName = function(){
            console.log(this.name);
        };
    }
}
var friend = new Person("Nicholas", 29, "Software Engineer");
friend.sayName();
```
使用动态原型模式时，不能使用对象字面量重写原型。如果在已经创建了实例的情况下重写原型，那么就会切断现有实例与新原型之间的联系。

## this作用域
#### 对象
作为对象的方法调用，this指向obj对象
```jsx harmony
var obj = {
    a: 1,
    getA: function(){
        console.log ( this === obj ); // 输出：true
        console.log ( this.a ); // 输出: 1
    }
};
obj.getA();
```
#### 普通函数
作为普通函数调用，指向全局对象（window）
```jsx harmony
var getName = function(){
    return this;
};
console.log( getName() );//输出Window
```
有时候我们会遇到一些困扰，比如在div 节点的事件函数内部，有一个局部的callback 方法，callback 被作为普通函数调用时，callback 内部的this 指向了window
```html
<html>
    <body>
        <div id="div1">我是一个div</div>
    </body>
    <script>
        window.id = 'window';
        document.getElementById( 'div1' ).onclick = function(){
        console.log(this.id); // 输出：'div1'
        var callback = function(){
            console.log(this.id);// 输出：'window'
        }
        callback();
        };
    </script>
</html>
```
如果想让this指向div1，可以用一个变量保存div节点的引用：
```jsx harmony
document.getElementById( 'div1' ).onclick = function(){
var that = this; // 保存div 的引用
var callback = function(){
    console.log ( that.id ); // 输出：'div1'
}
callback();
};
```
在ECMAScript5的strict模式下，this不会指向全局对象，而是undefined:
```jsx harmony
function func() {
  "use strict"
  console.log(this)//输出"undefined"
}
func();
```
#### 构造函数
作为构造函数调用，当用new运算符调用函数时，该函数总会返回一个对象，通常情况下，this指向返回的这个对象
```jsx harmony
var MyClass = function(){
    this.name = 'sven';
};
var obj = new MyClass();
console.log ( obj.name );//输出：sven
```
特殊情况：构造器如果显式返回一个Object类型的对象,那么最终会返回这个显式的对象，而不是this:
````jsx harmony
var MyClass = function(){
    this.name = 'sven';
    return { // 显式地返回一个对象
        name: 'anne'
    }
};
var obj = new MyClass();
console.log(obj.name); // 输出：anne
````
如果构造器不显示的返回任何数据，或者返回一个非对象的数据，就不会造成上述问题：
```jsx harmony
var MyClass = function(){
    this.name = 'sven';
    return 'anne'; // 返回string 类型
};
var obj = new MyClass();
console.log(obj.name); // 输出：sven
```
#### call和apply
用Function.prototype.call 或Function.prototype.apply 可以动态地改变传入函数的this：
```jsx harmony
var obj1 = {
    name: 'sven',
    getName: function(){
        return this.name;
    }
};
var obj2 = {
    name: 'anne'
};
console.log(obj1.getName()); // 输出: sven
console.log(obj1.getName.call( obj2)); // 输出：anne
```
apply和call方法能很好的体现JavaScript的函数式语言特性，call和apply方法的第一个参数都会成为函数this的值，当第一个为null时，this默认指向全局对象。
#### 丢失的this
```jsx harmony
var obj = {
    myName: 'sven',
    getName: function(){
        return this.myName;
    }
};
console.log( obj.getName() ); // 输出：'sven'
var getName2 = obj.getName;
console.log(getName2()); // 输出：undefined
```
调用obj.getName时，getName方法是作为obj对象的属性被调用的，此时this指向obj，所以输出为'sven'。
当另外一个变量引用obj.getName,并调用时，this指向全局Window，所以为undefined；
```jsx harmonyx harmony
window.myName='window'
var obj = {
    myName: 'sven',
    getName: function(){
        return this.myName;
    }
};
var obj1 = {
    myName: '123',
};
console.log(obj.getName());//输出'sven',this指向obj
var getName=obj.getName;
console.log(getName());//输出'window',this指向Window
obj1.getName=obj.getName;
console.log(obj1.getName());//输出'123'，this 指向obj1
```
#### call和apply的区别
两者的作用相同，区别在于传入参数形式的不同。

apply：接受两个参数，第一个参数指定了函数体内this对象的指向，第二个参数为一个带下标的集合，这个集合可以为数组（类数组），apply方法把这个集合中的元素作为参数传递给被调用的函数：
```jsx harmony
var func=function(a,b,c){
    console.log([a,b,c]);
}
func.apply(null,[1,2,3]);//输出[1,2,3]
```
call：参数数量不固定，第一个参数也是代表函数体内this的指向，第二个参数开始往后，每个参数被依次传入函数：
```jsx harmony
var func = function( a, b, c ){
    alert ( [ a, b, c ] ); // 输出 [ 1, 2, 3 ]
};
func.call( null, 1, 2, 3 );
```
#### call和apply的用途
改变this指向
````jsx harmony
var obj1 = {
    name: 'sven'
};
var obj2 = {
    name: 'anne'
};
window.name = 'window';
var getName = function(){
    console.log( this.name );
};
getName(); // 输出: window
getName.call( obj1 ); // 输出: sven
getName.call( obj2 ); // 输出: anne
````
当执行getName.call( obj1 )这句代码时，getName 函数体内的this 就指向obj1 对象，所以此处的
```jsx harmony
var getName = function(){
    alert ( this.name );
};
```
实际上相当于：
```jsx harmony
var getName = function(){
    alert ( obj1.name ); // 输出: sven
};
```
实际开发中，经常会遇到this 指向被不经意改变的场景，比如有一个div 节点，div 节点的onclick 事件中的this 本来是指向这个div 的：
```jsx harmony
document.getElementById('div1').onclick=function() {
    console.log(this.id);//div1
    var func=function() {
      console.log(this.id);//输出：undefined
    }
    func();
}
```
这时候用call来修正func函数体内的this,使其依然指向div：
```jsx harmony
document.getElementById('div1').onclick=function() {
    var func=function() {
      console.log(this.id);//输出：undefined
    }
    func.call(this);
}
```
使用call来修正丢失的this
```jsx harmony
document.getElementById=(function(func) {
  return function() {
    return func.apply(document,arguments)
  }
})(document.getElementById);
var getId=document.getElementById;
var div =getId('div1');
console.log(div) //输出：div1
```
#### Function.prototype.bind
```jsx harmony
Function.prototype.bind = function( context ){
var self = this; // 保存原函数
    return function(){ // 返回一个新的函数
        return self.apply( context, arguments ); // 执行新的函数的时候，会把之前传入的context  当作新函数体内的this
    }
};
var obj = {
    name: 'sven'
};
var func = function(){
   console.log(this.name); // 输出：sven
}.bind(obj);
func();
```


## 数组去重
#### 方法一
定义一个数组res保存结果，遍历需要去重的数组，将需要去重的数组和res比较，如果该元素存在res中，不放入，否则放入数组中：
```jsx harmony
function unique(a) {
    var res=[];
    for(var i=0,len=a.length;i<len;i++){
        var item=a[i];
        for(var j=0,resLen=res.length;j<resLen;j++){//①
            if(res[j]===item){
                break;//break:结束①的循环;continue:结束当前j的循环，j+1的循环继续执行
            }
        }
        //当①的循环结束，如果还没有break，则表示item不存在res中
        if(j===resLen){
            res.push(item)
        }
    }
    return res;
}
var x=[1,1,'1','2',2,1]
var ans=unique(x)
console.log(ans);//输出 [1, "1", "2", 2]
```
优化：可以用ES5的Array.prototype.indexOf()方法来简化代码
```jsx harmony
function unique(a) {
    var res=[];
    for(var i=0,len=a.length;i<len;i++){
        var item=a[i];
        (res.indexOf(item)===-1)&&res.push(item)
    }
    return res;
}
var x=[1,1,'1','2',2,1]
var ans=unique(x)
console.log(ans);//输出 [1, "1", "2", 2]
```
用filter(callback,thisArg)，继续优化：
```jsx harmony
//filter
function unique(a) {
    //filter三个参数
    //item:元素的值
    //index:元素的索引
    //array:被遍历的数组
    var res=a.filter(function(item,index,array) {
        return array.indexOf(item)===index;//过滤条件
    })
    return res;
}
var a=[1,1,2,'1','2',2];
var ans=unique(a);
console.log(ans)//输出 [1, 2, "1", "2"]
```
#### 方法二
方法一是把数组中的元素和结果数组元素进行比较。换个思路：把数组中重复元素的最后一个元素放入数组结果
```jsx harmony
function unique(a) {
    var res=[];
    for(var i=0,len=a.length;i<len;i++){//①
        for(var j=i+1;j<len;j++){
            //
            if(a[i] === a[j]){
                j=++i;//当i增加时，直接进入①的下一个循环，不执行②处的代码
            }
        }
        res.push(a[i]);//②
    }
    return res;
}
var a = [1, 1, '1', '2', 1];
var ans = unique(a);
console.log(ans); // => ["1", "2", 1]
```
#### 方法三
sort()方法，使用sort()方法排序后，相同元素会被放在相邻的位置，只需比较相邻位置的元素即可
```jsx harmony
function unique(a) {
  return a.concat().sort().filter(function(item,pos,ary) {
    //!pos表示不为第一个参数
    //item !=ary[pos-1] 表示当前值和前一个值不相等
    return (!pos)||(item !==ary[pos-1]);
  });
}
var a = [1, 1, 3, 2, 1, 2, 4];
var ans = unique(a);
console.log(ans); // => [1, 2, 3, 4]
```
但是存在问题,1 和 "1" 会被排在一起，不区分顺序：
```jsx harmony
function unique(a) {
  return a.concat().sort().filter(function(item,pos,ary) {
    //!pos表示不为第一个参数
    //item !=ary[pos-1] 表示当前值和前一个值不相等
    return (!pos)||(item !==ary[pos-1]);
  });
}
var a = [1,'1', 1, 3, 2, 1, 2, 4,'1'];
var ans = unique(a);
console.log(ans); // =>[1, "1", 1, "1", 2, 3, 4]
```
#### 方法四
用javascript中的对象来做哈希表，可以去重完全由Number基本类型组成的数组。
```jsx harmony
function unique(a) {
  var seen={};
  return a.filter(function(item) {
    return seen.hasOwnProperty(item)?false:(seen[item]=true)
  })
}
var a=[1,'1',1,2,3,1,2,1,1];
var ans=unique(a);
console.log(ans)//输出 [1,2,3]
```
但是不能区分1和'1'的问题，改进：
```jsx harmony
function unique(a) {
  var ret=[];
  var hash={};
  for (var i=0,len=a.length;i<len;i++){
      var item  =a[i];
      var key=typeof(item) + item;
      if(hash[key]!==1){
          ret.push(item);
          hash[key]=1;
      }
  }
  return ret;
}
var a=[1,'1',1,'1','1'];
var ans=unique(a);
console.log(ans)// 输出 [1, "1"]
```
#### 方法五
ES6 利用Set()方法的一个特性(去重)：
```jsx harmony
function unique(a) {
  return Array.from(new Set(a));
}
var a = [{name: "hanzichi"}, {age: 30}, new String(1), new Number(1)];
var ans = unique(a);
console.log(ans);
```
## ES6学习
文档地址 [`http://es6.ruanyifeng.com/#README`](http://es6.ruanyifeng.com/#README)
#### let
1.不可重复声明；<br />
2.没有变量提升；<br />
3.暂时性死区；<br />
特别之处：for循环语句部分是一个父作用域，循环体内部是一个单独的子作用域：
```jsx harmony
for (let i = 0; i < 3; i++) {
  let i = 'abc';
  console.log(i);
}//输出三个'abc'
```
#### 块级作用域与函数声明
ES6之前，只有全局作用域和函数作用域，ES6引入块级作用域；
ES6规定，块级作用域中，函数声明语句的行为类似与let，在块级作用域之外不可引用。
#### const
const 声明一个只读变量，作用域同let.一旦声明，常量就要马上赋值并且不能改变： 
```jsx harmony
const PI = 3.1415;
PI // 3.1415
PI = 3;
// TypeError: Assignment to constant variable.
```
const本质：并不是变量的值不得改动，而是变量指向的内存地址不得改动，对于复合类型的数据（对象和数组），
变量指向的内存地址，保存的是一个指针，const只能保证这个指针是固定的，指针指向的数据结构不可控制。
```jsx harmony
const foo = {};

// 为 foo 添加一个属性，可以成功
foo.prop = 123;
foo.prop // 123

// 将 foo 指向另一个对象，就会报错
foo = {}; // TypeError: "foo" is read-only
```
如果想将对象冻结，可以使用Object.freeze({})方法。
```jsx harmony
const foo = Object.freeze({});
// 常规模式时，下面一行不起作用；
// 严格模式时，该行会报错
foo.prop = 123;
console.log(foo.prop)//输出 undefined
```
#### ES6的6种声明
var;function;let;const;import;class;
#### 顶层对象
浏览器顶层对象为window；  Node的顶层对象为global；  在ES5中，顶层对象的属性和全局变量是等价的。
```jsx harmony
window.a = 1;
a // 1
a = 2;
window.a // 2
```
为了兼容性，ES6规定：var和function命令声明的全局变量，依然是顶层对象的属性；let，const，class命令声明的全局变量，不属于顶层对象的属性。从ES6开始，全局对象与顶层对象的属性脱离。
```jsx harmony
var a = 1;
// 如果在Node的REPL环境，可以写成global.a
// 或者采用通用方法，写成this.a
window.a // 1
let b = 1;
window.b // undefined
```
#### global对象
ES5的顶层对象，非常不统一：
浏览器里是window,但是node和Web worker没有window.浏览器和Web worker里面，self也指向顶层对象，单node没有self。Node里面，顶层对象是global,但其他环境不支持。
为了都能取到顶层对象，现在一般使用this变量，但是有局限性。
```text
全局环境中，this会返回顶层对象。但是，Node模块和ES6模块中，this返回的是当前模块。
函数里面的this，如果函数不是作为对象的方法运行，而是单纯作为函数运行，this会指向顶层对象。但是，严格模式下，这时this会返回undefined。
不管是严格模式，还是普通模式，new Function('return this')()，总是会返回全局对象。但是，如果浏览器用了CSP（Content Security Policy，内容安全政策），那么eval、new Function这些方法都可能无法使用。
```
两种解决上述问题的方法：
```jsx harmony
// 方法一
(typeof window !== 'undefined'
   ? window
   : (typeof process === 'object' &&
      typeof require === 'function' &&
      typeof global === 'object')
     ? global
     : this);
// 方法二
var getGlobal = function () {
  if (typeof self !== 'undefined') { return self; }
  if (typeof window !== 'undefined') { return window; }
  if (typeof global !== 'undefined') { return global; }
  throw new Error('unable to locate global object');
};
```
有一个提案，引入global作为顶层对象。在所有环境下，global都是存在的，垫片库system.global模拟了这个提案：
```jsx harmony
// CommonJS的写法
require('system.global/shim')();
// ES6模块的写法
import shim from 'system.global/shim'; shim();
```
#### 数组的解构赋值与Iterator
```jsx harmony
//普通
let [a, b, c] = [1, 2, 3];
console.log(a);//1
console.log(b);//2
console.log(c);//3
//如果解构不成功，变量的值为undefined
let [x, y, ...z] = ['a'];
x // "a"
y // undefined
z // []
//
let [head, ...tail] = [1, 2, 3, 4];
head // 1
tail // [2, 3, 4]
//不完全解构：等号左边只匹配一部分等号右边的数组
let [x, y] = [1, 2, 3];
x // 1
y // 2
let [a, [b], d] = [1, [2, 3], 4];
a // 1
b // 2
d // 4
```
如果等号右边不是数组（不是可遍历结构），就会报错：
```jsx harmony
//全部报错
let [foo] = 1;
let [foo] = false;
let [foo] = NaN;
let [foo] = undefined;
let [foo] = null;
let [foo] = {};
```
上面的语句都会报错，因为等号右边的值，转为对象以后不具备Iterator接口(前五个)，要么本身就不具备Iterator接口（最后一个表达式）。
对于Set结构，也可以使用数组的解构赋值。
```jsx harmony
let [w, x, y, z] = new Set(['a', 'b', 'c', 'c']);
w;//'a'
x;//'b'
y;//'c'
z;//undefined, 应为Set有去重的功能
```
只要有Iterator接口，都可以使用解构赋值。
```jsx harmony
function* fibs() {
  let a = 0;
  let b = 1;
  while (true) {
    yield a;
    [a, b] = [b, a + b];
  }
}

let [first, second, third, fourth, fifth, sixth] = fibs();
sixth // 5
```
结构赋值，默认值,只有严格等于（===）才生效：
```jsx harmony
let[x=1]=[undefined];
x//1
let [x=1] =[null]
x//null
```
#### 对象的解构赋值
对象解构赋值和数组解构赋值区别：数组解构赋值按次序排列；对象的结构赋值，变量必须和属性名相同；
```jsx harmony
let {bar,foo}={foo:'aaa',bar:'bbb'}
foo//'aaa'
bar//'bbb'
let {baz}={foo:'aaa',bar:'bbb'}
baz //undefined
```
如果变量名和属性名不一致：
```jsx harmony
var { foo: baz } = { foo: 'aaa', bar: 'bbb' };
baz // "aaa"

let obj = { first: 'hello', last: 'world' };
let { first: f, last: l } = obj;
f // 'hello'
l // 'world'
```
对象解构赋值的内部机制，是先找到同名属性，然后再赋给对应的变量。真正被赋值的是后者，不是前者：
```jsx harmony
let{foo:baz}={foo:'aaa',bar:'bbb'};
baz//'aaa'
foo//error: foo is not defined
```
已经声明的变量用于解构赋值：
```jsx harmony
// 错误的写法
let x;
{x} = {x: 1};
// SyntaxError: syntax error
```
javascript会将{x}理解为代码块，从而发生语法错误。只有不将大括号写在行首，避免Javascript将其解释为代码块。
```jsx harmony
//正确写法
let x;
({x} = {x: 1});
```
#### 字符串解构赋值
```jsx harmony
const [a, b, c, d, e] = 'hello';
a // "h"
b // "e"
c // "l"
d // "l"
e // "o"
let {length : len} = 'hello';
len // 5
```
#### 数值和布尔值的解构赋值
```jsx harmony
let {toString: s} = 123;
s === Number.prototype.toString // true

let {toString: s} = true;
s === Boolean.prototype.toString // true
```
#### 函数参数的解构赋值
```jsx harmony
function add([x, y]){
  return x + y;
}

add([1, 2]); // 3

//另外一个例子
[[1, 2], [3, 4]].map(([a, b]) => a + b);
//[3,7]
```
#### 解构的用途
1.交换变量的值
```jsx harmony
let x = 1;
let y = 2;

[x, y] = [y, x];
```
2.从函数返回多个值
```jsx harmony
// 返回一个数组
function example() {
  return [1, 2, 3];
}
let [a, b, c] = example();
// 返回一个对象
function example() {
  return {
    foo: 1,
    bar: 2
  };
}
let { foo, bar } = example();
```
3.函数参数的定义
参数和变量名可以一一对应
```jsx harmony
// 参数是一组有次序的值
function f([x, y, z]) { }
f([1, 2, 3]);

// 参数是一组无次序的值
function f({x, y, z}) { }
f({z: 3, y: 2, x: 1});
```
4.提取JSON数据
快速提取JSON对象中的数据
```jsx harmony
let jsonData = {
  id: 42,
  status: "OK",
  data: [867, 5309]
};

let { id, status, data: number } = jsonData;

console.log(id, status, number);
// 42, "OK", [867, 5309]
```
5.函数参数的默认值
指定参数的默认值，就可以避免在函数内写：var foo=config.foo||'default foo'
```jsx harmony
jQuery.ajax = function (url, {
  async = true,
  beforeSend = function () {},
  cache = true,
  complete = function () {},
  crossDomain = false,
  global = true,
  // ... more config
}) {
  // ... do stuff
};
```
6.遍历Map结构
```jsx harmony
var map = new Map();
map.set('first', 'hello');
map.set('second', 'world');

for (let [key, value] of map) {
  console.log(key + " is " + value);
}
// first is hello
// second is world
//================
// 获取键名
for (let [key] of map) {
  // ...
}
// 获取键值
for (let [,value] of map) {
  // ...
}
```
7.输入模块指定方法
```jsx harmony
const { SourceMapConsumer, SourceNode } = require("source-map");
```
#### 数组扩展
Array.from()作用：将类数组对象和可遍历对象转为真正的数组。
```jsx harmony
let arrayLike = {
    '0': 'a',
    '1': 'b',
    '2': 'c',
    length: 3
};
// ES5的写法
var arr1 = [].slice.call(arrayLike); // ['a', 'b', 'c']
// ES6的写法
let arr2 = Array.from(arrayLike); // ['a', 'b', 'c']
```
Array.from()接受第二个参数的用法，作用类似数组的map方法，对每个元素进行处理，返回的数组为处理后的值：
```jsx harmony
Array.from([1,2,3],x=>x*x);//[1,4,9]
//等同于
Array.from([1,2,3]).map(x=> x*x);
```
Array.of()方法用于将一组值，转换为数组。
```jsx harmony
Array.of(3, 11, 8) // [3,11,8]
Array.of(3) // [3]
Array.of(3).length // 1
```
为什么会有Array.of()?用来解决由于参数不同导致的重载问题，行为统一
```jsx harmony
Array() // []
Array(3) // [undefined * 3]  当参数为1个时，指定的是数组的长度而不是
Array(3, 11, 8) // [3, 11, 8]
// 模拟Array.of();
function ArrayOf(){
  return [].slice.call(arguments);
}
```
#### 函数的扩展
指定函数默认值
```jsx harmony
//es5方法
function log(x, y) {
  y = y || 'World';
  console.log(x, y);
}
log('Hello') // Hello World
//es6方法
function Point(x = 0, y = 0) {
  this.x = x;
  this.y = y;
}
var p = new Point();
p // { x: 0, y: 0 }
```
函数有name属性，会返回函数名
```jsx harmony
var f = function () {};
// ES5
f.name // ""
// ES6
f.name // "f"
```
箭头函数（=>）,如果箭头函数的代码块部分多于一条语句，就是用大括号将他们括起来，并用return返回
```jsx harmony
var sum = (num1, num2) => { return num1 + num2; }
```
如果大括号被解释为代码块，那么箭头函数直接返回一个对象，必须在对象上加上括号
```jsx harmony
var getTempItem = id => ({ id: id, name: "Temp" });
```
#### 对象的扩展
属性简洁表示：
```jsx harmony
var foo = 'bar';
var baz = {foo};
baz // {foo: "bar"}
// 等同于
var baz = {foo: foo};
```
对象方法的name属性：
```jsx harmony
const person = {
  sayName() {
    console.log('hello!');
  },
};
person.sayName.name   // "sayName"
```
比较两个值是否相等：Object.is();
ES5只有两种方法(==)和(===)。
```jsx harmony
Object.is('foo', 'foo')// true
Object.is({}, {})// false


+0 === -0 //true
NaN === NaN // false

Object.is(+0, -0) // false
Object.is(NaN, NaN) // true
```
对象合并：Object.assign（）,将源对象的所有可枚举属性复制到目标对象中
```jsx harmony
var target = { a: 1 };
var source1 = { b: 2 };
var source2 = { c: 3 };
var source3 = { a: 3 };
Object.assign(target, source1, source2,source3);
target // {a:3, b:2, c:3}
```
#### Set和Map数据结构
类似数组，但是成员唯一，所以可以用来去重
```jsx harmony
// Set实例的属性和方法
// Set.prototype.constructor:构造函数
// Set.prototype.size：返回Set实例的成员总数。
// Set实例的方法分为两大类：操作方法（操作数据）和遍历方法（遍历成员）。
// 操作方法：</hr>
// add(value):添加某个值，返回Set结构本身。</hr>
// delete(value):删除某个值，返回一个布尔值，表示删除是否成功。</hr>
// has(value):返回一个布尔值，表示该值是否为Set的成员。</hr>
// clear():清除所有成员，无返回值。</hr>
// 遍历方法：</hr>
// keys():返回键名的遍历器</hr>
// values():返回键值的遍历器</hr>
// entries()：返回键值对的遍历器</hr>
// forEach()：使用回调函数遍历每个成员</hr>
```
Set结构，没有键名只有键值（键名和键值是同一个值），所有遍历的时，keys和values方法行为一致
```jsx harmony
let set = new Set(['red', 'green', 'blue']);

for (let item of set.keys()) {
  console.log(item);
}
// red
// green
// blue
for (let item of set.values()) {
  console.log(item);
}
// red
// green
// blue
for (let item of set.entries()) {
  console.log(item);
}
// ["red", "red"]
// ["green", "green"]
// ["blue", "blue"]
```
Map结构
类似对象（对象的键只是字符串），但是键