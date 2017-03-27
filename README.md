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
```$xslt
alert(typeof test1); //Object 
alert(test1 instanceof Object); //true
```