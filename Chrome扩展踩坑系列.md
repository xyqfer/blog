# Chrome 扩展踩坑系列

嘛，最近因为工作上的需求折腾了下 Chrome 扩展，主要用在淘宝天猫上跑些批量自动化的东西。下面说下那些各式各样的坑 233

> 温馨提醒：以下有封号危险 233


## 淘宝地址页

先看张图吧

![](https://github.com/xyqfer/blog/raw/master/img/tb-addr1.png)

需要注入的信息包括省市区街道、详细地址、姓名、手机号码，后面三个都好处理，省市区街道要蛋疼些。

首页，需要展开地址选择器：

![](https://github.com/xyqfer/blog/raw/master/img/tb-addr2.png)

> document.querySelector(".J_CnCity").click();

然后依次选择对应的地址，由于有些名称不是完全一致的（比如广东省、广东其实对应的是一个地址项），因此做成模糊匹配了，即 `indexOf` （逃

由于某些历史原因，注入的地址里没有街道的，因此需要在注入详细地址后触发文本框的 `blur` 事件，然后会有街道的自动提示，再点选上就好了 233

![](https://github.com/xyqfer/blog/raw/master/img/tb-addr3.png)

至于删除地址呢，新建 iframe 然后把地址设置为删除按钮的链接就行了 233

## 商品详情页

由于需要批量下单，因此表单提交得新开窗口打开了。坑爹的是 `form` 的 `target` 是在 js 里写的：

> xxform.target = "_self";

想了好久都没啥好办法，干脆直接劫持掉然后改成 `_blank` 再冻住好了（指的是不可写） 233

```
// 这里 $ 指的是 document.querySelector，控制台执行
$("#J_FrmBid").target = "_blank";
Object.defineProperty($("#J_FrmBid"), "target", {
    writable: false
});
```

然后还有一个需求是需要改表单的提交地址，跟上面的一样坑爹，也是在 js 里设置的。好吧只能 hook 掉 `submit` 然后里边来搞了。。

```
HTMLFormElement.prototype._nativeSubmit = HTMLFormElement.prototype.submit;

HTMLFormElement.prototype.submit = function () {
   this.action = this.action + "#xxx";
   this._nativeSubmit.apply(this, arguments);
};
```

还有，别一口气开一堆的窗口，很容易触发滑块验证的 233

## 下单页

淘宝的下单页选地址时会有个 `confirm` 出来让你确认的，显然给自动选地址的功能带来了很大的麻烦。暴力点，直接覆盖掉：

> confirm = function() {return true;}

## Windows 下 Notification 貌似出不来了 233

还没去查 = =

## ajax hook

很多操作都是依赖 ajax 然后触发下一步的，极度建议注入一个 ajax hook 的东西，方便监听响应事件

## 后续计划

好好学习，按时填坑


