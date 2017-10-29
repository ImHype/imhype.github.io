title: 前端模板解析技术
date: 2017-10-27 15:16:34
tags: parser
---

本文是系列 “MVVM 框架创作指北” 系列中的一文，重点是分享下个人对于前端模板解析技术的见解。  
本文会以带入一些问题的方式来表述：
1. 什么是前端模板？
1. 什么是模板解析？


## 什么是前端模板？
主要有两个特点：
1. 支持写入表达式 (expression) 与一些语句 (statement)；
2. 支持异步渲染 (async rendering)；

以笔者开发的前端框架 [Daisy](https://github.com/daisyjs/daisy.js) 为例：
```js
class Componnet extends Daisy.component {
    state() {
        return {
            name: '君羽'
        }
    }
    render() {
        return `
            <div $if={{name}}>你好,{{name}}</div>
        `
    }
}
```

`render` 函数需要 `return` 出一个符合 `Daisy` 模板语法规则的字符串，以供模板解析阶段使用。


## 模板解析是什么？
通俗点讲，模板解析是文本渲染出页面，或生成可渲染页面的中间结构的过程。  

如果语法不是太复杂的话，可以直接通过对语法标签和代码片段进行分割，识别语法标签内的内容（循环、条件语句）然后拼装代码，具体可以参考这篇[博客](http://www.cnblogs.com/hustskyking/p/principle-of-javascript-template.html)。   

而如果模板的语法比较复杂，或者语法与 html 语法差别较大，采用上面的方式就不太适合。所以，这种情况下，我们需要借鉴编译器相关知识 -- 模板引擎本质上就是编译原理的前端（词法解析及语法解析）。

编译原理？ 词法解析？ 语法解析？ 

一下次冒出了三个字，这都是啥啥啥啊，宝宝还小，只想做一名简简单单的切图仔。

## 屠龙术
编译原理，通常是指讲编程语言（program language）转换成更低级的语言。

又称屠龙术，有一言叫做“屠龙之术奈何天下无龙”。由此可见，屠龙术之闻名。那今天让我们一起来揭开屠龙术神秘面纱吧。

### 一般流程


### 词法解析

### 语法解析
中间结构类似如下：
```json 
{
    "type": "Program",
    "body": [
        {
            "type": "Element",
            "name": "div",
            "attributes": [
                {
                    "type": "Attribute",
                    "name": "$if",
                    "value": {
                        "type": "Expression",
                        "value": {
                            "type": "Identifier",
                            "name": "name"
                        }
                    }
                }
            ],
            "directives": {},
            "children": [
                {
                    "type": "Text",
                    "value": "你好,"
                },
                {
                    "type": "Expression",
                    "value": {
                        "type": "Identifier",
                        "name": "name"
                    }
                }
            ]
        }
    ]
}
```