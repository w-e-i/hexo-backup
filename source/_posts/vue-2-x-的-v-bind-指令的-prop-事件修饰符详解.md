---
title: vue 2.x 的 v-bind 指令的 .prop 事件修饰符详解
date: 2018-01-30 15:21:03
tags: [vue,修饰符]
---
vue 官方文档对 .prop 修饰符的解释是：

![](prop.png)

使用例子：

![](prop-instance.png)

那么，具体的原理和用法是什么呢？这要从 html 的 DOM node 说起。
<!-- more -->


----------

> 在 html  标签里，我们可以定义各种 attribute。在浏览器解析 DOM 树渲染页面后，每个标签都会生成一个对应的 DOM 节点。节点是一个对象，所以会包含一些 properties，attributes 也是其中一个property。

定义
--

    Property：节点对象在内存中存储的属性，可以访问和设置。
    Attribute：节点对象的其中一个属性( property )，值是一个对象，可以通过点访问法 document.getElementById('xx').attributes 或者 document.getElementById('xx').getAttributes('xx') 读取，通过 document.getElementById('xx').setAttribute('xx',value) 新增和修改。
    在标签里定义的所有属性包括 HTML 属性和自定义属性都会在 attributes 对象里以键值对的方式存在。

例子：
```HTML
    <input id="input" type="foo" value="11" class="class"></input>
```
打印的 attribute 对象（NamedNodeMap 对象表示元素属性节点的无序集合）：

![](attribute.png)


----------

Property 与 Attribute 的差别
------------------------

1、Attribute 对象包含标签里定义的所有属性，Property 只包含 HTML 标准的属性，不包含自定义属性（eg: data-xxx）。

2、Attribute 里的属性的值是 html 标签上原始的值，除非使用 setAttribute() 方法更改，不会根据用户输入而改变（eg: input 标签）。Property 在页面初始化时会映射并创建 Attribute 对象里的标准属性，从而节点对象能以对象的访问方式获取标准属性。在用户输入内容修改了原始值后，Property 里对应的属性会随之变化。即，查看原始值使用 Attribute，查看最新值使用 Property。（input 的 value 值也可以通过 input.defaultValue 查看原始值）

3、Property 与 Attribute 的某些属性名称是完全一样的，例如 ref, id ；某些名称有些轻微差别，例如 Attribute 里的 for、class 属性映射出来对应 Property 里的 htmlFor、className；某些属性名称一样，但是属性值会有限制或者修改，不会完全一样，相关的属性有 src, href, disabled, multiple 等。

例子：
```HTML
    <input src="test.html"></input>
```
// input.src :
![](input-src.png)
// input.attributes.src.value:
![](input-attribute.png)

4、由于 Property 不能读取自定义属性，如果标签在开始的时候对标准属性定义了非标准范围内的值，Property 会默认选择一个标准值代替，导致与 Attribute 里的属性不完全相等。

例子：
```HTML
    <input id="input" type="foo"></input>
    // input.type === 'text'
    // input.attributes.type === 'foo'
```


Property 与 Attribute 各自的属性和方法
-----------------------------
Property: http://www.w3school.com.cn/jsref/dom_obj_all.asp
Attributes: http://www.w3school.com.cn/jsref/dom_obj_attributes.asp


.prop 修饰符用途
-----------

> v-bind 默认绑定到 DOM 节点的 attribute 上，使用 .prop 修饰符后，会绑定到 property



注意事项：
- 使用 property 获取最新的值；
- attribute 设置的自定义属性会在渲染后的 HTML 标签里显示，property 不会。

修饰符用途：

>  通过自定义属性存储变量，避免暴露数据
防止污染 HTML 结构

例如：
```HTML
    <input id="input" type="foo" value="11" :data="inputData"></input>
    // 标签结构: <input id="input" type="foo" value="11" data="inputData 的值"></input>
    // input.data === undefined
    // input.attributes.data === this.inputData

    <input id="input" type="foo" value="11" :data.prop="inputData"></input>
    // 标签结构: <input id="input" type="foo" value="11"></input>
    // input.data === this.inputData
    // input.attributes.data === undefined
```
