---
layout: post
title: "Simple Angular Directive"
date: 2014-12-15 20:28:44 +0800
comments: true
categories: 
---

angular directive

angular有很多优良特性，其中很重要的一点是directive
directive,中文翻译是指令。
那么问题来了。。。

问："What are Directives?

答：**At a high level, directives are markers on a DOM element (such as an attribute, element name, comment or CSS class) that tell AngularJS's HTML compiler ($compile) to attach a specified behavior to that DOM element or even transform the DOM element and its children."** -- AngularJS 官方文档

问：说人话

答：“从高层次来说，指令是一些页面元素的标记，这些标记能够让angular编译器为该元素添加特殊行为，甚至可以转化改元素和其子元素。”

简而言之 - 为DOM元素添加事件。

问：javascript也可以添加事件，为什么要用directive呢？

答：简单方便，最重要的是重用程度实在是高，比刀耕火种的js要高端大气上档次很多。

问：show me

答：看好了！

html:

    <input type="text" valid-check ></input>

directive.js:

    regApp.directive('validCheck', function(){
  	  return {
        require: 'ngModel',c
        link: function (scope, element, attr, modelCtrl) {
            modelCtrl.$validators.validCheck = function (modelValue, viewValue) {
                if (!modelCtrl.$isEmpty(viewValue)) {
                if(viewVaule.length !=5)
                   return false;
                 return true;
                }
            }
        }
    }
    })


问：看起来好复杂，求解释

答：第一行定义了一个叫做validCheck的directive，这个东东会返回一个对象，require是这个directive需要绑定的model，link方法里可以定义directive的动作。这个例子中link方法在做验证。通过$validators 对象来给directive绑定验证函数，绑定之后，每次改变输入值都会自动调用验证方法进行验证。modelValue和viewValue分别代表model中的值，和页面上view的值。当viewValue不为空的时候才继续下面的验证。最后返回布尔值。

问：听起来不错，挺好用

答：此处有坑，请注意

问：哦？

答：当link方法返回false的时候，model会变成undefined，model中的方法都不会再执行。意味着false的时候这个input上绑定的方法（ng-change等）都不再执行。初学者要注意这一点
问：总有办法避免吧？

答：聪明！这样写就可以了


    <input type="text" valid-check ng-model-options="{allowInvalid: true}"></input>

当然directive还有很多别的用法，例如绑定键盘事件

    regApp.directive('numbersOnly', function(){
    	return function (scope, element, attrs) {
        element.bind("keypress", function (event) {
            if (event.which < 48 || event.which > 57) {
                scope.$apply(function () {
                    scope.$eval(attrs.numbersOnly);
                });
                event.preventDefault();
            }
        });
       };
    });

上面这段代码就只允许用户输入数字。

directive可以作为元素或者属性添加到DOM中，没错，就像<label> 标签，或者class属性一样。
html:

    <div error-box ></div>

directive.js:

    directive('errorBox', function(){
      return {
    	restrict: 'A';
        templateUrl: 'error-box.html'
      }
    }
restrict: 'A' 表示可以作为元素属性来应用directive，渲染页面之后error-box.html中的内容会填充到div里。

换一种方式：
html:

    <error-box />

directive.js:

	directive('errorBox', function(){
      return {
    	restrict: 'E';
        templateUrl: 'error-box.html'
      }
    }
restrict: 'E'则表示你在angular的帮助下创建了一个html标签，渲染页面时error-box.html里的内容会替换掉<error-box/> 这个标签

所以说，Directive功能如此强大，和data-binding一起作为angular的灵魂当之无愧。
有空好好学学。

答：好！














