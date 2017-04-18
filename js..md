## javascript动态插入代码并执行
#### eval方法
```jsx harmony
var responseHtml = 'alert("我是小白。。。")';//这里的代码就是页面上获取的
eval(responseHtml);
```
#### dom注入
```jsx harmony
var responseHtml = 'alert("我是小白。。。")';//这里的代码就是页面上获取的
var ele = document.createElement("script");
ele.innerHTML = responseHtml;
document.body.appendChild(ele);

```