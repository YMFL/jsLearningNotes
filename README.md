## 创建对象
#### 工厂模式：
```js
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
```js
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
```js
console.log(person1.constructor == Person); //true
console.log(person2.constructor == Person);
```
构造函数的问题：每个方法毒药重新创建一遍，如person1和person2中的sayName()方法；
#### 原型模式
```js
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
```js
console.log(Person.prototype.isPrototypeOf(person1)); //true
console.log(Person.prototype.isPrototypeOf(person2)); //true
```
ECMAScript 5 增加了一个新方法，叫Object.getPrototypeOf()，在所有支持的实现中，这个
方法返回[[Prototype]]的值。例如：
```js
console.log(Object.getPrototypeOf(person1) == Person.prototype); //true
console.log(Object.getPrototypeOf(person1).name); //"Nicholas"
```
通过使用hasOwnProperty()方法，什么时候访问的是实例属性，什么时候访问的是原型属性就
一清二楚了。
```js
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
```js
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
```js
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
```js
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
```js
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
```js
document.getElementById( 'div1' ).onclick = function(){
var that = this; // 保存div 的引用
var callback = function(){
    console.log ( that.id ); // 输出：'div1'
}
callback();
};
```
在ECMAScript5的strict模式下，this不会指向全局对象，而是undefined:
```js
function func() {
  "use strict"
  console.log(this)//输出"undefined"
}
func();
```
#### 构造函数
作为构造函数调用，当用new运算符调用函数时，该函数总会返回一个对象，通常情况下，this指向返回的这个对象
```apple js
var MyClass = function(){
    this.name = 'sven';
};
var obj = new MyClass();
console.log ( obj.name );//输出：sven
```
特殊情况：构造器如果显式返回一个Object类型的对象,那么最终会返回这个显式的对象，而不是this:
````apple js
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
```apple js
var MyClass = function(){
    this.name = 'sven';
    return 'anne'; // 返回string 类型
};
var obj = new MyClass();
console.log(obj.name); // 输出：sven
```
#### call和apply
用Function.prototype.call 或Function.prototype.apply 可以动态地改变传入函数的this：
```apple js
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
```apple js
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
```apple js
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
```apple js
var func=function(a,b,c){
    console.log([a,b,c]);
}
func.apply(null,[1,2,3]);//输出[1,2,3]
```
call：参数数量不固定，第一个参数也是代表函数体内this的指向，第二个参数开始往后，每个参数被依次传入函数：
```apple js
var func = function( a, b, c ){
    alert ( [ a, b, c ] ); // 输出 [ 1, 2, 3 ]
};
func.call( null, 1, 2, 3 );
```
#### call和apply的用途
改变this指向
````apple js
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
```apple js
var getName = function(){
    alert ( this.name );
};
```
实际上相当于：
```apple js
var getName = function(){
    alert ( obj1.name ); // 输出: sven
};
```
实际开发中，经常会遇到this 指向被不经意改变的场景，比如有一个div 节点，div 节点的onclick 事件中的this 本来是指向这个div 的：
```apple js
document.getElementById('div1').onclick=function() {
    console.log(this.id);//div1
    var func=function() {
      console.log(this.id);//输出：undefined
    }
    func();
}
```
这时候用call来修正func函数体内的this,使其依然指向div：
```apple js
document.getElementById('div1').onclick=function() {
    var func=function() {
      console.log(this.id);//输出：undefined
    }
    func.call(this);
}
```
使用call来修正丢失的this
```apple js
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
```apple js
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







