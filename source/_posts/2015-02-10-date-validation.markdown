---
layout: post
title: "JavaScript实现的日期验证"
date: 2015-02-10 23:08:40 +0800
comments: true
categories: 
---

项目需要，最近在用angular做各种validation。

---

###日期验证

这里分享一个日期验证。

github地址：<https://github.com/EmaLou/date-validation>

看页面(有点丑)：<http://emalou.me/date-validation/>

为什么用js，不用高大上的angular？

因为简单的js复用性高。只包含了验证是否为合法日期的代码，比较基础，可以cover大部分通用情况。

###禁止用户输入大于12的月份

其实日期验证的功能，有很多特性可以加，比如**禁止用户输入大于当年年月的日期**，用angular的watch方法就很容易实现


	.directive('validateMonth', function () {
    	return function (scope, element, attrs) {
        scope.$watch(attrs.ngModel, function(newValue, oldValue) {
            if (!newValue) {
                return;
            }
            if (newValue > 12) {
                scope.customerDetail.month = oldValue;
            }
        })
    	};
	})

在directive上添加watch事件，可以禁止用户输入限定条件之外的内容。原理也很简单，当超过限制条件后，用旧的value替换新的value。

替换速度很快，肉眼不可见，就达到了“禁止输入”的效果。

### 其它功能

还有一些跟日期输入有关的效果，类似onblur的时候自动补全0，输入两个数字后自动跳到下一个input输入框，不管使用jquery还是angular都很容易实现，此处不再赘述。



