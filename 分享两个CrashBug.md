# 分享两个 Crash Bug

> 分享两个在工作中遇到能导致 WebView Crash 的写法
> 
> 超长流水账预警！

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

这个比较新鲜，几个礼拜前的 233

还是类似的套路，某一天开始客服同学反馈在某合作方的 App 里进入到某个页面（下面用 A 页面代替）时 Crash 了。。

首先在鹅厂的优测找了远程真机测试，然后并不能重现。。

然后查了下，前后那几天并没有更新线上。。排除

然后查异常日志，然后并没有啥子东西。。再次排除

然后收集了下出问题的机型，还是比较有规律的。都是 Android 的，系统几乎都是 4.4，这里列举一些 UA：

> Mozilla/5.0 (Linux; Android 4.4.4; vivo X5Max V Build/KTU84P) AppleWebKit/537.36 (KHTML, like Gecko) Version/4.0 Chrome/33.0.0.0 Mobile Safari/537.36
> 
> Mozilla/5.0 (Linux; Android 4.4.4; MI 3 Build/KTU84P) AppleWebKit/537.36 (KHTML, like Gecko) Version/4.0 Chrome/33.0.0.0 Mobile Safari/537.36
> 
> Mozilla/5.0 (Linux; Android 4.4.4; CHM-CL00 Build/CHM-CL00) AppleWebKit/537.36 (KHTML, like Gecko) Version/4.0 Chrome/33.0.0.0 Mobile Safari/537.36
> 
> Linux; U; Android 4.4.4; HM NOTE 1LTE MIUI/V8.0.1.0.KHICNDG

而且只在合作方的 App 里 Crash，是不是觉得很熟悉的配方？

但是！诡异的是远程真机都试过，就是没法重现！

于是陷入了僵局，来回调试查日志什么的搞了两个多礼拜，依旧没啥眉目。。

好了，我认，咸鱼（又是。。）买了台二手红米 Note1 Lte，就是上面第3个 UA 那个

然后某个周日，手机到了，然而依旧无法重现。系统是 4.4 的，但是小版本号对不上，好吧我们来刷机吧。。

你们猜后面发生了什么。。。

是的，我刷错包了。。。。。。（不玩 Android 好多年

于是手机就变成了 zhuan 头。。（而且还是改代码强刷的 =。=

典型的不作死就不会死系列。。

当然，第二天就让同事拿去楼下的小米维修部救回来。正常开机那一瞬间感觉手里捧着个新生儿一般。。。颤抖。好了不废话找 Android 开发的同学（让专业的来。。）刷了个对应的版本。。恩，不用猜还是没法重现。。

下班回去的时候突然想到，貌似都没去看过 A 页面的代码啊。。

第二天继续，果然看出问题了：

> 首先，A 页面里有段代码是让某个提示只在合作方的 App 里显示的，而且是要满足特定条件才显示的（换句话说之前都是不满足这个特定条件
> 
> 然后，这个提示里有个动画，这个动画小伙伴写在伪元素里了

很好，先把这个提示给放出来（去掉条件判断

bingo~ 挂了 233

尝试把伪元素里的动画去掉，不会 Crash 了。。

好吧我只记得是说不要在伪元素里写动画因为有些是不支持的，没想到还有这种 feature。。

然后搜了下确实有人总结过了。。。[原来 CSS 这样写是会让 app 崩溃的
](http://ued.ctrip.com/blog/crash-app-by-css.html)

最后总结下：

> 感谢咸鱼让我翻身！




 



