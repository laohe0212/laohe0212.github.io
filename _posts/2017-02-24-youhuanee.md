---
layout: default
title: 优化nee
---
<a href="https://wangxiaozhi123.github.io">返回</a>
<h2>{{ page.title }}</h2>
<p>{{ page.date | date_to_string }}</p>
Nee语言之前是不支持for循环的。我今天花了一点时间增加了这个功能。如何实现的呢？

主要是把for语句翻译成while，下面我给个具体的思路
<pre><code>for a ; b ; c do
    //code
end;
</code></pre>
经过翻译之后变成了
<pre><code>a;
while b do
    //code
    c;
end;
</code></pre>
但是完全变成这样也不行，因为语句a会逃逸出while块，作用域会影响到全局。这种解决方法也不难。我的关于变量作用域的变量是一个C++的类。我只需要在处理循环之前先保存变量作用域的上下文，循环之后会改变。循环结束再变回来。

C++代码如下:
<pre><code>void process_for(variable_table& _vt){
    //variable_table 是关于变量的一些信息
    variable_table temp_vt = _vt;
    //处理循环 _vt可能变化
    
    _vt = temp_vt;
}
</code></pre>

龙书里也有类似知识，这可能是最简单的语法糖了吧，这也让我理解了其他语言的语法糖的本质。一些可能是运行代码前的简单替换。例如C/C++的```#define```。或者深入虚拟机或编译器层面做到深层替换。理解了这些会让我容易写出更快更好的程序。
