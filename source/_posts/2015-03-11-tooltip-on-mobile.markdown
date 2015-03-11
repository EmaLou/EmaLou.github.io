---
layout: post
title: "Bootstrap中的tooltip 手机端显示"
date: 2015-03-11 23:27:26 +0800
comments: true
categories: 
---

tooltip相信大家都不陌生，实现方法也多种多样。
现在比较成熟的前端框架也都支持这一js特效，比如bootstrap。

HTML 代码

	<span class="tooltip" data-placement="bottom" data-content="This is a tooltip"></span>

JS 代码

	$('.tooltip').popover({
         html: true,
         container: body,
         trigger: 'hover'
     });


HTML不难理解，data-placement 指tooltip相对元素的位置，bottom就是放在元素下面，data-content就是tooltip里面的内容了。

说说JS，这个方法是给具有.tooltip的class执行popover方法，就可以快速实现tooltip。

**参数很重要**，container参数指包含tooltip的元素，因为默认tooltip的position是absolute，可以通过将container元素的position属性设为relative，来改变tooltip的相对位置。

在普通电脑屏幕上，这个功能也许不重要，但是在手机端，它对tooltip的位置起着决定性的作用。

再看第三个参数，trigger-触发事件，这里用鼠标hover事件来触发tooltip的显示（手机端自动将hover事件变成click事件）。

---

###下面说一个手机端的坑

上面都是引子，这才是重点 - 手机端横竖屏转换时的tooltip位置

这里有两点要注意，

1. 手机端的tooltip是靠click触发的，
2. 横竖屏切换时样式会改变

如果还是上面的代码，会发现click之后，tooltip不消失，这时手机从横屏切换到竖屏，tooltip的位置不再之前那个图标下了，它移动了。

其实不是tooltip移动了，而且整个页面的样式变化了，图标位置也改变了，而tooltip没有被重新触发，还保持在之前相对container元素的位置，恩，玩坏了。

笔者初看到这个问题的时候想了很多办法，比如改变container元素，给它一个position:relative，然后在横竖屏切换的时候改变container的位置。。。比如横竖屏切换的时候让tooltip隐藏起来，后面需要tooltip再重新触发，这时候位置就是正确的。。。

但是这些方法都有**瑕疵**。

最后用了牺牲浏览器性能的方法解决了这个问题，先上js代码

	$(window).on("resize", function(){
        resizeTooltip();
     });

     function resizeTooltip() {
         if ($('.popover').length != 0) {
             $('.tooltip').popover('show');
         } else {
             $('.tooltip').popover('hide');
         }
     }

给window上绑定resize监听事件(resize是jquery默认的事件，只能给window上绑定，不能给元素上绑定)，一旦浏览器窗口大小发生改变，就执行resizeTooltip方法。

bootstrap会给tooltip加上默认class - popover，横竖屏变化时，先监测页面上有没有tooltip，有的话就调用.popover('show') 重新render tooltip，没有的就调用.popover('hide')隐藏tooltip。

至此，手机，pad随便玩，tooltip都能跟随横竖屏切换正常显示了。

**如果还不行，就给span外面加一个空的div，用这个div来作为tooltip的container**。

如果再不行，就调调样式。

**注意：**如果页面上有多个tooltip，这里就要在window resize的时候多监测几个tooltip。
要通过class 或 id 明确区分每一个tooltip，对每一个tooltip都要做一次存在与否的检查，这样才能确保手机端的样式正确。



