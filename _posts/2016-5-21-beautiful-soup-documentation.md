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

## 对象的种类 (Kinds of Objects)

Beautiful Soup 将复杂 HTML 文档转换成一个复杂的树形结构，每个节点都是 Python 对象，所有对象可以归纳为4种： `Tag`，`NavigableString`，`BeautifulSoup`，`Comment`。

{% highlight python %}
soup = BeautifulSoup('<b class="bold verybold" id="1">Extremely bold</b>')
soup.name
# u'[document]'
tag = soup.b
type(tag)
# <class 'bs4.element.Tag'>
tag.name
# u'b'
tag.attrs
# {'class': ['bold', 'verybold'], 'id': '1'}
type(tag.attrs)
# <type 'dict'>
type(tag['class'])
# <type 'list'>
type(tag['id'])
# <type 'str'>
tag.string
# u'Extremely bold'
type(tag.string)
# <class 'bs4.element.NavigableString'>
{% endhighlight %}


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

##### Multi-valued attributes

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


### NavigableString

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


### Comments and Other Special Strings

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



------



## 遍历文档树 (Navigating the Tree)

{% highlight python %}
html_doc = """
<html><head><title>The Dormouse's story</title></head>
<p class="title"><b>The Dormouse's story</b></p>
<p class="story">Once upon a time there were three little sisters; and their names were
<a href="http://example.com/elsie" class="sister" id="link1">Elsie</a>,
<a href="http://example.com/lacie" class="sister" id="link2">Lacie</a> and
<a href="http://example.com/tillie" class="sister" id="link3">Tillie</a>;
and they lived at the bottom of a well.</p>

<p class="story">...</p>
"""

from bs4 import BeautifulSoup
soup = BeautifulSoup(html_doc)
{% endhighlight %}


### 子节点 (Going Down)

一个 Tag 可能包含多个字符串或其它的 Tag，这些都是这个 Tag 的子节点。
但 Beautiful Soup 中字符串节点不支持这些属性，因为字符串没有子节点（string can’t have children）。


#### Navigating using tag names

Using a tag name as an attribute will give you only **the first** tag by that name.
If you need to get **all** the `<a>` tags, or anything more complicated than the first tag with a certain name, you’ll need to use one of the methods described in *Searching the tree*, such as `find_all()`:

{% highlight python %}
soup.a
# <a class="sister" href="http://example.com/elsie" id="link1">Elsie</a>
soup.find_all('a')
# [<a class="sister" href="http://example.com/elsie" id="link1">Elsie</a>,
#  <a class="sister" href="http://example.com/lacie" id="link2">Lacie</a>,
#  <a class="sister" href="http://example.com/tillie" id="link3">Tillie</a>]
{% endhighlight %}


#### .contents & .children

`.contents` 和 `.children` 属性仅包含 tag 的直接子节点。

tag 的 `.contents` 属性可以将 tag 的子节点以 list 的方式输出。字符串没有 `.contents` 属性，因为字符串没有子节点。

通过 tag 的 `.children` 生成器，可以对 tag 的子节点进行循环。

{% highlight python %}
p_story = soup.find('p', 'story')
p_story.contents
# [u'Once upon a time there were three little sisters; and their names were\n',
#  <a class="sister" href="http://example.com/elsie" id="link1">Elsie</a>,
#  u',\n',
#  <a class="sister" href="http://example.com/lacie" id="link2">Lacie</a>,
#  u' and\n',
#  <a class="sister" href="http://example.com/tillie" id="link3">Tillie</a>,
#  u';\nand they lived at the bottom of a well.']
len(p_story.contents)
# 7
story_first_para = p_story.contents[0]
story_first_para
# u'Once upon a time there were three little sisters; and their names were\n'
story_first_para.contents
# AttributeError: 'NavigableString' object has no attribute 'contents'
for child in p_story.children:
    print child
# Once upon a time there were three little sisters; and their names were
#
# <a class="sister" href="http://example.com/elsie" id="link1">Elsie</a>
# ,
#
# <a class="sister" href="http://example.com/lacie" id="link2">Lacie</a>
#  and
#
# <a class="sister" href="http://example.com/tillie" id="link3">Tillie</a>
# ;
# and they lived at the bottom of a well.
{% endhighlight %}

`BeautifulSoup` 对象本身一定会包含子节点，也就是说 \<html\> 标签也是 `BeautifulSoup` 对象的子节点：

{% highlight python %}
len(soup.contents)
# 1
soup.contents[0].name
# u'html'
{% endhighlight %}


#### .descendants

`.descendants` 属性可以对所有 tag 的子孙节点进行递归循环：

{% highlight python %}
head_tag = soup.head
head_tag.contents
# [<title>The Dormouse's story</title>]
for child in head_tag.descendants:
    print(child)
# <title>The Dormouse's story</title>
# The Dormouse's story

len(list(soup.children))
# 1
len(list(soup.descendants))
# 25
{% endhighlight %}


#### .string

如果 tag 只有一个 `NavigableString` 类型子节点，那么这个tag可以使用 `.string` 得到子节点；
如果 tag 包含了多个子节点，tag 就无法确定 `.string` 方法应该调用哪个子节点的内容，`.string` 的输出结果是 None：

{% highlight python %}
title_tag = head_tag.contents[0]
title_tag.string
# u'The Dormouse's story'
print(soup.html.string)
# None
{% endhighlight %}


#### .strings 和 stripped_strings

如果 tag 中包含多个字符串，可以使用 `.strings` 来循环获取：
输出的字符串中可能包含了很多空格或空行，使用 `.stripped_strings` 可以去除多余空白内容：

{% highlight python %}
for string in soup.stripped_strings:
    print(repr(string))
# u"The Dormouse's story"
# u"The Dormouse's story"
# u'Once upon a time there were three little sisters; and their names were'
# u'Elsie'
# u','
# u'Lacie'
# u'and'
# u'Tillie'
# u';\nand they lived at the bottom of a well.'
# u'...'
{% endhighlight %}

全部是空格的行会被忽略掉，段首和段末的空白会被删除。



### 父节点 (Going Up)


#### .parent

文档的顶层节点比如 \<html\> 的父节点是 `BeautifulSoup` 对象；
`BeautifulSoup` 对象的 `.parent` 是 None：

{% highlight python %}
title_tag.string.parent
# <title>The Dormouse's story</title>
html_tag = soup.html
type(html_tag.parent)
# <class 'bs4.BeautifulSoup'>
print(soup.parent)
# None
{% endhighlight %}


#### .parents

{% highlight python %}
link = soup.a
link
# <a class="sister" href="http://example.com/elsie" id="link1">Elsie</a>
for parent in link.parents:
    print(parent.name)
# p
# body
# html
# [document]
{% endhighlight %}



### 兄弟节点 (Going Sideways)


#### .next_sibling 和 .previous_sibling


#### .next_siblings 和 .previous_siblings


### 回退和前进 (Going Back and Forth)

#### .next_element 和 .previous_element
#### .next_elements 和 .previous_elements






------



## 搜索文档树

### 过滤器
#### 字符串
#### 正则表达式
#### 列表
#### True
#### 方法

### find_all()
#### name 参数
#### keyword 参数
#### 按CSS搜索
#### text 参数
#### limit 参数
#### recursive 参数

### 像调用 find_all() 一样调用tag
### find()
### find_parents() 和 find_parent()
### find_next_siblings() 合 find_next_sibling()
### find_previous_siblings() 和 find_previous_sibling()
### find_all_next() 和 find_next()
### find_all_previous() 和 find_previous()
### CSS选择器































{% highlight css %}

{% endhighlight %}



