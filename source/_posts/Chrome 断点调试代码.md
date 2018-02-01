---
title: Chrome 断点调试代码
date: 2018-01-30 15:33:05
tags: [chrome,断点]
---
简单地说，断点调试是指自己在程序的某一行设置一个断点，调试时，程序运行到这一行就会停住，然后你可以一步一步往下调试，调试过程中可以看各个变量当前的值，出错的话，调试到出错的代码行即显示错误，停下。

在web开发中，打断点是经常使用的调试代码的方法，现在在这里简略的翻译一下官方对此功能的讲解，并插入一些自己的说明。

文章翻译自：https://developers.google.com/web/tools/chrome-devtools/javascript/breakpoints
<!-- more -->
何时使用何种类型的断点：

**Line-of-code**：  知道在DevTools代码里要打点的具体区域；

**Conditional line-of-code**： 知道在DevTools代码里要打点的具体区域且设置条件，只有为真才执行断点操作；

**DOM**： 在 body 中添加，检测节点或其子节点的增删和属性变化；

**XHR**： 在 xhr url 包含特定内容的时候打点；

**Event listener**： 在触发特定事件的时候打点；

**Exception**： 在抛出异常的时候打点；

**Function**： 在特定函数被调用的时候打点；

**debugger**： 在书写的代码里希望打点的时候手动打点；


----------

断点方法
==
**Line-of-code breakpoints**
===

浏览器会执行解析操作到打点的那行代码之前（不包含那行代码）。

操作： f12 -> Sources Tab -> 双击打开需要打点的文件 -> 找到需要打点的那行代码 -> 在行数上单击，出现一个蓝色标记，打点完成。

在标记上再次单击，会删除当前断点。


![](breakPoint.png)


在代码中输入 debugger 同样能在指定位置暂停，除了不是在 DevTools UI 里设置以外和 line-of-code breakpoints 是相等的。
```Javascript
    console.log('a');

    console.log('b');

    debugger;  //在此暂停

    console.log('c');
```

----------


**Conditional line-of-code breakpoints**
===

在你希望有条件地打点的时候使用 conditional line-of-code 方法。

操作： f12 -> Sources Tab -> 双击打开需要打点的文件 -> 找到需要打点的那行代码 -> 右键行数，选择 Add conditional breakpoint -> 在出现的对话框中输入条件 -> 点击 enter，出现橙色标志，打点完成。


![](Conditional-breakPoint.png)


----------


**管理断点**
===

可以在 BreakPoints 面板上统一管理所有的断点。


![](breakPoint-manage.png)



上面的图片显示页面共有两个断点，一个在 get-started.js 第15行，一个在第32行。

    ●  checkbox 选择启用禁用断点

    ●   在条目上右键，可以选择移除当前断点、停用当前断点、禁用所有断点、移除所有断点、移除其他断点。

        禁用所有断点相当于把所有 checkbox 的勾都去掉；

        停用当前断点会让浏览器忽略掉此断点，但是断点位置和图标仍然保留，以便再次激活使用；

        移除断点会直接去掉此断点；


----------


DOM change breakpoints
===

在文档节点发生变化的时候暂停。

操作： f12 -> Elements Tab -> 点击希望监测的节点 -> 右击节点 -> 在出现的菜单上选择 Break on -> 按需要选择 Subtree modifications,Attribute modifications, Node removal。


![](dom-change-breakPoint.png)



dom 改变断点类型：

    ●    subtree modifications ， 在当前节点的子节点发生增加、移除、内容改变、交换顺序的情况的时候生效。其他情况例如当前节点发生了变化，或者子节点的属性发生了变化都不会触发。

    ●    attributes modifications ， 在当前节点的属性发生变化，例如增加属性、移除属性、属性值改变 的时候触发。

    ●    node removal， 在当前节点被移除的时候触发。


----------


**XHR breakpoints**
===

在你希望监听特定的 xhr 请求的时候，使用 xhr breakpoints 。 指定特定的字符串，当有包含此字符串的 xhr url 出现时触发，DevTools 会在 xhr.send() 方法被调用的地方暂停。

xhr breakpoints 对 fetch 请求也有效。

对于一些被封装好了的 xhr 请求例如 JQuery 的 ajax 方法，浏览器无法定位到被调用的地方。

操作： f12 -> Source Tab -> XHR Breakpoints 面板 -> 点击 + 号 -> 在出现的对话框里输入指定的字符串，浏览器会在出现包含此字符串的 xhr 请求时暂停（无论字符串在 url 的哪个位置） -> enter ， 完成断点。



![](xhr-breakPoint.png)


----------


**Event listener breakpoints**
===

监测事件，在事件发生后暂停，断点到事件绑定的位置。支持单独的事件例如 click ， 也支持一整个类别的事件，例如所有的鼠标事件。

操作： f12 -> Source Tab -> 展开 Event Listener Breakpoints 面板，会列出所有能监听的事件 -> 全选或展开之后单独选事件，完成断点。


![](event-listener-breakPoint.png)



上图是在移动设备的手持装置方向事件（横竖屏转换）上打点。


----------


**Exception breakpoints**
===

在你希望捕捉到报异常的代码的时候，使用 exception breakpoints。

操作： f12 -> Source Tab -> 点击 Pause on exceptions 暂停图标 -> 图标变成蓝色，表明启用了在未捕获到的异常出现的时候断点的功能。

可选操作： 勾选 Pause On Caught Exceptions ， 能够在捕获到异常的情况下也断点。
```Javascript
    try{

        throw 'a exception';

    }catch(e){

        console.log(e);

    }
```
上面 try 里面的代码会遇到异常，但是后面的 catch 代码能够捕获该异常。如果是所有异常都中断（**勾选了 Pause On Caught Exceptions**），那么代码执行到会产生异常的 throw 语句时就会自动中断；而如果是仅遇到未捕获异常才中断，那么这里就不会中断。一般我们会更关心遇到未捕获异常的情况。



![](exception-breakPoint.png)


----------


**Function breakpoints**
===

在你希望 debug 一个具体的函数时使用。功能与在此函数的第一行代码出打断点是一样的。

操作： 在代码里插入 debug(functionName) 或者在浏览器控制台调用。

代码里插入：
```Javascript
    function sum(a,b){

        let result = a+b;  // 浏览器在这里暂停

        return result;

    };

    debug  (sum);  // 参数是一个函数，不是字符串

    sum();
```
控制台调用：

控制台输入debug(sum)，点击 enter，再触发一次 sum 操作，就进入断点页面。

要注意确保目标函数与 debug 函数在同一个作用域里面，否则会报 ReferenceError：


![](function-breakPoint.png)



