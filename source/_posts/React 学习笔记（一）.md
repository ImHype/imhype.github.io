---
title: React 学习笔记
date: 2017-08-05 18:27:31
tags: React
---

React 本人处于初学阶段，主要学习资料是胡子大哈编写的 [React 小书](http://huziketang.com/books/react)。

## 使用 `create-react-app` 来创建 React 应用
```bash
$ npm install -g create-react-app
$ create-react-app hello-react
```

下载过程慢，可以将 npm registry 设置成 cnpm 镜像源，不过这条命令会永久修改镜像源，发布模块时记得指定 npm 官方 registry。

```bash
$ npm config set registry https://registry.npm.taobao.org
```

应用创建成功之后， npm start 启动应用
```bash
$ npm start
```

如果成功启动会有提示，即可打开 localhost:3000 端口。

查看 `npm start` 脚本，发现其实是调用了 `react-scripts` 的命令。

查看了 [`react-scripts` 源码](https://github.com/facebookincubator/create-react-app/blob/master/packages/react-scripts/scripts/start.js#L68)，发现其实内部原理和 `@foxman/plugin-webpack-dev-server` 类似，调用了 `webpack-dev-server` 与 `webpack` ，并提供了默认配置。

## 关于 JSX
使用 `JavaScript` 来描述 `DOM` 结构
```html
<div class='box' id='content'>
  <div class='title'>Hello</div>
  <button>Click</button>
</div>
```

用 React 提供的 API 来创建，代码如下
```js
React.createElement(
    "div",
    null,
    React.createElement(
        "h1",
        { className: 'title' },
        "React 小书"
    )
)
```

可想而知，这种方式其实是很不方便的，所以便有了 JSX。

JSX 的代码经过编译后，跑在浏览器上的就是如上形式的代码。

## React.createElement
```js
config {
    key, ref, __self, __source, ...props
}

ReactElement.createElement = function (type, config, children) {
    return ReactElement(type, key, ref, self, source, ReactCurrentOwner.current, props);
}
```

`createElement` 是对 `ReactElement` 的使用封装，暴露的接口简单化。最终 `return` 如下对象

```js
{
    // This tag allow us to uniquely identify this as a React Element
    $$typeof: REACT_ELEMENT_TYPE,

    // Built-in properties that belong on the element
    type: type,
    key: key,
    ref: ref,
    props: props,

    // Record the component responsible for creating this element.
    _owner: owner，
    _self,
    /**
    configurable: false,
    enumerable: false,
    writable: false,
    */
    _source,
    /**
    configurable: false,
    enumerable: false,
    writable: false,
    */
    _store： {
        validated
        /**
            configurable: false,
            enumerable: false,
            writable: true,
            value: false
        */
    }
}
```

## class Header extends Component 与 React.createClass
找到一份不错的资料：

* [React.createClass versus extends React.Component](https://toddmotto.com/react-create-class-versus-component/)
* [[译] React.createClass 对决 extends React.Component](https://www.peachis.me/react-createclass-versus-extends-react-component/)

Facebook 的意图是逐渐弃用 React.createClass， 而 es6 的写法确实使用更多的是 es6 自身的特性，而非 React 提供的封装，且用法更加简单，不需要使用者对 API 过多记忆。


## 组件的 render 方法
`render 方法`，目前看来是 `React` 暴露给使用者的一个钩子，执行该方法，返回使用者期望的一个 `DOM` 结构 `JavaScript` 表示。
```js
render () {
  const className = 'header'
  return (
    <div className={className}>
      <h1>React 小书</h1>
    </div>
  )
}
```

上文提到 JSX 会被编译成 `React.createElement`。

这个操作，似乎并没有什么黑魔法。比如，以下的操作会抛错。
```javascript
class App extends Component {
    constructor() {
        super();
        this.state = {
            text: 'To get started, edit <code>src/App.js</code> and save to reload.'
        }
    }
    render() {
        return (
            <div className="App">
                <p className="App-intro">{text}</p>
            </div>
        );
    }
}
```

你只能这么写

```js
render() {
    const text = this.state.text;
    return (
        <div className="App">
            <p className="App-intro">{text}</p>
        </div>
    );
}
```

## 组件嵌套
```js
class Title extends Component {
  render () {
    return (
      <h1>React 小书</h1>
    )
  }
}

class Header extends Component {
  render () {
    return (
      <div>
        <Title />
      </div>
    )
  }
}
```
就像普通 HTML Tag 一样使用，原理还是 JSX 解析时，把变量 `Title` 直接传入到了 `React.createElement` 里面，由于 `Title` 不是字符串变量，所以便启用组件嵌套的逻辑。


## 事件监听
```js
class Title extends Component {
  handleClickOnTitle (e) {
    console.log(e)
  }

  render () {
    return (
      <h1 onClick={this.handleClickOnTitle}>React 小书</h1>
    )
  }
}
```

React 的事件绑定一般为小驼峰的方式，具体替考官方 [SyntheticEvent - React](https://facebook.github.io/react/docs/events.html#supported-events)。

## event 对象

React.js 中的 event 对象并不是浏览器提供的，而是它自己内部所构建的。React.js 将浏览器原生的 event 对象封装了一下，对外提供统一的 API 和属性，这样你就不用考虑不同浏览器的兼容性问题。这个 event 对象是符合 [W3C 标准（ W3C UI Events ）](https://www.w3.org/TR/DOM-Level-3-Events/)的。

## event handler 中的 this
```js
class Title extends Component {
  handleClickOnTitle (e) {
    console.log(this)
  }

  render () {
    return (
      <h1 onClick={this.handleClickOnTitle}>React 小书</h1>
    )
  }
}
```

点击触发时，发现 console 出来值，是 undefined。很好理解，onClick 传入的只是一个函数，React 还做了一层 bind 操作，否则函数执行时 this 会指向 window，目的猜想应该是防止用户误操作导致修改了全局的值，修改 `undefined` 是会抱错的，开发阶段就能意识到这个问题。

所以，你需要这样解决

```js
class Title extends Component {
    handleClickOnTitle (e) {
        console.log(this)
    }

    render () {
        return (
            <h1 onClick={this.handleClickOnTitle.bind(this)}>React 小书</h1>
        )
    }
}
```

当然，更优雅的方式是这样的：

```js
class Title extends Component {
    constructor() {
        this.handleClickOnTitle = this.handleClickOnTitle.bind(this);
    }

    handleClickOnTitle (e) {
        console.log(this);
    }

    render () {
        return (
            <h1 onClick={this.handleClickOnTitle.bind(this)}>React 小书</h1>
        )
    }
}
```
