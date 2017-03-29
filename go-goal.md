## Suntime工作碰到的问题以及解决
#### cookie值为数组的情况
当设置cookie的值为数组时，会自动转换为字符串，并且会自动去除[]和引号等
```apple js
var x=[1,3,4,5]
document.cookie='hhh'+'='+x;
console.log(document.cookie);//输出  "hhh=1,3,4,5"
```





