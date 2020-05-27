# 实现元素等高: Flexbox vs. Grid

> 原文地址: 《[Equal Height Elements: Flexbox vs. Grid](https://moderncss.dev/equal-height-elements-flexbox-vs-grid/)》
 
> 我从事前端开发13年有余，一直致力于研究现代CSS解决方案，这是我该系列文章的第2篇

很久以前（大约7年前），我写了一个jQuery插件来计算等高列的效果。它确保了在一个非常特殊的情况下，无论内容的长度如何，都能保持内容框的高度相等。当时浮动的布局方式并没有解决这个问题

## Flexbox 解决方案

当 `flexbox` 出现时就会实现这种效果

```scss
.flexbox {
  display: flex;
}
```

在默认情况下，子元素在同一排中会有`stretch`(默认)属性，会自动实现等高的效果。

但是如果添加两个`.column div` 子元素就会失去等高的效果

修改方案：

```scss
.flexbox {
  display: flex;

  // 确保内容元素填满.column
  .element {
    height: 100%;
  }
}
```

这样就会实现和上面同样的效果，并且随着`.element` 内容增涨而拉伸。

Grid 解决方案

`Grid`拥有类似的样式

``` scss
.grid {
  display: grid;
  // 切换默认的轴默认属性
  grid-auto-flow: column;
}
```

与`flexbox`相似,子元素会自动为等高的效果，同样子元素也需要定义高度，像`flexbox`一样

```scss
.grid {
  display: grid;
  grid-auto-flow: column;
  
  // 确保内容元素填满.column
  .element {
    height: 100%;
  }
}
```

`codepen` 示例:

[https://codepen.io/5t3ph/pen/BaoamwO](https://codepen.io/5t3ph/pen/BaoamwO)

## 哪种方案更好

`flexbox`的优势在于默认的样式即可实现等高的效果，而`grid`则需要显式设置。但是，元素本身并不是等宽的（这可能是一个优势，取决于内容的类型，例如导航链接）。

`grid`的优点在于天生具有等宽的样式，如果你想这样实现的话，另外一个优势是当你不想要`auto-flow`时
而是设置每一行的具体列数，`grid`可以轻松的做到。

更新我们`grid`方案，以处理定义每行最多3列的问题，简单如下：

```scss
&.col-3 {
  grid-gap: $col_gap;
  grid-template-columns: repeat(3, 1fr);
}
```

`flexbox`方案：

```scss
$col_gap: 1rem;

.flexbox.col-3 {
  // 需要定义 wrap 属性
  // 超出到下一行
  flex-wrap: wrap;

  .column {
    // "hack" for 元素间隙
    margin: $col_gap/2;
    // 计算宽度
    max-width: calc((100% / 3) - #{$col_gap});
  }
}
```

您还需要考虑如何以响应方式处理这些解决方案，但这有点超出本文的范围：)
