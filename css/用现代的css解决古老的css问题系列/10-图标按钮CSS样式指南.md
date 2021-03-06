# 图标按钮 CSS 样式指南

> 原文地址: 《[Icon Button CSS Styling Guide](https://moderncss.dev/icon-button-css-styling-guide/)》

> 我从事前端开发 13 年有余，一直致力于研究现代 CSS 解决方案，这是我该系列文章的第十篇

本指南将在上一集 "[CSS 按钮样式指南](https://moderncss.dev/css-button-styling-guide/)"的基础上，探讨图标按钮的使用案例。我们将涵盖图标+文本以及仅有图标的按钮。

---

> 注：现在 SVG 已经有了很好的支持，在图标系统与图标字体相比，首选 SVG。我们不会专门深入研究 SVG，但我们会假设使用 SVG 图标。

## 图标+文字按钮

首先，让我们从目前的按钮中做一个比较简单的扩展，在文本旁边加一个 svg 图标。

```html
<a href="javascript:;" class="button">
  <svg class="button__icon" xmlns="http://www.w3.org/2000/svg" viewBox="0 0 32 32" aria-hidden="true" focusable="false">
    <path
      d="M32 12.408l-11.056-1.607-4.944-10.018-4.944 10.018-11.056 1.607 8 7.798-1.889 11.011 9.889-5.199 9.889 5.199-1.889-11.011 8-7.798z"
    ></path>
  </svg>
  Button Link
</a>
```

关于 SVG 图标+文本的使用案例有 3 个关键特征。

1. 使用一个新的类---`button__icon`

2. `viewBox`值与图标边界紧密相关，最好是一个正方形，以获得最佳的效果，即使数值有差异（如 24 与 32）。

3. 对于可访问性，我们推荐用：

- `aria-hidden="true"` - 允许辅助技术跳过 SVG，因为它是装饰性的，并且不提供可见按钮文本的任何语义值

- `focusable="false"` - 防止在某些版本的 IE 浏览器中出现"double focus"事件。

> 关于图标按钮可访问性的更多信息。请阅读 Sara Soueidan 的《[this excellent article](https://www.sarasoueidan.com/blog/accessible-icon-buttons/)》这篇文章，她是可访问性和 SVG 方面的专家。

## 图标+文字的图标样式

由于在`.button`上应用了`display: inline-flex`,而SVG上没有`width`属性，所以默认情况下，图标是看不到的。

因此，我们首先使用`em`单位将尺寸添加到新的`.button__icon`类中，使它与使用的`font-size`保持相对:

```scss
.button__icon {
  // 你可能希望你的图标看起来更大一些
  // 或相对于文本而言，更小
  width: 0.9em;
  height: 0.9em;
}
```

![](https://dev-to-uploads.s3.amazonaws.com/i/00g7uw9dfcb80pq2hikz.png)

根据[规范的默认值](https://developer.mozilla.org/en-US/docs/Web/SVG/Attribute/fill#path)，包括路径在内的SVG部分有一个黑色的填充。为了调整这一点，我们将使用特殊的关键字`currentColor`，它将把按钮应用的文字颜色扩展到SVG中。

```scss
.button__icon {
  // ...existing styles
  fill: currentColor;
}
```

![fill](https://dev-to-uploads.s3.amazonaws.com/i/0rs7lk1bmq6hqkcggekq.png)

最后需要调整的是在图标和按钮文字之间增加一点间距，我们将再次使用em单元进行调整。

```scss
.button__icon {
  // ...existing styles
  margin-right: 0.5em;
}

```

![button margin style](https://dev-to-uploads.s3.amazonaws.com/i/niqz77ol4aaskwjic6dw.png)

我们需要添加一个实用程序类，让图标放在文字之后，或者是按钮的 "末端"（对于从右到左的语言）。我们将现有的边距清零，并将其翻转到左边。

```scss
.button__icon {
  // ... existing styles
  
  &--end {
    margin-right: 0;
    margin-left: 0.5em;
  }
}
```

```html
<a href="javascript:;" class="button">
  Button Link
  <svg class="button__icon button__icon--end" xmlns="http://www.w3.org/2000/svg" 
viewBox="0 0 32 32" aria-hidden="true" focusable="false">
    <path d="M32 12.408l-11.056-1.607-4.944-10.018-4.944 10.018-11.056 1.607 8 7.798-1.889 11.011 9.889-5.199 9.889 5.199-1.889-11.011 8-7.798z"></path>
  </svg>
</a>
```

![](https://dev-to-uploads.s3.amazonaws.com/i/xj30apl4rbcnzs1vjs8r.png)

## 仅有图标的按钮

我们要做的假设是，除了仅有图标的按钮外，我们还需要普通按钮（有或没有图标）。这很重要，因为我们将在一个新的类之外重用.button类，这样我们就不需要重新定义重置和基本视觉样式。覆盖是最小的。

```html
<a href="javascript:;" class="button icon-button" aria-label="Icon-only Button">
  <svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 32 32" 
aria-hidden="true" class="icon-button__icon" aria-hidden="true" focusable="false">
    <path d="M32 12.408l-11.056-1.607-4.944-10.018-4.944 10.018-11.056 1.607 8 7.798-1.889 11.011 9.889-5.199 9.889 5.199-1.889-11.011 8-7.798z"></path>
  </svg>
</a>
```

从图标+文字按钮的变化:

1. 在`a`标签中增加了图标按钮类。

2. 增加了一个`ria-label="Icon-only Button"`，因为我们删除了视觉文本，所以提供了一个无障碍的文本选择。

3. 将SVG上的类调换为`icon-button__icon`。

> 重要的是：`ria-label`的值应该描述按钮的作用，而不是图标的作用。关于更多的阅读和其他提供文本替代的方法，请参阅 [Sara Soueidan的文章](https://www.sarasoueidan.com/blog/accessible-icon-buttons/)

下面是调整前的内容--一个看起来空荡荡的按钮，因为我们又回到了没有宽度的问题。

![](https://dev-to-uploads.s3.amazonaws.com/i/09pnf9xm2pectdy9ug6j.png)

首先，让我们创建一个新的类。由于CSS中的 "C"，这个规则需要放在.button规则之后。

```scss
.icon-button {
  width: 2.5rem;
  height: 2.5rem;
  border-radius: 50%;
  padding: 0.35em;

  &__icon {
    width: 100%;
    height: 100%;
    fill: currentColor;
  }
}
```

我们定义了一个新的宽度和高度，完全可以根据您的设计要求进行调整，但它应该相当于一个正方形。这样，当边框半径为50%时，可以创建一个 "圆 "的外观。

然后，我们在SVG图标和按钮边界之间添加一些填充物（同样是根据你的口味/设计要求），以增加一些呼吸空间。

接下来，我们定义我们的`icon-button__icon`类。这里的区别在于我们希望宽度和高度与容器的宽度和高度一致，所以我们将其设置为100%。这样，我们只需要在`.icon-button`类上重新定义字体大小属性，就可以扩展到多个尺寸的图标按钮。

进展情况是这样的:

![](https://dev-to-uploads.s3.amazonaws.com/i/tdnn9ug4rcpmn45czqb1.png)

这不是我们想要的，但我们可以通过调整.button类中的以下属性来解决这个问题。我们将使用:not()选择器来排除仅用于普通按钮的属性。

```scss
.button {
  // ...existing styles

  // 找到这些样式并更新，不要重复。
  &:not(.icon-button) {
    padding: 0.25em 0.75em;
    min-width: 10ch;
    min-height: 44px;
  }
}
```

现在我们有了我们想要的：

![](https://dev-to-uploads.s3.amazonaws.com/i/aqknn8adugm9vn63c091.png)

## demo

包括使用上一集中创建的`.button--small`类，以及一个 "真正的按钮 "来验证样式对这两个元素的作用。

https://codepen.io/5t3ph/embed/preview/ExVpVJa?height=265&amp;theme-id=default&amp;default-tab=result&amp;user=5t3ph&amp;slug-hash=ExVpVJa&amp;preview=true&amp;name=cp_embed_1