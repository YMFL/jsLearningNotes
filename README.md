## 创建对象
### 工厂模式：
```
function createPerson(name, age, job){
    var o = new Object();
    o.name = name;
    o.age = age;
    o.job = job;
    o.sayName = function(){
    alert(this.name);
    };
    return o;
}
var person1 = createPerson("Nicholas", 29, "Software Engineer");
var person2 = createPerson("Greg", 27, "Doctor");
```
优点：解决了创建多个相似对象的问题
缺点：没有解决对象识别的问题（即怎样知道一个对象的类型）
### 构造模式
```$xslt
function Person(name, age, job){
    this.name = name;
    this.age = age;
    this.job = job;
    this.sayName = function(){
    alert(this.name);
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
```$xslt
alert(person1.constructor == Person); //true
alert(person2.constructor == Person);
```
构造函数的问题：每个方法毒药重新创建一遍，如person1和person2中的sayName()方法；
### 原型模式
```$xslt
function Person(){
}
Person.prototype.name = "Nicholas";
Person.prototype.age = 29;
Person.prototype.job = "Software Engineer";
Person.prototype.sayName = function(){
    alert(this.name);
};
var person1 = new Person();
person1.sayName(); //"Nicholas"
var person2 = new Person();
person2.sayName(); //"Nicholas"
alert(person1.sayName == person2.sayName); //true
```
理解原型对象：
无论什么时候，只要创建了一个新函数，就会根据一组特定的规则为该函数创建一个prototype属性，这个属性指向函数的原型对象。
所有原型对象都会自动获得一个constructor(构造函数)的属性，这个属性包含一个指向prototype属性所在函数的指针。
可以通过isPrototypeOf()方法来确定对象之间是否存在这种关系:
```
alert(Person.prototype.isPrototypeOf(person1)); //true
alert(Person.prototype.isPrototypeOf(person2)); //true
```
ECMAScript 5 增加了一个新方法，叫Object.getPrototypeOf()，在所有支持的实现中，这个
方法返回[[Prototype]]的值。例如：
```$xslt
alert(Object.getPrototypeOf(person1) == Person.prototype); //true
alert(Object.getPrototypeOf(person1).name); //"Nicholas"
```
通过使用hasOwnProperty()方法，什么时候访问的是实例属性，什么时候访问的是原型属性就
一清二楚了。
```$xslt
function Person(){
}
Person.prototype.name = "Nicholas";
Person.prototype.age = 29;
Person.prototype.job = "Software Engineer";
Person.prototype.sayName = function(){
    alert(this.name);
};
var person1 = new Person();
var person2 = new Person();
alert(person1.hasOwnProperty("name")); //false
person1.name = "Greg";
alert(person1.name); //"Greg"——来自实例
alert(person1.hasOwnProperty("name")); //true
alert(person2.name); //"Nicholas"——来自原型
alert(person2.hasOwnProperty("name")); //false
delete person1.name;
alert(person1.name); //"Nicholas"——来自原型
alert(person1.hasOwnProperty("name")); //false
```
更加简单的原型语法：
```$xslt
function Person(){
}
Person.prototype = {
    name : "Nicholas",
    age : 29,
    job: "Software Engineer",
    sayName : function () {
        alert(this.name);
    }
};
```
动态原型模式:
```$xslt
function Person(name, age, job){
//属性
    this.name = name;
    this.age = age;
    this.job = job;
    //方法
    if (typeof this.sayName != "function"){
        Person.prototype.sayName = function(){
            alert(this.name);
        };
    }
}
var friend = new Person("Nicholas", 29, "Software Engineer");
friend.sayName();
```
使用动态原型模式时，不能使用对象字面量重写原型。如果在已经创建了实例的情况下重写原型，那么就会切断现有实例与新原型之间的联系。


