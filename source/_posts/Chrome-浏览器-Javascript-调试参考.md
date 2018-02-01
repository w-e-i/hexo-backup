---
title: Chrome 浏览器 Javascript 调试参考
date: 2018-01-30 15:45:00
tags: [chrome,调试]
---
此文章翻译自 https://developers.google.com/web/tools/chrome-devtools/javascript/reference， 是对 chrome 下调试 javascript 的工具和方法介绍。

调试 js 需要结合浏览器断点操作，具体可见我的上一篇文章：[Chrome 断点调试代码](https://w-e-i.github.io/2018/01/30/Chrome%20%E6%96%AD%E7%82%B9%E8%B0%83%E8%AF%95%E4%BB%A3%E7%A0%81/)。
<!-- more -->



---

1、调试图标概览
==

打上断点之后，需要操作对应图标进行调试，图标如下：


![](icons.png)



从左到右分别是：

**Pause/Resume script execution**：暂停/恢复脚本执行（程序执行到下一断点停止）。

**Step over next function call**：执行到下一步的函数调用（执行当前断点所在行，跳到下一行且暂停）。

**Step into next function call**：进入当前函数，在第一行暂停。

**Step out of current function**：跳出当前执行函数。

**Deactive/Active all breakpoints**：关闭/开启所有断点（不会取消）。

**Pause on exceptions**：异常情况自动断点设置。


----------

**Pause/Resume script execution（ F8）**
===
在断点暂停后，点击恢复脚本执行，直到下一个断点为止。

长按图标，会出现灰色的播放按钮，鼠标移上去再松开左键，会忽略所有的断点，强制渲染完整的页面。


----------

**Step over next function call （F10）**
===
当在一行代码中暂停，代码里包含一个与正在调试的问题无关的函数时，可以点击此图标直接解析该函数，而不是进入函数内部逐行执行debug操作。

例如，当你在 debug 以下代码：
```Javascript
    function updateHeader() {

        var day = new Date().getDay();

        var name = getName(); // A

        updateName(name); // D

    };

    function getName() {

        var name = app.first + ' ' + app.last; // B

        return name; // C

    }
```
假设现在是在 A 处暂停。点击 '跳过下个函数调用' 图标，浏览器会解析被跳过的函数里的所有代码（这里是 B 和 C），然后在 D 处再次暂停。


----------

**Step into next function call （ F11）**
===
当断点找到了要 debug 的确切函数，点击图标进入此函数内部，逐行查看分析里面的变量值和方法。

例如：
```Javascript
    function updateHeader() {

        var day = new Date().getDay();

        var name = getName(); // A

        updateName(name); // D

    };

    function getName() {

        var name = app.first + ' ' + app.last; // B

        return name; // C

    }
```
此时在 A 处打点暂停了，而 A 处就是与问题相关的函数，点击进入函数里，会在 B 处暂停，再次点击会在 C 处暂停，同时 B 处会显示 'name' 变量的值。


----------

**Step out of current function （Shift + F8）**
===
进入到一个与正在 debug 的问题无关的函数后，可以点击此图标解析函数剩下的代码，跳出函数到下一行。

例如：
```Javascript
    function updateHeader() {

    var day = new Date().getDay();

        var name = getName(); // A

        updateName(name); // D

    };

    function getName() {

        var name = app.first + ' ' + app.last; // B

        return name; // C

    }
```
现在在 B 处暂停，step out 之后，浏览器解析 getName() 函数剩下的代码（C），然后在 D 处再次暂停。


----------
2、调试区域其他功能概览
==
**Run all code up to a certain line**
===
如果你在 debug 一个很长的函数，里面包含了很多与问题无关的代码，需要区分出来。

首先在函数里设置第一个断点，执行至暂停，然后，有三种方法 debug ：

    1、使用 step into function 逐行解析查看结果，会浪费不少时间；

    2、根据结果判断哪些是无用的代码，越过它们再设置下一个断点，使用 resume script 执行到下一个断点；

    3、根据结果判断哪些是无用的代码，在下一个需要暂停的地方，右键行数，选择 “continue to here”，浏览器会直接解析到那一行并暂停，推荐的方法。


----------

**View the current call stack**
===
在一行代码里暂停时，可以在 Call Stack 面板查看是哪些栈将你带到了当前断点（到达当前函数调用了哪些函数）。如果不是在一行代码里暂停， Call Stack 面板是没有内容的。

如果要查看异步函数，可以勾选 Async 。（在 windows 版本中没有此选项，默认显示异步函数）。

点击函数会跳到此函数调用的地方。蓝色箭头是当前查看的函数。


![](callstack.png)


----------
**Copy stack trace**
===
在 Call Stack 面板里右键，选择 Copy stack trace 可以将面板里的 stack 信息复制到 clipboard。


![](trace.png)



复制的信息格式如下（函数名称、在代码里的行数）：
```Javascript
    getNumber1(get-started.js:35)

    inputsAreEmpty(get-started.js:22)

    onClick(get-started.js:15)
```
----------
**Restart the top function of the call stack**
===
在调试函数的过程中，想回到函数的开头重新 debug 的时候，可以在 Call Stack 面板中对应的函数上右键，选择 Restart Frame 而无需在开头打断点。Call Stack 面板里是断点函数以及所涉及到的其他函数，最顶端的函数是当前的断点函数。

例如：
```Javascript
    function factorial(n) {

        var product = 0; // B

        for (var i = 1; i <= n; i++) {

        product += i;

    };

    return product; // A

    }
```
现在在 A 处暂停，点击 Restart Frame 之后，会在 B 处暂停。


![](restart-frame.png)


----------
**Ignore a script or pattern of scripts**
===
在 debug 过程中，可以选择忽略部分脚本，不在 Call Stack 中显示，在 step into function 的时候也不会进入被忽略的脚本。

例如：
```Javascript
    function animate() {

        prepare();

        lib.doFancyStuff(); // A

        render();

    }
```
A是你确认与当前问题无关的第三方库，那就可以将它关入黑盒子里忽略掉。

在编辑区操作： 在 Source Tab 中双击打开文件 -> 在文件编辑区右键，选择 Blackbox script 。


![](blackbox.png)



在Call Stack 面板操作： 在 Call Stack 面板中找到对应的脚本 -> 右键选择 Blackbox script 。



![](blackbox-callstack.png)


在控制台设置黑盒： 控制台右上角找到 'Customize and control DevTools' 图标（或按F1） -> 选择 Blackboxing tab -> 点击 Add pattern ->  在对话框中输入脚本名字或脚本名字的正则表达式 -> 点击 Add。



![](blackbox-pattern.png)


----------
**Change thread context**
===
在网站有 web workers 或者 service workers 线程的时候，需要分别查看主线程和这两个线程的 context ，可以在 Threads 面板切换。


![](thread.png)



上图蓝色箭头处是当前线程的 context ， 可以点击切换其他线程。


----------
**View and edit local, closure, and global properties**
===
在断点暂停时，可以在 Scope 面板里查看和编辑局部、闭包和全局范围内的属性和变量的值。不会显示不可枚举的属性。

双击一个属性值可以输入新的值。


![](scope.png)


----------
**Run snippets of debug code from any page**
===
如果在调试中，需要一次次在控制台输入相同的内容的话，可以使用 Snippets（代码片段） 功能减少重复劳动。代码片段是您在DevTools中编写、存储和运行的可执行脚本。

Snippets 是全局的，在浏览器的所有标签页都能找到和运行。

具体可查看 https://developers.google.com/web/tools/chrome-devtools/snippets。


----------
**Watch the values of custom JavaScript expressions**
===
在 debug 过程中，如果希望重点观察某些变量的值（而不是在 Scope 面板里层层点开），可以加入 Watch 面板。Watch 面板里的值会在执行代码时自动刷新。


![](watch.png)



'+' 图标创建新的 expression；右边是刷新图标，手动刷新变量的值；鼠标移动到变量上，在右侧会出现删除图标。


----------
**Make a minified file readable**
===
可以将最小化了的代码还原成对人友好的形式。

点击代码编辑区域左下角的 '{}' 图标。



![](formatter.png)


----------
**Edit a script**
===
如果要尝试修复 bug ， 不需要切换到编辑器修改，再刷新当前页面。你可以直接在代码编辑区域修改代码然后保存看看修改后的效果。如果是最小化了的代码，可以先还原成对人友好的格式。

tips：确定修改方向之后，记得在编辑器代码里修改保存。

操作： 修改代码 -> 按 Command + S （mac）或 Ctrl + S （windows, Linux）保存修改 -> 查看效果。


![](edit.png)
