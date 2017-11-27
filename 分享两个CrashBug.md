# 分享两个 Crash Bug

> 分享两个在工作中遇到能导致 WebView Crash 的写法

## 第一个

这个比较久远，大概快有一年了。情况是某一天开始客服同学反馈用户在 WebView 页面点击然后 App 就 Crash 了，而且只在 iOS 出现。

当时觉得非常诡异，反馈的问题页面有很多，看不出有什么规律。有些较为简单的页面也有反馈，查过代码也看不出有什么毛病。异常日志里也没找到什么相关信息。

后面统计了一下，出问题的都是 iOS 7，最多的版本是 7.1.2。公司那会刚好没有 iOS 7 的设备，于是申请买了一台。咸鱼二手 4s（貌似是），深圳华强北发货 233

在到货前猜测过会不会是 localStorage 的锅，因为这边的页面用这玩意本地存储 CSS 跟 JS。在几个可能的地方加了 try/catch，然而并没有什么用 = =

然后盼星星盼月亮，好吧并没有等很长时间手机到了。先打开 Safari 试下吧，果然挂了！

很好我们来分析一下，准确的说并不是 WebView 点击页面导致 Crash，而是在滚动的时候挂了。那么查下滚动容器的代码，当时为了在 iOS 下触发快速滚动回弹，加上了 `-webkit-overflow-scrolling: touch` ，尝试去掉后，问题解决了！

这样问题就很清晰了，应该是 native 层面的某些原因导致添加 `-webkit-overflow-scrolling: touch` 后在 iOS 7 的某些版本下 Crash 了。基于这种情况，我们只要在 iOS 大于 7 的系统下使用 `-webkit-overflow-scrolling: touch` 就好了，类似这样：

	html.ios-gt-7 .content {
		-webkit-overflow-scrolling: touch;
	}

这样修改后就再也没收到上述 Crash 的反馈了~

不过我们还得再挖掘下，到底是什么鬼 233

搜了下看到这个：[-webkit-overflow-scrolling: touch crashes browser on iPad
](https://github.com/coolwanglu/pdf2htmlEX/issues/523)，大概是说 `-webkit-overflow-scrolling: touch` 会导致内存暴涨，然后就炸了 233

突然想起之前看到哪里说使用 `-webkit-overflow-scrolling: touch` 时 UIWebView 会生成一层 ScrollView 嵌套进去，貌似是硬件加速什么的 233

> 找到了。。。 [网页在Safari快速滚动和回弹的原理： -webkit-overflow-scrolling : touch;的实现
](http://blog.csdn.net/hursing/article/details/9186199)

不过比较尴尬的是我用 Reveal 看了下（iPod Touch 9.3.1）似乎都是会生成一个 ScrollView 嵌套进去的。。呃

![](https://github.com/xyqfer/blog/raw/master/img/Reveal.jpeg)

## 第二个

待补充

