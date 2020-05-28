# CSS按钮造型指南

> 原文地址: 《[CSS Button Styling Guide](https://moderncss.dev/css-button-styling-guide/)》

> 我从事前端开发13年有余，一直致力于研究现代CSS解决方案，这是我该系列文章的第九篇

本指南将探讨如何为链接和按钮元素设计一个可访问、可扩展的按钮外观。

涵盖的主题有：

- 重置`a`和`button`的样式

- `display`, `visual`, `size`, 和`text`的样式

- 可访问的样式注意事项

- 常见场景的扩展样式

***

在`border-radius`、`box-shadow`和渐变出现之前，我一直在和按钮做斗争。

但是现在我们把按钮的造型设计做得太过分了，而且在某写地方完全忽略了按钮的真正含义，更不用说是一个可访问的按钮或链接了。

> 《[Links vs. Buttons in Modern Web Applications](https://marcysutton.com/links-vs-buttons-in-modern-web-applications)》这篇优秀的文章让我了解了什么时候使用链接与按钮是合适的。

我们将研究一下，在视觉上创建一个按钮外观的链接和按钮都需要哪些属性，以及为确保创建和使用无障碍的按钮所需的额外细节。

***

## 重置默认样式

基础样式--在Chrome浏览器中渲染的原生浏览器样式，目前唯一的变化是链接继承了主体上的自定义字体集，字体大小也做了调整。

![](https://dev-to-uploads.s3.amazonaws.com/i/1b5duijnf8zdydz1ue1p.png)

html:

```html
<a href="javascript:;">Button Link</a>
<button type="button">Real Button</button>
```

我使用了`javascript:;`字符串作为`href`值，这样我们就可以在不触发导航的情况下测试状态。同样，由于这个按钮不是用于表单提交，所以需要显式的按钮类型来防止触发get请求和页面重载。

## 重置样式

> 注：通常情况下，我将Normalize重置应用于CodePens，但这节课我们要从头开始，学习按钮和链接的重置。使用Normalize或其他流行的复位方法可以为你做一些这样的事情。

首先，我们在链接和按钮中加入按钮的类，只是为了强调本课的样式应用在哪里。

```html
<a href="javascript:;" class="button">Button Link</a>
<button type="button" class="button">Real Button</button>
```

## box-sizing

确保你的样式包括以下重置 - 如果你不想全局重置，可以将其范围设置为按钮类。

```scss
* {
  box-sizing: border-box;
}
```

简而言之，这个规则可以防止像border和padding这样的东西扩大预期的元素大小。

## 链接

对于链接，我们只需要做一个重置就可以。

```scss
a.button {
  text-decoration: none;
}
```

这就简单地去掉了下划线。

## 按钮

接下来，我们还有一些需要重置按钮的规则:

```scss
button.button {
  border: none;
  background-color: transparent;
  font-family: inherit;
  padding: 0;
  cursor: pointer;

  @media screen and (-ms-high-contrast: active) {
    border: 2px solid currentcolor;
  }
}
```

浏览器之间显示的值也有一些差异，不过我们很快就会把它改成独特的选项。

通过这些重置样式，我们现在有了这样的外观:

![](https://dev-to-uploads.s3.amazonaws.com/i/44rie4nuqfk5jwpkk6ff.png)

## display样式

我发现在许多场景中效果最好的是`display: inline-flex`，它给了我们flexbox的内容对齐能力，但它存在于`in-line-block`行为中的DOM中。

```scss
a.button,
button.button {
  display: inline-flex;
  align-items: center;
  justify-content: center;
}
```

如果你在未来添加图标，或者对宽度进行限制，Flex对齐就会派上用场。

## 视觉样式

接下来我们将应用一些标准的视觉样式，你也可以根据自己的喜好来调整。这是最灵活的一组样式，可以省去`box-shadow`和/或`border-radius`。

```scss
$btnColor: #3e68ff;
 
a.button,
button.button {
  // ... existing styles
  background-color: $btnColor;
  color: #fff;
  border-radius: 8px;
  box-shadow: 0 3px 5px rgba(0, 0, 0, 0.18);
}
```

现在链接和按钮变得更像了:

![](https://dev-to-uploads.s3.amazonaws.com/i/ctowxoi9kaub1bm96bz8.png)

## 按钮对比度

在创建初始按钮样式时，涉及到两个层次的对比:

1. 按钮的背景颜色和它所显示的背景之间至少有3:1。

2. 按钮文字与按钮背景之间至少有4.5:1(小于18px或16px黑体字的文字)或3:1(大于这些措施的文字)。

假设页面背景为白色，则按钮颜色选择为4.54：1。

## 尺寸

我们故意遗漏了 "视觉 "分类下的一个属性，在看到进度截图时，你可能会忽略了这个属性：`padding`。

因为`padding`是`盒模型`的一部分，所以我们把它留到了尺寸部分。

让我们应用大小值，然后讨论一下：

```scss
a.button,
button.button {
  // ... existing styles
  padding: 0.25em 0.75em;
  min-width: 10ch;
  min-height: 44px;
}
```

我们使用`em`为单位应用`padding`，这是一种偏好，它允许padding与应用的字体大小成比例调整。

接下来，我们使用`ch`为单位设置一个最小宽度，这个最小宽度大致等于所应用的字体和字体大小的0字符的宽度。这个建议是一个视觉上的节奏护栏。考虑一个场景，你有两个并排的按钮，有一个短的和一个长的标签，比如 "分享 "和 "了解更多"。如果没有最小宽度，"分享 "按钮会比 "了解更多 "短。

最小高度是基于确保按钮在触摸设备上的目标足够大，以满足WCAG 2.1成功标准的[2.5.5 - Target Size](https://www.w3.org/WAI/WCAG21/Understanding/target-size.html)。

越来越相似了，但我们还没有完成。

![](https://dev-to-uploads.s3.amazonaws.com/i/uylx2jbc92mr1bbdksoo.png)

## 文本样式

根据上次的进度截图，你可能会想跳过文本样式。

但看看当我们缩小窗口大小并触发响应行为时，会发生什么:

![](https://dev-to-uploads.s3.amazonaws.com/i/s6rmhllldvvrw5wsgzhy.png)

可以看到，对齐方式不同了，并且行高也可以调整。

我有意跳过了在重置样式中修复文本对齐的问题，因此我们现在要确保这两种样式都居中。然后，我们还可以降低行高--这可能需要根据使用的字体来调整。

```scss
a.button,
button.button {
  // ... existing styles
  text-align: center;
  line-height: 1.1;
}
```

可以，看起来不错！

![](https://dev-to-uploads.s3.amazonaws.com/i/4ub4z1jxoxk3cp8gzg4a.png)

## State Styles

现在，当用户在尝试与按钮进行交互时，唯一的视觉反馈就是光标变化为 "指针 "的变化。

有三种状态是我们需要确保的。

## :hover

平时最受关注的就是`hove`r，所以我们就从它开始。

鼠标悬停的典型效果就是改变背景颜色。由于我们相当接近4.5，所以我们会想把颜色变暗。

我们可以利用Sass的优势，使用我们在 "视觉 "部分定义的`$btnColor`变量来为我们计算这个颜色。

```scss
a.button,
button.button {
  // ... existing styles
  &:hover {
    background-color: scale-color($btnColor, $lightness: -20%);
  }
}
```

这个效果有点刺耳，但我们有另一个现代CSS工具来简化这个效果，恰当地命名为 `transition`。`transition`需要添加到悬停规则之外，这样它就可以同时适用于 "over "和 "out"。

```scss
a.button,
button.button {
  // ... existing styles
  
  transition: 220ms all ease-in-out;
  
  // ...&:hover
}
```

![](https://dev-to-uploads.s3.amazonaws.com/i/0416wpo396xq4tbc5ln2.gif)

## :focus

对于键盘用户，我们需要确保可以清楚地区分焦点状态。

默认情况下，浏览器对获得焦点的元素应用某种“光晕”效果。 一个不好的做法是简单地删除显示该效果的`outline`属性，而不替换它。

我们将用一个使用`box-shadow`的自定义焦点状态来代替轮廓。和 `outline` 一样，`box-shadow `不会改变整体元素的大小，所以不会导致布局偏移。而且，由于我们已经应用了一个`transition`，所以`box-shadow`也会继承使用，以获得引人注目的效果。

```scss
a.button,
button.button {
  // ... existing styles
  
  // ...&:hover

  &:focus {
    outline-style: solid;
    outline-color: transparent;
    box-shadow: 0 0 0 4px scale-color($btnColor, $lightness: -40%);
  }
}
```

我们再一次使用了`scale-color`功能，这一次要比悬停的颜色更深一点。这是因为一个按钮可以同时处于悬停和聚焦状态。

![](https://dev-to-uploads.s3.amazonaws.com/i/03cq806lheglhyn5xf6l.gif)

## :active

最后，特别是对于 "真正的按钮"，最好定义一个`:active`状态样式。

对于链接，在单击/点击的“按下”期间会短暂显示。

对于按钮来说，可以显示更长的时间，因为可以用空格键触发按钮，而空格键可以无限按住。

我们将把`:active`附加到现有的`:hover`样式中:

```
&:hover,
&:active {
  background-color: scale-color($btnColor, $lightness: -20%);
}
```

## 样式变化

（"幽灵"）按钮这个话题另当别论，还有两种变化，我们将快速补充。

## 小型按钮

使用BEM格式，我们要创建`button--small`，简单地缩小字体大小。由于我们将padding设置为`em`，这将按比例调整大小。而我们的最小高度将确保按钮保持足够大的触摸目标。

```scss
&--small {
  font-size: 1.15rem;
}
```

## 块状按钮

有的时候，你可能会想要`块`而不是内联，所以我们将添加宽度100%来允许这个选项，而不是改变显示属性，因为我们仍然想要按钮内容的弹性对齐：

```scss
&--block {
  width: 100%;
}
```

## 陷阱：Flex Columns的子项

考虑到按钮是flex列的子列，当按钮扩展到全宽时，即使没有`button--block`，你可能会措手不及。

为了防止这种情况的发生，你可以在基本按钮样式中添加`align-self: start`，或者为flex/grid对齐属性值中的:`start, center,  end`创建实用样式。

## 示例

https://codepen.io/5t3ph/embed/preview/rNOpgPa?height=265&amp;theme-id=default&amp;default-tab=result&amp;user=5t3ph&amp;slug-hash=rNOpgPa&amp;preview=true&amp;name=cp_embed_1