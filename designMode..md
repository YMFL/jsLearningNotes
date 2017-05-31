via JavaScript设计模式与开发实践.pdf
## 单例模式
定义：保证一个类仅有一个实例，并提供一个访问它的全局访问点.
## 实现单例模式
用一个变量标记当前是否已经为某个类创建过对象，如果有则直接返回创建过的对象
```ecmascript 6
var Singleton = function( name ){
 this.name = name;
 this.instance = null;
};
Singleton.prototype.getName = function(){
 alert ( this.name );
};
Singleton.getInstance = function( name ){
 //判断instance   false(无实例)
 if ( !this.instance ){
 this.instance = new Singleton( name );
 }
 return this.instance;
};
///////////////////////////////
// 或者：
///////////////////////////////
var Singleton = function( name ){
 this.name = name;
};
Singleton.prototype.getName = function(){
 alert ( this.name );
};
Singleton.getInstance = (function(){
 var instance = null;
 return function( name ){
 if ( !instance ){
 instance = new Singleton( name );
 }
 return instance;
 }
})(); 

var a = Singleton.getInstance( 'sven1' );
var b = Singleton.getInstance( 'sven2' );
alert ( a === b ); // true
```
缺点：增加这个类的不透明性
#### 透明的单例模式

```ecmascript 6
var CreateDiv = (function(){
 // 自执行函数内声明的变量不会被垃圾回收机制回收
 var instance;
 var CreateDiv = function( html ){
     if ( instance ){
        return instance;
     }
     this.html = html;
     this.init(); 
     return instance = this;
 };
 CreateDiv.prototype.init = function(){
    var div = document.createElement( 'div' );
    div.innerHTML = this.html;
    document.body.appendChild( div );
 };
 return CreateDiv;
})(); 
var a = new CreateDiv( 'sven1' );
var b = new CreateDiv( 'sven2' ); 
alert ( a === b ); // true 
```
#### 代理实现单例模式

```ecmascript 6
var CreateDiv = function( html ){ 
 this.html = html; 
 this.init(); 
}; 
CreateDiv.prototype.init = function(){ 
 var div = document.createElement( 'div' ); 
 div.innerHTML = this.html; 
 document.body.appendChild( div ); 
}; 
var ProxySingletonCreateDiv = (function(){ 
 var instance; 
 return function( html ){ 
 if ( !instance ){ 
 instance = new CreateDiv( html ); 
 } 
 return instance; 
 } 
})(); 
var a = new ProxySingletonCreateDiv( 'sven1' ); 
var b = new ProxySingletonCreateDiv( 'sven2' ); 
alert ( a === b ); 
```
优点：负责管理单例的逻辑移到了代理类ProxySingletonCreateDiv中，CreateDiv就变成一个普通的类

#### JS中的单例模式
在js中，通常将全局变量当成单例来使用。为避免污染全局变量，通常会作为对象的属性
```
//使用命名空间
var namespace1 = { 
 a: function(){ 
 alert (1); 
 }, 
 b: function(){ 
 alert (2); 
 } 
}; 
//使用闭包封装私有变量
var user = (function(){ 
 var __name = 'sven', 
 __age = 29; 
 return { 
 getUserInfo: function(){ 
 return __name + '-' + __age; 
 } 
 } 
})(); 
```
#### 惰性单例
概念：需要的时候才创建对象实例。
```ecmascript 6
var createLoginLayer = (function(){ 
 var div; 
 return function(){ 
 if ( !div ){ 
 div = document.createElement( 'div' ); 
 div.innerHTML = '我是登录浮窗'; 
 div.style.display = 'none'; 
 document.body.appendChild( div ); 
 } 
 return div; 
 } 
})(); 
document.getElementById( 'loginBtn' ).onclick = function(){ 
 var loginLayer = createLoginLayer(); 
 loginLayer.style.display = 'block'; 
}; 
```
通用的惰性单例：将创建对象和管理单例逻辑隔离出来。
## 策略模式
策略模式指的是定义一系列的算法，把它们一个个封装起来。将不变的部分和变化的部分隔开是每个设计模式的主题，策略模式也不例外，
策略模式的目的就是将算法的使用与算法的实现分离开来。
```ecmascript 6
var performanceS = function(){}; 
performanceS.prototype.calculate = function( salary ){ 
 return salary * 4; 
}; 
var performanceA = function(){}; 
performanceA.prototype.calculate = function( salary ){ 
 return salary * 3; 
}; 
var performanceB = function(){}; 
performanceB.prototype.calculate = function( salary ){ 
 return salary * 2; 
}; 
var Bonus = function(){
 this.salary = null; // 原始工资
 this.strategy = null; // 绩效等级对应的策略对象
}; 
Bonus.prototype.setSalary = function( salary ){ 
 this.salary = salary; // 设置员工的原始工资
}; 
Bonus.prototype.setStrategy = function( strategy ){ 
 this.strategy = strategy; // 设置员工绩效等级对应的策略对象
}; 
Bonus.prototype.getBonus = function(){ // 取得奖金数额
 return this.strategy.calculate( this.salary ); // 把计算奖金的操作委托给对应的策略对象
}; 
var bonus = new Bonus(); 
bonus.setSalary( 10000 ); 
bonus.setStrategy( new performanceS() ); // 设置策略对象
console.log( bonus.getBonus() ); // 输出：40000 
bonus.setStrategy( new performanceA() ); // 设置策略对象
console.log( bonus.getBonus() ); // 输出：30000 

```
#### javascript版本的策略模式
```ecmascript 6
var strategies = { 
 "S": function( salary ){ 
 return salary * 4; 
 }, 
 "A": function( salary ){ 
 return salary * 3; 
 }, 
 "B": function( salary ){ 
 return salary * 2; 
 } 
}; 
var calculateBonus = function( level, salary ){ 
 return strategies[ level ]( salary ); 
}; 
console.log( calculateBonus( 'S', 20000 ) ); // 输出：80000 
console.log( calculateBonus( 'A', 10000 ) ); // 输出：30000


```