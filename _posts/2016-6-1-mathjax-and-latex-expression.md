---
layout: post
title: MathJax and LaTex Expression
tags: [MathJax, LaTex]
use_math: true
---

[orig.Link](http://mlworks.cn/posts/introduction-to-mathjax-and-latex-expression/)

Config file:
http://docs.mathjax.org/en/latest/config-files.html

<!--more-->



MathJax 入门帮助
-----------


### 查看公式代码

可以在渲染完成的公式上右键点击唤出菜单：**Show Math As** > **Tex Commands**。

菜单中还提供了设置显示效果和渲染模式的选项。



### 在线编辑预览

推荐使用 [StackEdit](https://stackedit.io/editor) 学习 MathJax 的语法，它支持 Markdown 和 MathJax。

使用 [Detexify](http://detexify.kirelabs.org/classify.html)，你可以在网页上画出符号，Detexify 会给出相似的符号及其代码，但是不能保证它给出的符号可以在 MathJax 中使用，你可以参考 [Supported LaTeX commands | MathJax](http://docs.mathjax.org/en/latest/tex.html#supported-latex-commands) 或 [$\TeX$ Commands available in MathJax](http://www.onemathematicalcat.org/MathJaxDocumentation/TeXSyntax.htm) 以确定 MathJax 是否支持此符号。




MathJax 公式排版
-----------


### 行内公式 | inlineMath

表示公式嵌入到文本段中。用 `$` 或者 `\\(` `\\)` 开启公式环境，例如，

$ \sqrt{4} + e\^\pi $ 写作 `$ \sqrt{4} + e^\pi $`。

$ f: \{1, 2, 3, ...\} \rightarrow \mathcal{B} $ 写作 `\\( f: \{1, 2, 3, ...\} \rightarrow \mathcal{B} \\)`。

$ \sum_{r=0}\^n \binom{n}{r} v_a \geq 2\^n $ 写作 `$ \sum_{r=0}^n \binom{n}{r} v_a \geq 2^n $`。



### 行间公式 | displayMath

表示公式独自成为一个段落，居中显示。用 `$$` 或者 `\\[` `\\]` 开启公式环境，例如，

> 假设 $ x = 1 $，我们有
  \\[ 2\^n = (1+1)\^n = \sum_{i=0}\^n \binom{n}{r} 1\^r = \sum_{r=0}\^n \binom{n}{r} \\]

该公式可以用 <br/>
`$$ 2^n = (1+1)^n = \sum_{i=0}^n \binom{n}{r} 1^r = \sum_{r=0}^n \binom{n}{r} $$` 或 <br/>
`\\[ 2^n = (1+1)^n = \sum_{i=0}^n \binom{n}{r} 1^r = \sum_{r=0}^n \binom{n}{r} \\]` <br/>
写出。



### 公式内部空间

通常 MathJax 通过内部策略管理公式内部的空间，因此 `a  b` 与 `a     b` 都会显示为 $ a   b $。可以通过在 ab 间加入 `\,` 增加些许间隙，`\;` 增加较宽的间隙，`\quad` 与 `\qquad` [^ftnote1] 会增加更大的间隙，如，$ (a \qquad b) $。

如需在公式中输入文字，可以使用 `\text{…}`，如 `\lbrace x \in s \mid x \text{is extra large} \rbrace` 将得到 $ \lbrace x \in s \mid x \text{ is extra large} \rbrace $，
你同样可以在 `\text{…}` 中嵌入 `$…$`。

[^ftnote1]: [[meaning of quad]](http://tex.stackexchange.com/questions/119068/meaning-of-quad)



### 特殊字符转义

一些 MathJax 使用的特殊字符，可以用 `\` 将其转义为原来的含义，
如 $\$$ 用 `\$` 来表示。

其他需要转义的特殊字符包括：`\!`，`\#`，`\%`，`\&`，`\,`，`\;`，`\:`，`\_`，`\|`，`\{`，`\}`。

另外需要注意的是 MathJax 中用 `\\` 表示换行，如果你想使用`\`，必须要写 `\backslash`。



### 公式标记与引用

使用 `\tag{yourtag}` 来标记公式，如果想在之后引用该公式，则还需要加上 `\label{yourlabel}`，如 ` $$ a := x^2 - y^3 \tag{2.1}\label{2.1} $$ ` ：

$$ a := x\^2 - y\^3 \tag{2.1}\label{2.1} $$

为了引用公式，可以使用 `\eqref{yourlabel}`，如 ` $$ a + y^3 \stackrel{\eqref{2.1}} = x^2 $$ ` ：

$$ a + y\^3 \stackrel{\eqref{2.1}} = x\^2 $$

通过超链接可以跳转到被引用公式位置。



基本语法规则
-----------


### 括号


#### 小括号与方括号

使用原始的 `(` `)`，`[` `]` 即可，如 $ (2+3)[4+4] $。



#### 大括号

由于 `{` `}` 被用来分组（Groups），因此需要使用 `\{` 和 `\}` 来表示大括号，也可以用 `\lbrace` 和 `\rbrace` 表示，如 $ \{a*b\} $。



#### 尖括号

使用 `\langle` 和 `\rangle` 表示 ⟨ ⟩，如 $ \langle x \rangle $。



#### 上取整

使用 `\lceil` 和 `\rceil` 表示 ⌈ ⌉，如 $ \lceil x \rceil $。



#### 下取整

使用 `\lfloor` 和 `\rfloor` 表示 ⌊ ⌋，如 $ \lfloor x \rfloor $。



#### 不可见括号

使用 `.` 表示，如 $ \left. \frac12 \right\rbrace $。



#### 括号自适应

需要注意的是，原始符号并不会随着公式大小缩放。但使用 `\left` `\right` 可以自适应地调整括号大小。例如，

$$ \lbrace \sum_{i=0}\^n i\^2 = \frac{(n\^2+n)(2n+1)}{6}\rbrace\tag{3.1.7-1} $$

$$ \left\lbrace \sum_{i=0}\^n i\^2 = \frac{(n\^2+n)(2n+1)}{6} \right\rbrace \tag{3.1.7-2} \label{3.1.7-2} $$

$ \eqref{3.1.7-2} $ 写作 `$$ \left\lbrace \sum_{i=0}^n i^2 = \frac{(n^2+n)(2n+1)}{6} \right\rbrace $$`。

`\left` 和 `\right` 适用于下列几种符号：
`(` `)`, `[` `]`, `\{` `\}`, `|`, `\vert`, `\Vert`, `\langle` `\rangle`, `\lceil` `\rceil`, `\lfloor` `\rfloor`。



### 上标、下标

上标 `^`，下标 `_`，例如 $ x_i\^2 $ 写作 `x_i^2`，$ \log_2 x $ 写作 `\log_2 x`。

默认情况下，上下标符号仅对下一个组起作用。一个组即单个字符或者使用 `{…}` 包裹起来的内容。也就是说，如果使用 `10^10`，会得到 $ 10\^10 $，而 `10^{10}` 才是 $ 10\^{10} $。同时，大括号还能消除二义性，如 `x_i^2` 得到 $ x_i\^2 $，而 `x_{i^2}` 得到 $ x_{i\^2} $ ，再如 `{x^5}^6` 得到 $ {x\^5}\^6 $，而 `x^{5^6}` 得到 $ x\^{5\^6} $。



### 分式

第一种，使用 `\frac xy`，`\frac` 作用于其后的两个组（即此处的x和y），结果为 $ \frac xy $。<br/>
如果分子或分母不是单个字符，请使用 `{…}` 来分组，如 `\frac{a+1}{b+1}` 得到 $ \frac{a+1}{b+1} $。

第二种，使用 `\over` 来分隔一个组的前后两部分，如 `{a+1 \over b+1}` 将得到 $ {a+1 \over b+1} $。



### 根式

使用 `\sqrt` 来表示。如 ` (\sqrt{x})' = \frac{1}{2\sqrt{x}} ` 得到 $ (\sqrt{x})' = \frac{1}{2\sqrt{x}} $。



### 三角函数

请尽量使用公式语法： `\sin`，`\cos`，`\arcsin`，`\arccos`，`\tan`，`\cot`，`\arctan`。

$$ \sin\theta+\sin\varphi= 2\sin[(\theta+\varphi)/2]cos[(\theta-\varphi)/2]  $$

对比直接用 HTML 输出的结果，sinθ+sinφ = 2 sin[(θ+φ)/2] cos[(θ-φ)/2]。



### 极限与导数

"\lim"，"\ln"，
如 `\lim_{x \to 0}` 表示 $ \lim_{x \to 0} $。

虽然可以直接用罗马字母表示，但还请尽量使用公式语法。



### 求和与积分

`\sum` 用来表示求和符号 $\sum$，其下标表示求和下限，上标表示上限。
如 `\sum_1^n` 得到 $ \sum_1\^n $。

`\int` 用来表示积分符号 $\int$，同样地，其上下标表示积分的上下限。
如 `\int_{-\infty}^\infty e^{-\pi x^2}\,\mathrm{d}x=1` 得到 $ \int_{-\infty}\^\infty e\^{-\pi x\^2}\,\mathrm{d}x=1 $。

与此类似的符号还有，`\iint` 表示 $\iint$，`\prod` 表示 $\prod$，`\bigcup` 表示 $\bigcup$，`\bigcap` 表示 $\bigcap$。



### 比较运算符

`\lt`，`\gt` 分别表示 $\lt$ 和 $\gt$，<br/>
`\le`，`\ge` 分别表示 $\le$ 和 $\ge$，<br/>
`\neq` 表示 $ \neq$。

可以在这些运算符前面加上 `\not`，如 `\not\lt` 得到 $\not\lt$。



### ？？？
`\approx`
`\sim`
`\cong`
`\equiv`
`\prec`



### 模运算

`\pmod`。如，`a\equiv b\pmod n` 表示 $ a\equiv b\pmod n $。



### 逻辑运算符

`\land`，`\lor`，`\lnot` 分别表示 $\land$，$\lor$，$\lnot$， <br/>
`\forall`，$\forall$， <br/>
`\exists`，$\exists$， <br/>
`\top`，$\top$，`\bot`，$\bot$， <br/>
`\vdash`，$\vdash$，`\vDash`，$\vDash$



### 集合关系与运算

`\cup`，$\cup$，`\cap`，$\cap$， <br/>
`\setminus`，$\setminus$， <br/>
`\supset`，$\supset$，`\subset`，$\subset$，`\subseteq`，$\subseteq$，`\subsetneq`，$\subsetneq$， <br/>
`\in`，$\in$，`\notin`，$\notin$， <br/>
`\emptyset`，$\emptyset$，`\varnothing`，$\varnothing$



### 表示排列

使用 `{n+1 \choose 2k}` 或 `\binom{n+1}{2k}`，得到 $ \binom{n+1}{2k} $。



### 箭头

`\to`，$\to$， <br/>
`\rightarrow`，$\rightarrow$，`\leftarrow`，$\leftarrow$， <br/>
`\Rightarrow`，$\Rightarrow$，`\Leftarrow`，$\Leftarrow$， <br/>
`\mapsto`，$\mapsto$



### 点

`\ldots` 与 `\cdots`，其区别是dots的位置不同，
`\ldots` 位置稍低，$ a_1+a_2+\cdots+a_n $，
`\cdots` 位置居中，$ a_1, a_2, \ldots ,a_n $。



### 星

`\star`，$\star$，`\ast`，$\ast$，`\oplus`，$\oplus$，`\circ`，$\circ$，`\bullet`，$\bullet$



### 一些希腊字母的变体形式

|   名称   |   大写   |   语法   |   小写   |   语法   |   含义   |
| :------  | :------ | :------- | :------ | :------- | :------ |
| alpha   | $ A $        | A        | $ \alpha $      | \alpha      | |
| beta    | $ B $        | B        | $ \beta $       | \beta       | |
| gamma   | $ \Gamma $   | \Gamma   | $ \gamma $      | \gamma      | |
| delta   | $ \Delta $   | \Delta   | $ \delta $      | \delta      | |
| epsilon | $ E $        | E        | $ \epsilon $    | \epsilon    | |
|         |              |          | $ \varepsilon $ | \varepsilon | |
| zeta    | $ Z $        | Z        | $ \zeta $       | \zeta       | |
| eta     | $ H $        | H        | $ \eta $        | \eta        | |
| theta   | $ \Theta $   | \Theta   | $ \theta $      | \theta      | |
|         |              |          | $ \vartheta $   | \vartheta   | |
| iota    | $ I $        | I        | $ \iota $       | \iota       | |
| kappa   | $ K $        | K        | $ \kappa $      | \kappa      | |
|         |              |          | $ \varkappa $   | \varkappa   | |
| lambda  | $ \Lambda $  | \Lambda  | $ \lambda $     | \lambda     | |
| mu      | $ M $        | M        | $ \mu $         | \mu         | |
| nu      | $ N $        | N        | $ \nu $         | \nu         | |
| xi      | $ \Xi $      | \Xi      | $ \xi $         | \xi         | |
| omicron | $ O $        | O        | $ \omicron $    | \omicron    | |
| pi      | $ \Pi $      | \Pi      | $ \pi $         | \pi         | |
|         |              |          | $ \varpi $      | \varpi      | |
| rho     | $ P $        | P        | $ \rho $        | \rho        | |
|         |              |          | $ \varrho $     | \varrho     | |
| sigma   | $ \Sigma $   | \Sigma   | $ \sigma $      | \sigma      | |
|         |              |          | $ \varsigma $   | \varsigma   | |
| tau     | $ T $        | T        | $ \tau $        | \tau        | |
| upsilon | $ \Upsilon $ | \Upsilon | $ \upsilon $    | \upsilon    | |
| phi     | $ \Phi $     | \Phi     | $ \phi $        | \phi        | |
|         |              |          | $ \varphi $     | \varphi     | |
| chi     | $ X $        | X        | $ \chi $        | \chi        | |
| psi     | $ \Psi $     | \Psi     | $ \psi $        | \psi        | |
| omega   | $ \Omega $   | \Omega   | $ \omega $      | \omega      | |



### 顶部符号

对于单字符，使用 `\hat`，如，`\hat x` 得到 $ \hat x $。
多字符可以使用 `\widehat`，如，`\widehat{xy}` 得到 $\widehat{xy}$。
你是不是想看看在`\widehat`里放很多字是什么感觉，是这样的： $\widehat{abcdefghijklmn}$。

类似的，还有 `\bar x`，$ \bar x $，`\overline{xyz}`，$\overline{xyz}$; <br/>
`\vec x`，$ \vec x $，`\overrightarrow{xy}`，$\overrightarrow{xy}$，`\overleftrightarrow{xy}`，$\overleftrightarrow{xy}$; <br/>
以及在 $ \frac d{dx} x \dot x = \dot x\^2 + x \ddot x $ 中用到的 `\dot`和`\ddot`。


### 更多的特殊符号
可以在 [this shorter listing](http://pic.plover.com/MISC/symbols.pdf) 或
[this exhaustive listing](http://mirror.math.ku.edu/tex-archive/info/symbols/comprehensive/symbols-a4.pdf) 找到。










本站的 MathJax 配置
-----------

{% highlight js %}
<script type="text/x-mathjax-config">
  MathJax.Hub.Config({
    // skipStartupTypeset: true,
    // showMathMenu: false,
    showProcessingMessages: false,
    messageStyle: "none",
    extensions: ["tex2jax.js"],
    TeX: { equationNumbers: { autoNumber: "AMS" } },
    jax: ["input/TeX", "output/HTML-CSS"],
    tex2jax: {
      inlineMath: [ ["$","$"], ["\\(","\\)"] ],
      displayMath: [ ["$$","$$"], ["\\[","\\]"] ],
      preview : "none",
      processEscapes: true,
      skipTags: ["script", "noscript", "style", "textarea", "pre", "code"]
    },
    "HTML-CSS": {
      preferredFont: "TeX",
      availableFonts: ["STIX","TeX"],
      scale: 100,
      minScaleAdjust: 75,
      matchFontHeight: true,
      styles: {
        ".MathJax": {
          "font-size": "1.181em",
          "outline": "none",
          "top": "-0.055554em"
        }
      }
    }
  });
</script>
<script type="text/javascript"
  src="http://cdn.mathjax.org/mathjax/latest/MathJax.js?config=TeX-AMS-MML_HTMLorMML">
</script>
{% endhighlight %}





参考文章
-----------

[MathJax basic tutorial and quick reference | Mathematics Stack Exchange](http://meta.math.stackexchange.com/questions/5020/mathjax-basic-tutorial-and-quick-reference/5044)


