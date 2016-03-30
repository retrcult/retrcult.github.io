---
layout: post
title: HTML5 Touch Events
tags: [Mobile, JavaScript]
---

<!--more-->

### 标准的触摸事件

#### Webkit内的触摸事件

| 事件名称      | 描述       | 包含touch数组 |
| ----------- | --------- | ------------ |
| touchstart  | 触摸开始    | 是 |
| touchmove   | 触摸点改变  | 是 |
| touchend    | 触摸结束    | 是 |
| touchcancel | 触摸被取消  | 否 |

！touches数组是触摸对象数组，触摸对象携带着我想要的数据

#### 触摸对象的属性

| 属性         | 描述       |
| ----------- | --------- |
| identifier  | 本次触摸的唯一标识符，只要用户的手指保持在屏幕上，这个值就不会改变 |
| screenX     | 触点相对于屏幕左侧的距离 |
| screenY     | 触点相对于屏幕顶部的距离 |
| clientX     | 触点相对于浏览器窗口左侧的距离 |
| clientY     | 触点相对于浏览器窗口顶部的距离 |
| pageX       | 触点相对于页面左侧的距离 |
| pageY       | 触点相对于页面顶部的距离 |
| target      | 获取事件源，类似于`event.target` |

！其实还有三个属性：radiusX/radiusY，rotationAngle，force，但由于兼容性差且浏览器实现有差异，在此不做介绍。


### IE10的触摸事件

#### IE指针事件

| 事件名称          | 描述（在触摸设备上）       |
| --------------- | ---------------------- |
| MSPointerDown   | 触摸开始 |
| MSPointerMove   | 接触点移动 |
| MSPointerUp     | 触摸结束 |
| MSPointerOver   | 触摸点移动到元素内，相当于`mouseover` |
| MSPointerOut    | 触摸点离开元素，相当于`mouseout` |

！IE叫指针事件，因为该事件有三种触发形式：鼠标单击，电子笔轻触和手指触摸。

#### MSPointerEvent属性

| 属性             | 描述                   |
| --------------- | ---------------------- |
| hwTimestamp     | 创建事件的时间（ms） |
| isPrimary       | 标识该指针是不是主指针 |
| pointerId       | 指针的唯一ID（类似于触摸事件的标识符） |
| pointerType     | 一个整数，标识了该事件来自鼠标、手写笔还是手指 |
| pressure        | 笔的压力，0-255，只有手写笔输入时才可用 |
| rotation        | 0-359的整数，光标的旋转度（如果支持的话）|
| tiltX/tiltY     | 手写笔的倾斜度，只有用手写笔输入时才支持 |

{% highlight js %}
function handleEvent(event){
  switch(event.pointerType){
    case event.MSPOINTER_TYPE_TOUCH:
      //手指
      break;
    case event.MSPOINTER_TYPE_PEN:
      //手写笔
      break;
    case event.MSPOINTER_TYPE_MOUSE:
      //鼠标
      break;
    default:
      break;
  }
}
document.body.addEventListener("MSPointerDown", handleEvent, false);
{% endhighlight %}


