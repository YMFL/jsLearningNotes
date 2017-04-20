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

## 懒加载
#### 懒加载图片
原理：定时器（或其他方式，如滚动到一定位置），再给img的src属性指定图片路径；
```jsx harmony
setTimeout(function(){
    document.querySelectorAll('img')[0].src='https://avatars1.githubusercontent.com/u/18079377?v=3&s=460'
},2000)
```
