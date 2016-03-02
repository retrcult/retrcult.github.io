---
layout: post
title: Ghost Clicks in Mobile Browsers
tags: [Mobile, Browser, JavaScript]
---

[orig.Link](http://www.ayqy.net/blog/%E7%A7%BB%E5%8A%A8%E9%A1%B5%E9%9D%A2%E7%82%B9%E5%87%BB%E7%A9%BF%E9%80%8F%E9%97%AE%E9%A2%98%E8%A7%A3%E5%86%B3%E6%96%B9%E6%A1%88/)

### click与300ms延迟

用户碰触页面之后，需要等待一段时间来判断是不是双击（double tap）动作，而不是立即响应单击（click），等待的这段时间大约是300ms。

移动事件提供了`touchstart`、`touchmove`、`touchend`却没有提供tap支持，主流框架（库）都是手动实现了自定义tap事件，以求消除300ms延迟，提高页面响应速度。对于简单的页面，可以把`touchstart`或者`touchend`当作tap来用，但存在一些问题，比如手指接触目标元素，按住不放，慢慢移出响应区域，会触发touchstart事件执行对应的事件处理器（本不应该触发），touchend事件也存在类似的问题。

<!--more-->


### 点击穿透问题

点击穿透现象有3种：

##### 点击穿透问题：点击蒙层（mask）上的关闭按钮，蒙层消失后发现触发了按钮下面元素的click事件

蒙层的关闭按钮绑定的是touch事件，而按钮下面元素绑定的是click事件，touch事件触发之后，蒙层消失了，300ms后这个点的click事件fire，event的target自然就是按钮下面的元素，因为按钮跟蒙层一起消失了。

##### 跨页面点击穿透问题一：如果按钮下面恰好是一个有href属性的\<a\>标签，那么页面就会发生跳转

因为\<a\>标签跳转默认是click事件触发，所以原理和上面的完全相同。

##### 跨页面点击穿透问题二：这次没有mask了，直接点击页内按钮跳转至新页，然后发现新页面中对应位置元素的click事件被触发了

和蒙层的道理一样，js控制页面跳转的逻辑如果是绑定在touch事件上的，而且新页面中对应位置的元素绑定的是click事件，而且页面在300ms内完成了跳转，三个条件同时满足，就出现这种情况了。

非要细分的话还有第四种，不过概率很低，就是新页面中对应位置元素恰好是a标签，然后就发生连续跳转了。。。诸如此类的，都是点击穿透问题。


### 解决方案
问题已经很明了了，有很多解决方案，但思路不外乎2种：

##### 不要混用touch和click

既然touch之后300ms会触发click，只用touch或者只用click就自然不会存在问题了

##### 吃掉（或者说是消费掉）touch之后的click

依旧用tap，只是在可能发生点击穿透的情形做额外的处理，拿个东西来挡住、或者tap后延迟350毫秒再隐藏mask、pointer-events、在下面元素的事件处理器里做检测（配合全局flag）等等，能吃掉就行


### 详细解决方案

#### 只用touch
最简单的解决方案，完美解决点击穿透问题

把页面内所有click全部换成touch事件（touchstart、’touchend’、’tap’），需要特别注意\<a\>标签，\<a\>的href也是click，需要去掉换成js控制的跳转，或者直接改成\<span\>和tap控制跳转。如果要求不高，不在乎滑走或者滑进来触发事件的话，、\<span\>和touchend就可以了，毕竟tap需要引入第三方库。

#### 只用click
下下策，因为会带来300ms延迟，页面内任何一个自定义交互都将增加300毫秒延迟，想想都慢。<br>
不用touch就不会存在touch之后300ms触发click的问题，如果交互性要求不高可以这么做，强烈不推荐，快一点总是好的

#### 拿个东西来挡住
比较笨的方法。

{% highlight js %}
#preventClick { width:100%; height:100%; position:absolute; z-index:1000; top:0; left:0; }

<body>
  <div id="preventClick"></div>
</body>

function onDeviceReady() {
  setTimeout(function(){
    $('#preventClick').hide();
  }, 300);
}
{% endhighlight %}

#### tap后延迟350ms再隐藏mask
改动最小，缺点是隐藏mask变慢了，350ms还是能感觉到慢的。<br>
只需要针对mask做处理就行，改动非常小，如果要求不高的话，用这个比较省力。

#### pointer-events
不建议使用。<br>
mask隐藏后，给按钮下面元素添上`pointer-events: none;`样式，让click穿过去，350ms后去掉这个样式，恢复响应。<br>
缺陷是mask消失后的的350ms内，用户可以看到按钮下面的元素点着没反应，如果用户手速很快的话一定会发现。

#### 在下面元素的事件处理器里做检测（配合全局flag）
比较麻烦，不建议使用。

#### 全局flag记录按钮点击的位置（坐标点），在下面元素的事件处理器里判断event的坐标点，如果相同则是那个可恶的click，拒绝响应
只是想法，没测试过，实在不行就用记录时间戳判断，等待350ms，这样就和pointer-events差不多。

#### fastclick
不介意多加载几KB的话。<br>
还是会有bug，更多信息请查看：[fastclick导致click事件触发两次的问题](http://www.foreverpx.cn/2015/07/02/Fastclick%E5%AF%BC%E8%87%B4click%E4%BA%8B%E4%BB%B6%E8%A7%A6%E5%8F%91%E4%B8%A4%E6%AC%A1%E7%9A%84%E9%97%AE%E9%A2%98/)。<br>
首先引入fastclick库，再把页面内所有touch事件都换成click。

