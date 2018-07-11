---
title: CSS动画
date: 2018-05-04 16:04:29
tags: css
---

> 区分 animation（动画）、transition（过渡）、 transform（变形）、translate(平移)；理解各个 css 属性对应的常用取值与应用

#### transform——对元素 2D 或 3D 转换

```css
transform: none|transform-functions;
```

transform-functions——对应函数有如下：

1. translate：平移
2. scale：缩放
3. rotate(angle)：旋转
4. skew(angle)：倾斜

<!-- more -->
备注：

1. 以上函数基于平面，其他对应的有-X、-Y、-3d，分别对应 X 轴、Y 轴、3D 转换
2. transform-functions 接受多个函数，eg：
   ```css
   transform: translate(10px,20px) scale(.5,.5);
   ```
   
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

注意：
1. IE10+
2. 不是所有的CSS属性都支持transition
3. transition的优点在于简单易用，但是它有几个很大的局限。
  1. transition需要事件触发，所以没法在网页加载时自动发生。
  2. transition是一次性的，不能重复发生，除非一再触发。
  3. transition只能定义开始状态和结束状态，不能定义中间状态，也就是说只有两个状态。
  4. 一条transition规则，只能定义一个属性的变化，不能涉及多个属性。

#### Animation——定义帧动画(指定动画一个周期持续的时间，以及动画效果)

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

简写

```css
animation: 1s 1s rainbow linear 3 forwards normal;
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
