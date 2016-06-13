---
layout: post
title: React Basics
tags: [ReactJS]
---

[Orig](http://www.ruanyifeng.com/blog/2015/03/react.html)

<!--more-->



{% highlight js %}

{% endhighlight %}


Components
this.props
this.state
event
this.refs
Special Non-DOM Attributes
key
ref
Inline Styles
Component Lifecycle




React 允许 HTML 语言直接写在 JavaScript 语言之中，不加任何引号，这就是 JSX 的语法。

JSX 的基本语法规则：
遇到 HTML 标签（以 `<` 开头），就用 HTML 规则解析；遇到代码块（以 `{` 开头），就用 JavaScript 规则解析。

{% highlight js %}
var names = ['Alice', 'Bob', 'Carlos', 'Dan'];
ReactDOM.render(
  <div>
    {
      names.map(name => {
        return <div>Halo, {name}!</div>;
      })
    }
  </div>,
  document.getElementById('root')
);
{% endhighlight %}



JSX 允许直接在模板插入 JavaScript 变量。
如果这个变量是一个数组，则会展开这个数组的所有成员。

{% highlight js %}
var nodes = [
  <h1>Hello world!</h1>,
  <h2>Let’s Get started</h2>,
  <blockquote>React is all about modular, composable components.</blockquote>,
  <p>The first thing you’ll notice is the XML-ish syntax in your JavaScript. We have a simple precompiler that translates the syntactic sugar to this plain JavaScript.</p>
];
ReactDOM.render(
  <div>{nodes}</div>,
  document.getElementById('root')
);
{% endhighlight %}


React 允许将代码封装成组件（Component），然后像插入普通 HTML 标签一样，在网页中插入这个组件。
组件类的第一个字母必须大写，否则会报错。
另外，组件类只能包含一个顶层标签，否则也会报错。

组件的用法与原生的 HTML 标签完全一致，可以任意加入属性。组件的属性可以在组件类的 `this.props` 对象上获取。
有一个地方需要注意，就是 class 属性需要写成 `className`，for 属性需要写成 `htmlFor`，这是因为 class 和 for 是 JavaScript 的保留字。

`this.props` 对象的属性与组件的属性一一对应，但是有一个例外，就是 `this.props.children`。它表示组件的所有子节点。

这里需要注意，`this.props.children` 的值有三种可能：如果当前组件没有子节点，它就是 <i>undefined</i>；如果有一个子节点，数据类型是 <i>object</i>；如果有多个子节点，数据类型就是 <i>array</i>，处理的时候要小心。

React 提供一个工具方法 `React.Children` 来处理 `this.props.children`。我们可以用 `React.Children.map` 来遍历子节点，而不用担心 this.props.children 的数据类型。更多的 React.Children 的方法，请参考[官方文档](https://facebook.github.io/react/docs/top-level-api.html#react.children)。

{% highlight js %}
class NodeList extends React.Component {
  render() {
    return <ul>
      {
        React.Children.map(this.props.children, (child, key) => {
          return <li>{key+1}. {child}</li>;
        })
      }
    </ul>;
  }
}
ReactDOM.render(
  <NodeList>
    <span>hello</span>
    <span>world</span>
  </NodeList>,
  document.getElementById('root')
);
{% endhighlight %}




组件的属性可以接受任意值，字符串、对象、函数等等都可以。有时，我们需要一种机制，验证别人使用组件时，提供的参数是否符合要求。组件类的 `PropTypes` 属性，就是用来验证组件实例的属性是否符合要求。此外，`getDefaultProps` 方法可以用来设置组件属性的默认值。PropTypes 和 getDefaultProps 设置，可以查看[官方文档](http://facebook.github.io/react/docs/reusable-components.html)。

{% highlight js %}
class NodeList extends React.Component {
  render() {
    return <div>
      <h1>{this.props.mainTitle}</h1>
    </div>;
  }
}
NodeList.propTypes = {
  mainTitle: React.PropTypes.string.isRequired
};
NodeList.defaultProps = {
  mainTitle: "React Demos"
};
ReactDOM.render(
  <NodeList mainTitle="React Props" />,
  document.getElementById('root')
);
{% endhighlight %}




React 组件并不是真实的 DOM 节点，而是存在于内存之中的一种数据结构，叫做虚拟 DOM （virtual DOM）。只有当它插入文档以后，才会变成真实的 DOM。根据 React 的设计，所有的 DOM 变动，都先在虚拟 DOM 上发生，然后再将实际发生变动的部分，反映在真实 DOM 上，这种算法叫做 DOM diff，它可以极大提高网页的性能表现。但是，有时需要从组件获取真实 DOM 的节点，这时就要用到 `ref` 属性。[官方文档](http://facebook.github.io/react/docs/more-about-refs.html)。


{% highlight js %}
class NodeList extends React.Component {
  constructor(props) {
    super(props);
    this.state = {
      text: "Click button above and view output here."
    };
    this.handleClick = this.handleClick.bind(this);
  }
  handleClick() {
    this.setState({
      text: this.refs.textInput.value
    });
  }
  render() {
    return <div>
      <h1>React Refs</h1>
      <input type="text" ref="textInput" placeholder="Type anything.." />
      <button type="submit" onClick={this.handleClick}>Click</button>
      <p ref="textOutput">{this.state.text}</p>
    </div>;
  }
}
ReactDOM.render(
  <NodeList/>,
  document.getElementById('root')
);
{% endhighlight %}

The `ref` attribute can be a callback function, and this callback will be executed immediately after the component is mounted. The referenced component will be passed in as a parameter, and the callback function may use the component immediately, or save the reference for future use (or both).

{% highlight js %}
  render: function() {
    return (
      <TextInput
        ref={function(input) {
          if (input != null) {
            input.focus();
          }
        }} />
    );
  },
// or using an ES6 arrow function:
  render: function() {
    return <TextInput ref={(c) => this._input = c} />;
  },
  componentDidMount: function() {
    this._input.focus();
  },
{% endhighlight %}

When attaching a ref to a DOM component like <i>\<div></i>, you get the DOM node back; when attaching a ref to a composite component like <i>\<TextInput></i>, you'll get the React class instance. In the latter case, you can call methods on that component if any are exposed in its class definition.

React 组件支持很多事件，除了 `onClick` 事件以外，还有 `onFocus`、`onKeyDown`、`onCopy`、`onSubmit`、`onScroll` 等，完整的事件清单请查看[官方文档](http://facebook.github.io/react/docs/events.html#supported-events)。



















































{% highlight js %}

{% endhighlight %}


Components
this.props
this.state
event
this.refs
Special Non-DOM Attributes
key
ref
Inline Styles
Component Lifecycle
