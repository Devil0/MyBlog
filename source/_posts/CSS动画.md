---
title: CSS动画
date: 2018-05-04 16:04:29
tags: css
---

> 区分 animation（动画）、transition（过渡）、 transform（变形）；理解各个 css 属性对应的常用取值与应用

#### transform——对元素 2D 或 3D 转换

```css
transform: none|transform-functions;
```

transform-functions——对应函数有如下：

1. translate：平移
2. scale：缩放
3. rotate(angle)：旋转
4. skew(angle)：倾斜

备注：

1. 以上函数基于平面，其他对应的有-X、-Y、-3d，分别对应 X 轴、Y 轴、3D 转换
2. transform-functions 接受多个函数，eg：
   `js transform: translate(10px,20px) scale(.5,.5);`
   #### transition——定义过渡状态的过程

```css
transition{ transition-property  transition-duration  transition-timing-function  transition-delay}
```

对应属性值概念如下：

1. transition-delay
2. transition-duration
3. transition-property
4. transition-timing-function——对应值
   * linear：匀速
   * ease-in：加速
   * ease-out：减速（ease-in-out）
   * cubic-bezier 函数：自定义速度模式

触发方式：

* 伪类触发：:hover : focus :checked :active
* js 触发：toggleClass

#### Animation——定义帧动画

```css
animation: 1s 1s actionName linear 3 forwards normal;
```

对应属性值

```css
animation-delay: 1s;
animation-duration: 1s;
animation-name: actionName;
animation-timing-function: linear;
animation-iteration-count: 3;
animation-fill-mode: forwards;
animation-direction: normal;
```

动画事件对应处理

```css
@keyframes name {
  from {
    background: #c00;
  }
  50% {
    background: orange;
  }
  to {
    background: yellowgreen;
  }
}
```
