# 渐变与 Sketch

> 由于工作中用到的基本都是线性渐变，因此下文的渐变均指线性渐变
> 
> 考虑到有 PostCSS 加持因此下文也不讨论写法上的兼容问题 🤣

那你还说个 x？

好，先说下最常用到的写法。给到的渐变背景几乎都是从某个角度开始，中间有多个 color-stop 的，那么一般写法是这样的：

```
linear-gradient(37deg, #393B4C 10%, #9B9B9B 90%)
```

那么问题来了，这个渐变角度是怎么定义的？

先说结论：

> 渐变角度是渐变线与渐变容器中心点向上垂直线之间的夹角
> 
> 默认 180deg，即 to bottom
> 
> 0deg 时 0% 在底部，100% 在顶部

来人，上图！

![](https://github.com/xyqfer/blog/raw/master/img/linear-gradient2.png)

![](https://github.com/xyqfer/blog/raw/master/img/linear-gradient.png)

接下来就轮到我们的主角出场了：

## Sketch

是这样的，Sketch 有个 `Copy CSS Attributes` 的功能，可以导出一些 CSS 样式，比如背景、边框、阴影等，背景里也包括渐变。举个例子：

![](https://github.com/xyqfer/blog/raw/master/img/sketch-1.png)

然后用 `Copy CSS Attributes` 导出代码生成的渐变是这样滴：

![](https://github.com/xyqfer/blog/raw/master/img/linear-gradient3.png)

显然反了，这里把渐变角度取反就可以了~

![](https://github.com/xyqfer/blog/raw/master/img/linear-gradient4.png)

## 参考资料

- [Do you really know CSS linear-gradients?](https://medium.com/@patrickbrosset/do-you-really-understand-css-linear-gradients-631d9a895caf)
- [CSS linear-gradient overlay](https://codepen.io/captainbrosset/full/ByqRMB/)





