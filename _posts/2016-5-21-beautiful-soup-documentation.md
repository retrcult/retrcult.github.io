---
layout: post
title: Beautiful Soup Documentation
tags: [Python, WebCrawler, BeautifulSoup]
permalink: beautiful-soup-documentation
---

[Orig][1]

[1]: https://www.crummy.com/software/BeautifulSoup/bs4/doc/index.zh.html
     "Beautiful Soup 4.4.0 documentation"


## 如何使用

{% highlight python %}
from bs4 import BeautifulSoup
soup = BeautifulSoup(open("index.html"))
soup = BeautifulSoup("<html>data</html>")
{% endhighlight %}

然后，Beautiful Soup 选择最合适的解析器来解析这段文档，如果手动指定解析器那么 Beautiful Soup 会选择指定的解析器来解析文档。
推荐使用 lxml 作为解析器，因为效率更高。

{% highlight python %}
soup = BeautifulSoup(html_doc， "lxml")
{% endhighlight %}

<!--more-->

## 对象的种类

Beautiful Soup 将复杂 HTML 文档转换成一个复杂的树形结构，每个节点都是 Python 对象，所有对象可以归纳为4种： `Tag`，`NavigableString`，`BeautifulSoup`，`Comment`。


### Tag

`Tag` 对象与 XML 或 HTML 原生文档中的 tag 相同：

{% highlight python %}
soup = BeautifulSoup('<b class="boldest">Extremely bold</b>')
tag = soup.b
type(tag)
# <class 'bs4.element.Tag'>
{% endhighlight %}

`Tag` 中最重要的属性： `name` 和 `attributes`

#### Name

每个 tag 都有自己的名字，通过 `.name` 来获取：

{% highlight python %}
tag.name
# u'b'
{% endhighlight %}

如果改变了 tag 的 name，那将影响所有通过当前 Beautiful Soup 对象生成的 HTML 文档：

{% highlight python %}
tag.name = "blockquote"
tag
# <blockquote class="boldest">Extremely bold</blockquote>
{% endhighlight %}

#### Attributes

一个 tag 可能有很多个属性。 tag `<b class="boldest">` 有一个 class 的属性，值为 boldest。

{% highlight python %}
tag['class']
# u'boldest'
{% endhighlight %}

也可以直接 `.attrs`

{% highlight python %}
tag.attrs
# {u'class': u'boldest'}
{% endhighlight %}

tag 的属性可以被添加，删除或修改。tag 的属性操作方法与字典一样：

{% highlight python %}
tag['class'] = 'verybold'
tag['id'] = 1
tag
# <blockquote class="verybold" id="1">Extremely bold</blockquote>

del tag['class']
del tag['id']
tag
# <blockquote>Extremely bold</blockquote>

tag['class']
# KeyError: 'class'
print(tag.get('class'))
# None
{% endhighlight %}

##### 多值属性

在 Beautiful Soup 中多值属性的返回类型是 list。
最常见的多值的属性是 `class`（一个 tag 可以有多个 CSS 的 class）。还有一些属性 `rel`，`rev`，`accept-charset`，`headers`，`accesskey`。

{% highlight python %}
css_soup = BeautifulSoup('<p class="body strikeout"></p>')
css_soup.p['class']
# ["body"， "strikeout"]

css_soup = BeautifulSoup('<p class="body"></p>')
css_soup.p['class']
# ["body"]
{% endhighlight %}

如果某个属性看起来好像有多个值，但在任何版本的 HTML 定义中都没有被定义为多值属性，那么 Beautiful Soup 会将这个属性作为字符串返回：

{% highlight python %}
id_soup = BeautifulSoup('<p id="my id"></p>')
id_soup.p['id']
# 'my id'
{% endhighlight %}

将 tag 转换成字符串时，多值属性会合并为一个值：

{% highlight python %}
rel_soup = BeautifulSoup('<p>Back to the <a rel="index">homepage</a></p>')
rel_soup.a['rel']
# ['index']
rel_soup.a['rel'] = ['index'， 'contents']
print(rel_soup.p)
# <p>Back to the <a rel="index contents">homepage</a></p>
{% endhighlight %}

如果转换的文档是 XML 格式，那么 tag 中不包含多值属性：

{% highlight python %}
xml_soup = BeautifulSoup('<p class="body strikeout"></p>'， 'xml')
xml_soup.p['class']
# u'body strikeout'
{% endhighlight %}


### 可以遍历的字符串

字符串常被包含在 tag 内。Beautiful Soup 用 `NavigableString` 类来*包装 tag 中的字符串*。
一个 `NavigableString` 字符串与 Python 中的 Unicode 字符串相同，并且还支持包含在 **遍历文档树** 和 **搜索文档树** 中的一些特性。 通过 `unicode()` 方法可以直接将 `NavigableString` 对象转换成 Unicode 字符串：

{% highlight python %}
tag.string
# u'Extremely bold'
type(tag.string)
# <class 'bs4.element.NavigableString'>
unicode_string = unicode(tag.string)
unicode_string
# u'Extremely bold'
type(unicode_string)
# <type 'unicode'>
{% endhighlight %}

tag 中包含的字符串不能编辑，但是可以被替换成其它的字符串，用 `replace_with()` 方法：

{% highlight python %}
tag.string.replace_with("No longer bold")
tag
# <blockquote>No longer bold</blockquote>
{% endhighlight %}

### BeautifulSoup

`BeautifulSoup` 对象表示的是一个文档的全部内容。大部分时候，可以把它当作 Tag 对象，它支持 **遍历文档树** 和 **搜索文档树** 中描述的大部分的方法。

因为 `BeautifulSoup` 对象并不是真正的 HTML 或 XML 的 tag，所以它没有 name 和 attribute 属性。但有时查看它的 `.name` 属性是很方便的，所以 `BeautifulSoup` 对象包含了一个值为 “**[document]**” 的特殊属性 `.name`

{% highlight python %}
soup.name
# u'[document]'
{% endhighlight %}

### 注释及特殊字符串

`Comment` 对象是一个特殊类型的 `NavigableString` 对象：

{% highlight python %}
markup = "<b><!--Hey， buddy. Want to buy a used parser?--></b>"
soup = BeautifulSoup(markup)
comment = soup.b.string
type(comment)
# <class 'bs4.element.Comment'>
comment
# u'Hey， buddy. Want to buy a used parser'
{% endhighlight %}

Beautiful Soup 中定义的其它类型都可能会出现在 XML 的文档中：`CData`，`ProcessingInstruction`，`Declaration`，`Doctype`。与 `Comment` 对象类似，这些类都是 `NavigableString` 的子类，只是添加了一些额外的方法的字符串独享。下面是用 `CDATA` 来替代注释的例子：

{% highlight python %}
from bs4 import CData
cdata = CData("A CDATA block")
comment.replace_with(cdata)

print(soup.b.prettify())
# <b>
#  <![CDATA[A CDATA block]]>
# </b>
{% endhighlight %}















































{% highlight css %}

{% endhighlight %}



