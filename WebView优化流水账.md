# WebView 优化流水账

> 据说会持续更新 😳

## 换容器

- Android 接入 [X5](https://x5.tencent.com/tbs/)
- iOS 换 WKWebView
	- [iOS WKWebview 网页开发适配指南](https://mp.weixin.qq.com/wiki?t=resource/res_main&id=mp1483682025_enmey)
	- [WKWebView从入门到趟坑](https://mp.weixin.qq.com/s/18xXQWboHcjybd_VtcTmUg)

## 资源缓存

由于某司有大量的运营活动需求，这类页面图片占了约 70% 的大小。因此可以将 WebView 中的图片加载接入 native 层面去缓存，图片名作为唯一 key（与前端约定好图片更换后文件名会改变）。

然后是 webp，Android 这边考虑到一些可能存在的问题从 4.4（+ 特性检测）开始支持。iOS 目前不支持，需要客户端打入解码包。

另外就是资源预推，可以在活动开始前将相应的图片推送至客户端缓存起来，也算是一种错峰吧~

还有就是考虑到 iOS WebView 杀进程后资源都得重新拉，可以将 CSS/JS 存储至 localStorage，配合 combo 使用口味更佳 233

说到这个，还可以在客户端打包进去一些基础的公用库，比如 [支付宝H5开放文档](https://myjsapi.alipay.com/fe/preset-assets.html)，提到把 zepto、fastclick、swiper、react、vuejs 等基础库预置在客户端里内，并且区分版本（支持多版本

## HTTP/2

从 [caniuse](https://caniuse.com/#search=http2) 的结果看来支持程度还是挺不错的~

![](https://github.com/xyqfer/blog/raw/master/img/http2.png)

## CDN

我打算从别的角度来聊下，毕竟好处就不用多说了，而且价格也在不断的降。

对于类似我司这样的小厂，当然是花钱买 CDN 服务划算多了，自己烧钱搭是玩不起的。不过这样有个较为明显的问题，花钱买服务是没法控制 CDN 节点机器的，换句话说某些边缘节点挂了然后用户投诉了你也还得等，毕竟工单是有时效性的。

早先曾用过某厂的 CDN 服务，然后某天一个用户过来投诉说他那里页面打不开了，把这儿的客服骂了一整天（并没有夸张），老板都出面来安抚了。最后查了日志也没查到什么原因，目测 DNS 的锅。后来临时先把静态资源切回应用服务器就好了。

后面又换了一家，也出现过这样的问题。这次是同事反馈的，我自己也遇到过几次。查日志依旧无果，后面放弃了不折腾了。都先扔到 OSS 去，打爆了再开个新的 bucket 得了 233

貌似还没看到有支持 HTTP/2 的厂商，后面再确认下（flag

## 压缩

除了 gzip 也可以用下 Brotli，据说压缩率更高

## JSBridge && Router

功能增强吧~


