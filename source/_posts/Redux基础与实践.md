title: Redux基础与实践
date: 2017-07-27 22:27:31
tags:
## 启用 Redux
上述描述的只是一些建议上的东西，完成一些简单需求时可以遵守。倘若你的需求具备一定的复杂度，那么使用一套数据管理方案可能会增加可维护性。

### Redux 的一些概念
Redux 可以理解为一个数据的存储方案，具备发布订阅的事件模型，倡导三大原则。数据单向流动

#### 1. 单一数据源
```javascript
const store = createStore(reducers); // reducers 是一个纯函数
```

#### 2. 只读的 State 
从源码[https://github.com/reactjs/redux/blob/master/src/createStore.js](createStore) 可见，state 是 createStore 函数执行后，以闭包形式存在的一个对象，不能直接修改。
而当每次 dispatch 触发时，state 会被重新写入，以此方式保证了状态不会被不可预期的方式进行修改。

#### 3. 使用纯函数来执行修改 State


#### 订阅
```javascript
store.subscribe(function() {
    
});
```
#### 发布
```javascript
store.dispatch({
    type, payload
})
```
