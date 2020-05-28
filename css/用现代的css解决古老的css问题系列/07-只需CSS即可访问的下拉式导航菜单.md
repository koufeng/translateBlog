# 只需CSS即可访问的下拉式导航菜单

> 原文地址: 《[CSS-Only Accessible Dropdown Navigation Menu](https://moderncss.dev/css-only-accessible-dropdown-navigation-menu/)》

***

> 我从事前端开发13年有余，一直致力于研究现代CSS解决方案，这是我该系列文章的第七篇

我们将探讨：

- 动画与CSS过渡和转换

- 使用 :focus-within伪类

- 用于定位的CSS grid

- 动态定心技术

- 下拉式菜单的可访问性考虑

***

如果你曾经为 "鼠标悬停 "的概念而烦恼，那么这个升级版就是为你准备的!

在我们讲太深之前还是要提醒你，虽然我们的技术100%只使用了CSS，但有些用例可能会因为使用了一些香草JS而受益，尤其是为移动用户创造更好的体验。还有一个关键的功能需要一个[polyfill](https://allyjs.io/api/style/focus-within.html)来实现这个功能- `:focus-within` -[以获得最可靠的支持。](https://caniuse.com/#search=focus-within)。但相比于以往需要一个或多个jQuery插件来完成的日子，我们还是有了很大的进步。那么，让我们开始吧!

如果你没有使用过Sass，你可能需要花5分钟的时间来了解[Sass的嵌套语法](https://sass-lang.com/guide#topic-3)，这样才能最容易理解给出的代码示例。

## 基础导航HTML

我们会继续加强，但这里是我们的起点结构。

```less
<nav aria-label="Main Navigation">
  <ul>
    <li><a href="#">About</a></li>
    <li class="dropdown"><span class="dropdown__title" id="dropdown-title">Sweets</span>
      <ul class="dropdown__menu" aria-labelledby="dropdown-title">
        <li><a href="#">Donuts</a></li>
        <li><a href="#">Cupcakes</a></li>
        <li><a href="#">Chocolate</a></li>
        <li><a href="#">Bonbons</a></li>
      </ul>
    </li>
    <li><a href="#">Order</a></li>
  </ul>
</nav>
```

这是导航链接的语义标准。这种结构是灵活的，可以在你的页面上的任何地方使用，所以它可以像主导航一样轻松地成为侧边栏的目录。

一开始，我们就实现了两个专门针对无障碍的功能:

1.`aria-label`在`<nav>`上，当辅助技术被用来通过地标导航页面时，帮助识别它的目的。

2.`ria-labelledby`在`.dropdown__menu`上，链接到`.dropdown__title`的id，让辅助技术读取类似于 "链接，甜甜甜圈4项2级 "的内容，其中 "甜甜圈 "是下拉标题的值

我们（大部分）默认的起始外观如下:

![starting appearance](https://dev-to-uploads.s3.amazonaws.com/i/ph6ne7veudghpvnajgp6.png)

## 最初的导航样式

首先，我们将给`nav`赋予一些容器样式，并将其定义为网格容器。然后，我们将删除`nav ul`和`nav ul li`中的默认列表样式。

```less
nav {
  background-color: #eee;
  padding: 0 1rem;
  display: grid;
  place-items: center;

  ul {
    list-style: none;
    margin: 0;
    padding: 0;
    display: grid;

    li {
      padding: 0;
    }
  }

}
```

![](https://dev-to-uploads.s3.amazonaws.com/i/awvgs6rxt2j1787b9lbi.png)

我们已经失去了层次性的定义，但我们可以从以下几点开始把它找回来。

```less
nav {
  // ...existing styles

  > ul {
    grid-auto-flow: column;

    > li {
      margin: 0 0.5rem;
    }
  }
}
```

通过使用子组合器选择器`>`，我们定义了顶层的`ul`，它是`nav`的直接子元素，应该把它的`grid-auto-flow`切换到`column`，这样可以有效地把它更新为沿`x-axis`。然后我们给顶层的`li`元素添加margin，让它的定义更多一些。现在，未来的下拉项出现在 "Sweets "菜单的下方，并且更清楚地显示了它的子项。

![](https://dev-to-uploads.s3.amazonaws.com/i/y6ikx5v9h84lm44cfksp.png)

接下来，我们先给所有的链接以及`.dropdown__title`加点样式，然后除了`.dropdown__title`外，只给顶层链接加点样式:

```less
nav {
  > ul {

    > li {
      // All links contained in the li
      a,
      .dropdown__title {
        text-decoration: none;
        text-align: center;
        display: inline-block;
        color: blue;
        font-size: 1.125rem;
      }
      // Only direct links contained in the li
      > a,
      .dropdown__title {
        padding: 1rem 0.5rem;
      }

    }
  }
}
```

![](https://dev-to-uploads.s3.amazonaws.com/i/pmnd1jz05g8u934r72lk.png)

## 基础下拉样式

到目前为止，我们一直在依赖元素选择器，但我们将为下拉菜单引入类选择器，因为在一个给定的导航列表中可能会有多个选择器。

我们首先要对`.dropdown__menu`和它的链接进行风格化处理，通过定位和动画来帮助更清晰地识别它。

```less
.dropdown {
  position: relative;

  .dropdown__menu {
    background-color: #fff;
    border-radius: 4px;
    box-shadow: 0 0.15em 0.25em rgba(black, 0.25);
    padding: 0.5em 0;
    min-width: 15ch;

    a {
      color: #444;
      display: block;
      padding: 0.5em;
    }
  }
}
```

![](https://dev-to-uploads.s3.amazonaws.com/i/8fgjgciuge2ts2k0nqbl.png)

其中一个明显的问题是`.dropdown__menu`影响了导航容器，从下拉菜单周围的灰色导航背景可以看出。

我们可以通过在`.dropdown__menu`中添加`position: absolute`来解决这个问题，这样就可以把它从正常的文档流程中剥离出来:

![](https://dev-to-uploads.s3.amazonaws.com/i/jdvoh962drdgg0vu4bze.png)

你可以看到它在父列表项的左侧和下方对齐。这可能是你设想中想要的位置。

我们要带出一个居中的技巧，将菜单中心对准列表项:

```less
.dropdown__menu {
  // ... existing styles

  position: absolute;

  // Pull up to overlap the parent list item very slightly
  top: calc(100% - 0.25rem);
  // Use the left from absolute position to shift the left side
  left: 50%;
  // Use translateX to shift the menu 50% of it's width back to the left 
  transform: translateX(-50%);
}
```

这种居中技术的神奇之处在于，菜单可以是任意宽度，甚至是动态宽度，而且可以适当居中。

![](https://dev-to-uploads.s3.amazonaws.com/i/rgd0anjvrugsq1x8mk9x.png)

## 下拉式展示样式

有两个主要的触发器我们希望用来打开菜单: `:hover`和 `:focus`。

但是，传统的`:focus`不会保持下拉菜单的打开状态。一旦初始触发器失去了焦点，键盘焦点仍然可以在下拉菜单中移动，但从视觉上看菜单将消失。

### :focus-within

有一个即将推出的伪类叫`:focus-within`，它正是我们所需要的，使之成为一个只支持CSS的下拉菜单。正如介绍中提到的，如果你需要支持IE < Edge 79，它确实需要一个[polyfill](https://allyjs.io/api/style/focus-within.html)。

>`:focus-within` CSS伪类代表一个已经接收到焦点的元素，或包含一个已经接收到焦点的元素。换句话说，它代表一个元素本身被 `:focus` 伪类匹配的元素，或有一个被 `:focus` 匹配的子元素。

### 默认情况下隐藏下拉菜单

在显示下拉菜单之前，我们需要将其隐藏起来，所以我们将使用隐藏样式作为默认状态。

你的第一直觉可能是 `display: none`，但这将我们锁定了优雅的动画过渡。

下一步，你可以尝试简单的`opacity: 0`，这样可以明显地隐藏它，但会留下 "幽灵链接"，因为元素仍然有计算高度。

相反，我们将使用`opacity `和`transform`:

```less
.dropdown__menu {
  // ... existing styles
  transform: rotateX(-90deg) translateX(-50%);
  transform-origin: top center;
  opacity: 0.3;
}
```

我们添加opacity，为了以后能有更平滑的效果不能全部干到0。

而且，我们更新了`transform`，包括`rotateX(-90deg)`，这将使菜单在3D空间中旋转到90度 "后退"。这将有效地去除高度，并在显示时形成一个有趣的过渡。另外，你会注意到我们添加的`transform-origin`属性，它是为了更新应用变换的周围点，而不是默认的水平和垂直中心。

在进行揭示之前，我们需要添加一个`transition`。我们将其添加到`.dropdown__menu`中，这样它就可以同时适用于开关`hover`/`aka`，又称 "向前 "和 "向后"。

```less
.dropdown__menu {
  // ... existing styles
  transition: 280ms all ease-out;
}
```

## 揭开下拉菜单

有了这些事先的设置，就可以像简洁地完成`hover`和`focus`上的下拉菜单:

```less
.dropdown {
  // ... existing styles

  &:hover,
  &:focus-within {

    .dropdown__menu {
      opacity: 1;
      transform: rotateX(0) translateX(-50%);
    } 
  
  }

}
```

从本质上说，我们已经将`rotateX`重新设置为0，然后将`opacity`全部调到1，以达到完全可见。

效果是这样的:

![](https://dev-to-uploads.s3.amazonaws.com/i/5z5zaa54czp54u7jleca.gif)

`rotateX`属性让菜单的外观从后面摆动进来，`opacity`只是让它整体上的过渡更柔和一些。

## 处理悬停意向

当我刚开始战斗下拉菜单时，我主要是为IE7创建下拉菜单。在一个大项目中，有几个团队成员问我 "如果我只是滚动/鼠标点击菜单，你能阻止菜单出现吗？"。经过一番搜索，我终于找到了一个解决方案，hoverIntent jQuery plugin.

我需要这样设置，因为既然我们使用的是`transition`，我们还可以添加一个非常轻微的延迟。这将防止 "驱动式 "鼠标翻转时触发下拉动画。

延迟作为第三个值加在指定哪些属性过渡之后、过渡时序函数之前，所以延迟是作为第三个值加的:

```less
.dropdown__menu {
  // ... existing styles
  transition: 280ms all 120ms ease-out;
}
```

看看结果:

![](https://dev-to-uploads.s3.amazonaws.com/i/mmts717vg1uxyt8mivgq.gif)

触发菜单需要一个相当悠闲的翻滚，我们可以松散地推断为打开菜单的意图。这个延迟还是很短的，在打开菜单之前不会被人有意识地注意到，所以这会更好!

你仍然可以选择使用javascript来增强，特别是要推出一个 "巨型菜单"，会有更大的破坏性，但这仍然是相当好用的。

## 下拉菜单指示器

有了`hover`之后我们需要一个额外的提示，让用户知道这个菜单有额外的选项。一个非常常见的惯例是模仿原生选择元素的指示器的 "caret "或 "down arrow"。

为了添加这个，我们将更新`.dropdown__title`样式。我们将把它定义为一个内`inline-flex`，然后创建一个`:after`元素，使用边框技巧创建一个向下箭头。我们使用了`translateY()`的破折号将其与我们的文本进行对齐:

```less
.dropdown {
  // ... existing styles

    .dropdown__title {
      display: inline-flex;
      align-items: center;
      pointer-events: none;

      &:after {
        content: "";
        border: 0.35rem solid transparent;
        border-top-color: rgba(blue, 0.45);
        margin-left: 0.25em;
        transform: translateY(0.15em);
      }
    }

}
```

我们还偷偷地加入了`pointer-events: none`，它将删除标题本身上的任何光标事件。

![](https://dev-to-uploads.s3.amazonaws.com/i/t6fj8oode9wkn736dyq7.png)

## 触摸设备的处理

如果你在手机上尝试一下我们到目前为止制作的东西，你将无法打开菜单。

由于CSS中没有`:click`或`:touch`伪类，所以我们需要让`.dropdown__title`能够接收focus。

我们可以把它变成一个按钮，因为那是一个原生的可聚焦元素，但我们也可以把`tabindex="-1"`应用到`span`中。这也会向浏览器表明该元素能够接收到焦点，这使得我们的`:focus-within`选择器能够工作。

最新数据:

```less
<span tabindex="-1" class="dropdown__title" id="dropdown-title">Sweets</span>
```

## 关闭手机上的菜单

这里是你最终可能需要用javascript来强化的地方。

为了让它只用CSS，并为非应用型网站所接受，你需要在主体上应用`tabindex="-1"`，允许在菜单之外的任何点击都可以将焦点从菜单中移除，并允许它关闭。

这可不太行，所以你可能会用javascript来增强这一点，特别是如果你定义的`nav`使用`position: sticky`与用户一起使用的时候，你可能想用javascript来隐藏。

## 最后

这里是最后的结果，有了一些额外的样式，包括一个箭头来更直观地将菜单与链接项连接起来，在所有的导航链接上自定义焦点状态，以及`nav`上的`position: sticky`

演示:
https://codepen.io/5t3ph/embed/preview/MWaJePa?height=265&amp;theme-id=default&amp;default-tab=result&amp;user=5t3ph&amp;slug-hash=MWaJePa&amp;preview=true&amp;name=cp_embed_1