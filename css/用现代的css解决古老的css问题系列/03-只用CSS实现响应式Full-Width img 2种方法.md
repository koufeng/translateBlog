# 只用CSS实现响应式Full-Width img 2种方法

> 原文地址: 《[CSS-Only Full-Width Responsive Images 2 Ways](https://moderncss.dev/css-only-full-width-responsive-images-2-ways/)》

> 我从事前端开发13年有余，一直致力于研究现代CSS解决方案，这是我该系列文章的第2篇

以前在`jquery`流行的时候用于响应式背景图片的最受欢迎的工具是[Backstretch jQuery](https://www.jquery-backstretch.com/)插件。

在`background-size`属性得到广泛支持之前（即IE<9总市场份额下降了）我在大约30个项目中使用了此插件。

``` css
background-size: cover;
```

根据[caniuse.com](https://caniuse.com/#feat=mdn-css_properties_background-size_contain_and_cover)的说法,虽然这个属性和价值已经支持了9年多了! 但与使用`Backstretch`或其他方案交织在一起的网站可能还没有更新。

另一种方法是利用标准的`img`标签，并使用了神奇的属性：

```scss
object-fit: cover;
```

下面我们就来看看每一种方案的使用方法，学习一下什么时候选择哪一个合适。

## background-size: cover 方案

[//]:<> (A decade of my background was creating highly customized WordPress themes and plugins for enterprise websites. So using the example of templated cards, here's how you might set up using the background-size: cover solution.)

我十年的工作背景是为企业网站创建高度定制的WordPress主题和插件，所以用模板化的卡片为例，下面是你可能使用的`background-size: cover`解决方案来设置。

首先是`HTML`，将图片作为背景插入样式属性中。鼓励用一个`ria-label`来代替普通`img`标签上的`alt`属性。

```html
<article class="card">
  <div class="card__img" aria-label="Preview of Whizzbang Widget" style="background-image: url(https://placeimg.com/320/240/tech)"></div>
  <div class="card__content">
    <h3>Whizzbang Widget SuperDeluxe</h3>
    <p>
      Liquorice candy macaroon soufflé jelly cake. Candy canes ice cream
      biscuit marzipan. Macaroon pie sesame snaps jelly-o.
    </p>
    <a href="#" class="button">Add to Cart</a>
  </div>
</article>
```

对应`CSS`如下, 其中`padding-bottom`是一个特殊的技巧，使`div`背景图片保持16:9的比例。

```scss
.card__img {
  background-size: cover;
  background-position: center;
  // 16:9 ratio
  padding-bottom: 62.5%;
}
```

`codepen`示例：

[https://codepen.io/5t3ph/pen/VwvvVeo](https://codepen.io/5t3ph/pen/VwvvVeo)

## object-fit: cover 方案

根据[caniuse data](https://caniuse.com/#search=object-fit)的数据，没有`polyfill`的情况下，不支持IE浏览器。

这个样式是直接放在`img`标签上的，所以我们把我们的`HTML`更新为如下，把 `aria-label` 换成 `alt`。

```html
<article class="card">
  <img class="card__img" alt="Preview of Whizzbang Widget" src="https://placeimg.com/320/240/tech"/>
  <div class="card__content">
    <h3>Whizzbang Widget SuperDeluxe</h3>
    <p>
      Liquorice candy macaroon soufflé jelly cake. Candy canes ice cream
      biscuit marzipan. Macaroon pie sesame snaps jelly-o.
    </p>
    <a href="#" class="button">Add to Cart</a>
  </div>
</article>
```
然后我们更新css样式使用`height`属性来约束图像，以便任何尺寸的图像都符合约束比例。如果图像的固有尺寸大于约束的图像尺寸，那么 `object-fit`属性就会接管,默认情况下，img会在`card`+ 高度定义所创建的范围内居中。

```css
.card__img {
  object-fit: cover;
  height: 30vh;
}
```

codepen:

[https://codepen.io/5t3ph/pen/VwvvVqa](https://codepen.io/5t3ph/pen/VwvvVqa)

## 什么时候选择哪一个合适

如果你必须支持老版本的`IE`，那么在没有`polyfill`的情况下，你只能采用`background-size`这种解决方案。

这两种方案都可以根据你控制宽高比来实现`Full-Width`响应式图片。

如果你选择`background-size`这种方式：

- 应用于包含额外内容的容器（例如网站`header`背景）

- 应用于伪元素样式中，`img`标签不可用时

- 更加优雅地应用统一的图像尺寸

- 纯粹是装饰性的图像，不需要固有的`img`语义。

选择 `object-fit`方式:

- 为了保持图像提供的所有语义，使用标准的img对你的上下文来说是最好的。
