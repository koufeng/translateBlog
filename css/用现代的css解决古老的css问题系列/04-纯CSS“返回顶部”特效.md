> 原文地址: 《[Pure CSS Smooth-Scroll "Back to Top"](https://moderncss.dev/pure-css-smooth-scroll-back-to-top/)》

"Back to top"链接现在可能不经常使用，但有两个现代CSS技术很好地展示了它的特点。

- position: sticky
- scroll-behavior: smooth

2011年在[Web Designer Wall](https://web.archive.org/web/20110413163553/https://webdesignerwall.com/tutorials/animated-scroll-to-top)利用`jQuery`实现 "back to top" 效果

它的想法是为用户提供一个 "跳转链接"，让用户可以滚动回到网站的顶部，在以前的博客上经常使用。

下面我们就来学习一下如何实现。

![top](https://tva1.sinaimg.cn/large/007S8ZIlly1gf1kea82lbg30jz05nu0c.gif)

## 关于 position: sticky

这个新的 `position`属性值在[caniuse](https://caniuse.com/#search=position%3A%20sticky)上描述如下：

> 保持元素的`position`为 "fixed"或 "relative"，这取决于它在视口中的显示方式,
在元素滚动时表现为`fixed`

`caniuse data`的另一个重要注意事项是，你需要提供它的前缀以获得最好的支持, 我们在`Back to top`时不是十分优雅的实现`position: fixed`的效果

## 关于scroll-behavior: smooth

这是一个非常新的属性，并且[支持相对较低](https://caniuse.com/#search=scroll-behavior)。这个确切的定义要求滚动行为，特别是在选择锚点链接时，滚动行为会有一个平滑的动画外观，而不是默认的、更突兀的即时跳转。

使用它提供了“渐进式增强”，这意味着对于那些浏览器支持它的人来说，它将是一个更好的体验，但对于不支持它的浏览器来说，它也可以工作。

令人惊讶的是，`Safari`在支持这一点上落后了，但其他主要的浏览器都支持。

## 基本 html 结构

html 内容：

```html
<header id="top">Title</header>
<main>
  <article>
    <!-- long form content here -->
  </article>
  <!-- Back to Top link -->
  <div class="back-to-top-wrapper">
    <a href="#top" class="back-to-top-link" aria-label="Scroll to Top">🔝</a>
  </div>
</main>
```

我们把链接(#top)放在`article`之后，但在`main`内。它不是具体的`article`的一部分，我们也希望它在焦点顺序中排在最后。

我们还可以在`<header>`中添加`id="top"`，并使用该锚点作为回顶部链接的`href`值。如果你只想滚动到`<main>`的顶部，当然你也可以将这个`id`放在顶部其他位置

## 添加 smooth-scrolling

第一步很简单，添加下面的css： 

```less
// Smooth scrolling IF user doesn't have a preference due to motion sensitivities
@media screen and (prefers-reduced-motion: no-preference) {
  html,
  body {
    scroll-behavior: smooth;
  }
}
```

原来的解决方案没有考虑到`prefers-reduced-motion`的问题。

之前，我有一篇`CSS-Tricks`的文章，是用`jQuery`和`vanilla JS`完成的。这篇文章已经有一段时间了，感谢那个团队在有新解决方法的时候不断地更新这样的文章，👍

我发现了一些奇怪的地方，比如当你访问一个在URL中包含锚点的页面时，它仍然会执行平滑滚动，这对你的场景可能并不可取。

## "Back to Top" 锚点 样式

在实现之前，让我们先给这个链接做一些基本的样式。为了好玩，我用了一个`emoji`，但你可以换成SVG图标。

```less
.back-to-top-link {
  display: inline-block;
  text-decoration: none;
  font-size: 2rem;
  line-height: 3rem;
  text-align: center;
  width: 3rem;
  height: 3rem;
  border-radius: 50%;
  background-color: #D6E3F0;
  // emoji don't behave like regular fonts
  // so this helped position it correctly
  padding: 0.25rem;
}
```

上面的样式只实现了一个非常基本的圆形按钮。在完整的`Codepen`中，我添加了额外的更棒样式和 `:hover` 和 `:focus` 样式，但这些并不是必要的。

接下来，你可能会想知道为什么我们要为这个锚点外添加一层。原因是我们需要用它来为链接创建一个 "scroll track"。以使链接可以存在于其中。

`position: sticky` 会根据`element`在`dom`中的位置呈现`position`属性，然后会根据元素`top`的值，展示在窗口中，然而，我们把链接放在文档的最后，所以如果没有一些帮助，它基本上不会被选中。

我们将使用 外层元素`.back-to-top-wrapper`和 `position: absolute` 属性来改变`link`位置 ，值得庆幸的是，浏览器使用此视觉调整后的位置（也就是进入视口时）来计算何时“粘贴”链接。 

外层元素`.back-to-top-wrapper` 样式

```less
// 滚动条距离<main>多远出现 “link”

$scrollLength: 100vh;

.back-to-top-wrapper {
  // 轨迹
  // outline: 1px solid red;
  position: absolute;
  top: $scrollLength;
  right: 0.25rem;
  bottom: -5em;
  // 需要在不支持 "sticky "的浏览器中获得最佳支持。
  width: 3em;
  // 禁用默认鼠标事件
  pointer-events: none;
}
```

`pointer-events: none;`这个个定义可能对你来说也很陌生，这本质上是让交互事件，比如点击等交互事件在元素中失效，这样就会阻止一些默认事件发生，比如`a`标签跳转。

有了这些，我们现在可以看到链接在最初的视口内容下面的内容上有一点重叠。让我们在`<main>`中添加一些样式来防止这种重叠，并添加`position:relative`， 这对于外层的元素`absolute`属性是必须的。

``` less
main {
  // 为轨迹留一些空间
  padding: 0 3rem;
  position: relative;
  max-width: 50rem;
  margin: 2rem auto;

  // 可选，如果最后一个元素是块项，则清除margin。 
  *:last-child {
    margin-bottom: 0;
  }
}
```

`.back-to-top-link` 核心`link`样式

```less
.back-to-top-link {
  // `sticky` 不生效时 `fixed` 备选
  position: fixed;
  //  首选position属性，兼容需要前缀支持，在Safari上不支持。
  // @link https://caniuse.com/#search=position%3A%20sticky
  position: sticky;
  // 恢复默认事件
  pointer-events: all;
  // “sticky” 生效时距窗口顶部距离，如果是“fixed”则始终是相对窗口顶部距离
  top: calc(100vh - 5rem);
}
```

在浏览器支持`sticky`属性时会出现最理想的效果，否则`position: fixed;`生效，"Back to Top" 会一直存在窗口中

注意，我们还用` pointer-events: all;`还原了指针事件，这样，与链接的交互实际上是可以工作的。

codepen：

[https://codepen.io/5t3ph/pen/OJyyqWR](https://codepen.io/5t3ph/pen/OJyyqWR)

## 已知问题

在有简短内容场景中，会有一些问题，你可能会想利用`overflow: hidden`属性来解决，但不幸的是，这使得`position: sticky`无法完全工作☹️，所以在真实的开发中你应该区分出这种情况，或者在模板中注入文章之前执行一个计算来确定文章是否满足长度要求。
