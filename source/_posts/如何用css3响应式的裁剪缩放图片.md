---
title: 如何用css3响应式的裁剪缩放图片
date: 2019-05-10 15:56:57
tags:
- 响应式
- css3
categories: 
- css小技巧
---

其实这篇文章的标题也可以叫做《如何用css3实现微信小程序image的mode属性》，因为我们今天讨论的响应式裁剪缩放图片，主要是实现了微信小程序image标签的mode属性。

<!--more-->

## 微信小程序image组件的mode属性
开发过微信小程序的同学都知道，当后台返回来的图片大小不一样，或者用户上传的图片大小不一样，而我们需要统一进行展示的时候，可以利用image标签的mode属性对图片进行缩放裁剪。mode属性的具体属性及表现形式如图:

![mode的取值](https://upload-images.jianshu.io/upload_images/6337462-4319133be748e790.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

[小程序开发文档](https://developers.weixin.qq.com/miniprogram/dev/component/image.html)

## 如何使用css实现mode效果
在移动端和PC端开发中，我们可以采用两种方案去实现mode的各种效果，由于使用了css3的一些属性，移动端浏览器适配性比较好，不需要额外做适配，PC端可以根据具体情况做一些兼容处理。
* 使用背景图片的`background-position`属性和`background-size`属性
* 使用css3的`object-fit`属性和`object-position`属性

**object-fit属性**
> 这个属性决定了像[img]和[videos]这样的替换元素的内容应该如何使用他的宽度和高度来填充其容器。
> 可以通过使用 [`object-position`] 属性来切换被替换元素的内容对象在元素框内的对齐方式。

取值：
`contain`
被替换的内容将被缩放，以在填充元素的内容框时保持其宽高比。 整个对象在填充盒子的同时保留其长宽比，因此如果宽高比与框的宽高比不匹配，该对象将被添加“[黑边]

`cover`
被替换的内容在保持其宽高比的同时填充元素的整个内容框。如果对象的宽高比与内容框不相匹配，该对象将被剪裁以适应内容框。

`fill`
被替换的内容正好填充元素的内容框。整个对象将完全填充此框。如果对象的宽高比与内容框不相匹配，那么该对象将被拉伸以适应内容框。

`none`
被替换的内容将保持其原有的尺寸。

`scale-down`
内容的尺寸与 `none` 或 `contain` 中的一个相同，取决于它们两个之间谁得到的对象尺寸会更小一些。

**object-position属性**

[CSS]属性 **`object-position`** 规定了[可替换元素]的内容，在这里我们称其为对象（即 **`object-position`** 中的 **`object`）**，在其内容框中的位置。可替换元素的内容框中未被对象所覆盖的部分，则会显示该元素的背景。

在接下来的方案中，有些方案可以使用两种方式实现，有的我暂时只用了一种方案，大家可以自取所需。两种方案html代码如下：
```html
<!-- 背景图片 -->
<div class="wrapper">
  <div class="inner"></div>
</div>

<!-- object-fit -->
<img src="2.jpg" class="objectImg">
```
```css
.wrapper{
  width: 200px;
  height: 200px;
  border: solid 2px red;
  overflow: hidden;
}
```
我们展示的原图如下：
![原图，来自网络，侵删](https://upload-images.jianshu.io/upload_images/6337462-e510d3d56cb14b75.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

#### 实现在固定区域内，图片保持其宽高比，使图片短边能完全显示出来

![图片保持其宽高比，使图片短边能完全显示出来](https://upload-images.jianshu.io/upload_images/6337462-7846713894a29efc.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
```
.inner{
    width: 100%;
    height: 0;
    padding-bottom: 100%;
    background: green;
    background-size: cover;
    background-image: url('4.jpg');
    overflow: hidden;
    background-position: center center;
    background-repeat: no-repeat;
    -webkit-background-size:cover;
    -moz-background-size:cover;
    background-size:cover;
}

.objectImg{
  width: 200px;
  height: 200px;
  object-fit: cover;
}
```


#### 实现在固定区域内，图片保持其宽高比，使图片长边能完全显示出来
![图片保持其宽高比，使图片长边能完全显示出来](https://upload-images.jianshu.io/upload_images/6337462-629587c3afc877bc.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
```
.inner{
  width: 100%;
  height: 0;
  padding-bottom: 100%;
  background: green;
  background-size: contain;
  background-image: url('4.jpg');
  overflow: hidden;
  background-position: center center;
  background-repeat: no-repeat;
}

.objectImg{
  width: 200px;
  height: 200px;
  object-fit: contain;
}
```
**这种方案，图片两侧可能会出现背景色**

#### 不保持纵横比缩放图片，使图片完全适应


![不保持纵横比缩放图片，使图片完全适应](https://upload-images.jianshu.io/upload_images/6337462-7eddd68999a63e6c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


```
.objectImg{
  width: 200px;
  height: 200px;
  object-fit: fill;
}
```


**别的方式直接用图片宽高100%就可以，这种会使图片变形**


#### 不缩放图片，只显示图片的顶部、底部、左侧、右侧、左上、右上、左下、右下区域


![不缩放图片，只显示图片的顶部、底部、左侧、右侧、左上、右上、左下、右下区域](https://upload-images.jianshu.io/upload_images/6337462-edd6256330a17491.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


```css
.objectImg{
  width: 200px;
  height: 200px;
  object-fit: none;
}
```
```
<div class="wrapper">
  <img src="4.jpg" class="objectImg" style="object-position: 50% 0;">
  <div class="text">object-fit 顶部</div>
</div>

<div class="wrapper">
  <img src="4.jpg" class="objectImg" style="object-position: 50% 100%;">
  <div class="text">object-fit 底部</div>
</div>

<div class="wrapper">
  <img src="4.jpg" class="objectImg" style="object-position: 0% 50%;">
  <div class="text">object-fit 左侧</div>
</div>

<div class="wrapper">
  <img src="4.jpg" class="objectImg" style="object-position: 100% 50%;">
  <div class="text">object-fit 右侧</div>
</div>

<div class="wrapper">
  <img src="4.jpg" class="objectImg" style="object-position: 0 0;">
  <div class="text">object-fit 左上</div>
</div>

<div class="wrapper">
  <img src="4.jpg" class="objectImg" style="object-position: 0 100%;">
  <div class="text">object-fit 左下</div>
</div>
```


原理上就是我们设置`object-fit: none`属性后，图片将不会进行缩放，我们可以通过`object-position`属性自由调整图片的位置。`object-position`属性的用法和`background-position`属性简直一模一样。
