## React学习笔记
##### react
```jsx harmony
import React from 'react';
//AppComponent为组件名称  需要替换
class AppComponent extends React.Component {
  render() {
    return (
      <div>
      </div>
    );
  }
}
AppComponent.defaultProps = {
};
export default AppComponent;
```
##### react-dom
用法
```jsx harmony

```




#### Redux
管理不断变化的 state 非常困难。如果一个 model 的变化会引起另一个 model 变化，那么当 view 变化时，
就可能引起对应 model 以及另一个 model 的变化，依次地，可能会引起另一个 view 的变化。
Redux的作用：统一管理状态。

Action:就是把数据从应用中传到store的有效载荷，他是store数据的唯一来源，用store.dispatch()将action传到store中。
Action只是描述了有事情发生，并没有指明应用如何更新state。
```jsx harmony 
    const ADD_TODO ='ADD_TODO'
    {
        type:ADD_TODO，
        text:'Build my first Redux app'
    }
```
Reducer
指明应用如何更新state