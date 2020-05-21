# 将footer固定在下面: Flexbox vs Grid

> 原文地址: 《[From Node to Deno](https://aralroca.com/blog/from-node-to-deno)》

多年来，我不断地参考 Matthew James Taylor 的这篇[文章](https://matthewjamestaylor.com/bottom-footer)，寻找一种方法，无论页面内容的长度如何，都能让网页`footer`保持在页面底部。这个方法依赖于设置一个footer高度，虽然不能扩展，但在 BF（Flexbox之前）是一个非常好的解决方案。

如果你主要处理SPA开发，你可能会依然被这个问题困扰，但是它依然存在于：

[//]: <If you mostly deal with SPA development, you may be confused about why this problem is still around, but it's still a possibility to find your footer floating up for:>

- 登录页
- 博客/新闻文章（无广告...）
- 中间页面 例如确认弹层
- 产品列表
- 日历详情

现代CSS有两种处理方法：`flexbox`和 `grid`。

下面是两种方法的 `demo`, 默认为`flexbox`的方法。
也可以把`$method`变量换成`grid`, 在`Codepen`中查看完整的示例

codePen:

[https://codepen.io/5t3ph/pen/abvboxz](https://codepen.io/5t3ph/pen/abvboxz)

## Flexbox 方法

通过以下方式实现

```css
body {
  min-height: 100vh;
  display: flex;
  flex-direction: column;
}

footer {
  margin-top: auto;
}

// Optional
main {
  margin: 0 auto;
  // or: align-self: center
  max-width: 80ch;
}
```

### 如何实现

首先，我们确保`body`元素将至少拉伸到屏幕的全部高度，`min-height: 100vh`,如果内容很短，这将不会触发溢出（例外：某些手机浏览器），它将允许内容根据需要继续拉伸高度。

通过设置 `flex-direction: column`保留块元素,（假定`body`子元素实际上都是块元素）来保持正常文档流的行为。

`flexbox`的优势在于利用`margin: auto`填充它在相应方向上最近的同级中的空间,设置`margin-top: auto`有效地将`footer`推到屏幕的底部。

### Gotcha

在`flexbox`方法中，我在`main`上添加了一个`outline`，`main`元素未填充高度，这就是为什么我们必须使用margin-top: auto技巧。这可能对你来说并不重要，但如果有的话，请看`grid`方法，它可以拉伸`main`来填充可用的空间。

## Grid 方法

通过以下方式实现

```css
body {
  min-height: 100vh;
  display: grid;
  grid-template-rows: auto 1fr auto;
}

// Optional
main {
  margin: 0 auto;
  max-width: 80ch;
}
```

### 如何实现

我们保留 `min-height: 100vh`这个方法，后面我们使用
`grid-template-rows`来划分空间。

我们使用特殊`grid`单位`fr`，`fr` 是一个‘分数’，它是浏览器计算出可分配给该列或该行的可用空间的大小，这样它可以自动填充`head`和`footer`之间的所有可用空间，这也就解决了flexbox方法中的问题。

## 哪一个更好

在看到`grid`之后，你可能会有一瞬间的想法，觉得它显然更有优势。然而，如果在`head`和`footer`之间添加了更多元素，则需要更新模板(或者确保始终存在包装元素（如div）以不影响任何嵌套语义/层次结构)

另一方面，`flexbox`方法可以在不同的模板中使用，在中间部分有多个块元素的情况下，例如，在存档页中使用一系列的`<article>`元素，而不是单一的`<main>`。

所以，就像所有的技术一样，这取决于项目的不同：) 
