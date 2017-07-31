title: 如何面向现状编程
date: 2017-07-27 22:27:31
tags:
---

回顾这些年的前端发展，就前端开发模式上经历了很多阶段的发展。

* 刀耕火种 - 多年前的前端开发，需要掌握大量 CSS 兼容、JS 兼容技术、PS切图技术 来完成页面开发，动效制作；
* 命令式编程 - 使用 jQeury 工具库来解放繁琐 JS 兼容的工作，命令式 JS 书写模式大放异彩；
* 数据与视图的绑定 - 以 AngularJS 作为开端的 MV* 框架，亮点是（双向）数据绑定，将数据变化与视图改变直接关联，减少了大量命令式的 DOM 操作；
* 组件化 - React/Vue 作为典型代表，主张组件化的开发模式。这两者各有侧重，React 设计初衷就只是 View 层，Vue 则被设计为一款渐进式的开发框架。

<!-- more -->
抛开这些历史上，概念上的东西，我们来进入现实的开发场景。你接手一个项目后，最先做的是什么？

如果是一个新项目，你可能需要思考一些架构上的东西，比如：是否启用单页架构、使用什么开发框架、是否需要状态管理工具、资源构建怎么处理。嗯，你会饶有乐趣，然后兴致满满地开工。

事实上，新的项目并不多，我们更多时候也都是在维护现有代码，可能有很多事情会让你你觉得无奈的。比如，你对现有架构的不满意，对当前的业务逻辑书写得这么腐赞表示不理解。于是，你想要重构。当然，我也希望重构。但是，我们的生命是否允许你消耗这段时间来重构这段老的逻辑。答案，是否。

所以，更重要地就是，阻止更多的代码恶化，这似乎比你单枪匹马搞重构，有用的多的多。这也是本文的重点，探讨一些让业务代码更优雅更容易维护的一些思路。

## 关于 RegularJS 开发的一些槽点
RegularJS 是猪场海波大神开发的一款 MVVM 框架，本身其设计思路，和学习成本，都算中上。但是，作者对于设计思路在文档中的体现太少，导致使用者顺势而为的用户不多。比如说，下面的一些用法
1. 太多方法直接往 `this` 上放

```js
const ComponentA = Regular.extend({
    getList() {
        this.$request({
            url,
            success({
                code, body
            }) {
                if (code === 200) {
                    this.getListCallBack();
                }
            }
        })
    },
    getListCallBack({list}) {
        this.filterList(list);
    },
    filterList(list) {
        this.data.list = list.map(({title, content}) => {
            return {
                title, content
            };
        });
    }
});
```

当前函数式编程大热，也是有道理的。

纯函数对于可测试性和可预期性都有较大的优势。

所以，不如把一些无关 context 的方法抽出来吧，把他们作为一些纯函数在你的组件生命周期中调用。

比如，把发 Ajax 请求的方法抽成 service，统一管理，把一些对象处理函数独立成 filter，总之 this 上可能的干净，对后期的维护是很有帮助的。

2. 滥用双向绑定
双向绑定，是指数据更新与UI行为的绑定操作。经常会出现一个对象依次传入多层组件的情况，这时候最省力的数据更新方式，可能就是直接在自组件内修改这个对象。

Index 组件
```
<ComponentA obj={obj}></ComponentA>
```

ComponentA 组件
```
<ComponentB obj={obj}></ComponentB>
```

ComponentB 组件
```
<ComponentC obj={obj}></ComponentC>
```

ComponentC 组件
```
<input r-model={obj.a}/>
```

我们看到如上这种情况，看似很巧妙的结合 RegularJS 的双向绑定，做了数据更新的操作，也能让最外层组件可以使用最新的 obj 对象做一些事情。但是，对于最外层组件而言， obj 的变化是不可预期的。换言之，开发者很难通过直接查看最外层组件，感知到 obj 对象可能发生的改变。这也是，双向绑定带来的弊端。

那么，如何解决深层嵌套组件的数据更新问题呢，后面会提到。

3. 什么数据都放置到 this.data 上
这个问题的关键，是大家如何看待 MVVM 框架的 this.data。对于 MVVM 框架而言，最大的亮点莫过于视图与数据的绑定（单向 or 双向）， this.data 则是关键。

如果我们正视 data 的职责，data 只负责放置 UI渲染相关数据。那么，我们平时开发中，很多操作是存在争议的：
    1. 请求获取下来的数据，直接放置到 this.data 上的方式，后端在提供在前端的数据中，难免会存在一些冗余的字段，这些字段的存在会让你的 data 显得凌乱不清晰；
    2. 大家可能会将一些逻辑计算的数据也放置到 data 上，但是这些数据对于模板渲染也是无关的。

那么，你可能就能理解一些类似的话了：
「React只是一个优秀的视图层框架，他将复杂的状态管理留给了你」

## RegularJS 开发的一些建议
1. 尽量少地在 `this` 上放置方法，一般三类方法是必须的 1. 事件 handler; 2. compute get 方法; 3. data 的操作方法
```js
const filterList(list) {
    return list.map(({title, content}) => {
        return {
            title, content
        };
    });
}

const ComponentA = Regular.extend({
    computed: {
        title(){
            this.getTitles();
        }
    },
    onButtonClick() {
        service.getList({

        }).then(() => {
            this.setList(list);
        })
    },
    setList({list}) {
        this.data.list = filterList(list);
    },
    getTitles() {
        return this.data.list.map(item => item.title);
    }
});
```
    1. 事件 handler 可以让你的函数更贴近于事件编程方式，更便于理解；
    2. 使用 set 方法去设置 data 的数据，可以让你的 data 修改更可控；
    3. 将 computed 抽出成方法，可以在 js 中复用 computed 的逻辑；

原则就是，能挪出去的就挪出去作为纯函数。

2. 使用单向数据流替代双向绑定
双向绑定的使用，使用上是非常方便的，在深层嵌套的组件数据传递上，尤为明显。但它会使得数据的改变源头难以追踪，代码接受时也容易留坑，不好理解。

那么，我们应该控制住自己，不要过分依赖这种能力。换句话说，如果你肯定你的项目是不需要被维护的，那么你去使用吧（逃）

一般场景下，单向数据流的启用方式，自然是通过事件。

ComponentA

```html
<ComponentB
    obj={obj}
    on-btnClick={}
></ComponentB>
```

```js
Regular.extend({
    config() {
        extend(this.data, {
            obj: {
                clicked: false
            }
        })
    },
    onComponentBBtnClick() {
        this.setClicked();
    }
    setClicked() {
        this.data.obj.clicked = true;
    }
})
```
---- 
ComponentB
```html
<button disable={clicked} on-click={this.$emit('btnClick')}>点我！</button>
```

3. 更面向视图层的 data
解决这个问题，除了使用一个状态管理工具以外，没有一个较好的方案，所以能做的就是，尽可能少的向 data 上放置 UI 无关的数据。

## 启用 Redux
上述描述的只是一些建议上的东西，完成一些简单需求时可以遵守。倘若你的需求具备一定的复杂度，那么恭喜你，可以使用更适合问题的方案了 - redux。

### Redux 本身
Redux 可以理解为一个数据的存储方案，并具备发布订阅的事件模型。

#### 第一个 API
```javascript
const store = createStore();
```
#### 订阅
```javascript
store.subscribe(function() {
    
});
```
#### 发布
```javascript
store.dispatch(function () {

})
```
#### 发布
```javascript
store.dispatch(function () {
    
})
```
