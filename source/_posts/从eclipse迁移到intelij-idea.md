title: 从eclipse迁移到intelij idea
date: 2016-01-13 15:16:34
tags: Java Web 环境配置
---

我最早时候的前端开发其实是比较喜欢用Webstorm这个IDE的，因为在于css和js方面的易用性，然后在考拉的时候由于环境搭建的原因，只能使用eclipse。
返回学校之后，恶补了一下Java Web的基础知识，在目前我们每台设备都需要跑一个tomcat的基础上，想要提高前端开发的效率，建议还是使用 idea这个IDE。
<!--more-->
[http://www.ituring.com.cn/article/37792](http://www.ituring.com.cn/article/37792)这个文档详细讲解了如何从 eclipse迁移到idea

## idea中的Web项目的配置项

### 一、进入Project Sturcture
{%asset_img clipboard.png%}
### 二、Project 选择 1.7
{%asset_img clipboard2.png%}
### 三、配置web.xml路径与resource目录
{%asset_img clipboard15.png%}

### 四、引入modules,把当前项目引入
{%asset_img clipboard3.png%}

### 五、引入之后如下图所示 

{%asset_img clipboard4.png%}

### 六、配置一下sourse
> Maven搭建的Spring Mvc 的架构source文件一般在 “src\main\java”内，相应的resource目录和excluded目录配置一下

* source标签用于编译生成class
* test用于单元测试
* Resource即java-resource目录
* excluded 打包输出目录

{%asset_img clipboard5.png%}

### 七、dependencies
> 目录配置需要的包 maven应该是默认导入的

{%asset_img clipboard6.png%}
如果未导入，点击“+”号，选中“Library”

### 八、配置artifacts
> 项目打包配置 点击加号选中 war exploded

{%asset_img clipboard9.png%}

### 九、最后配置 Run 参数

> 增加一个Tomcat Server 容器，并配置deployment

{%asset_img clipboard13.png%}
### 十、run

{%asset_img clipboard14.png%}