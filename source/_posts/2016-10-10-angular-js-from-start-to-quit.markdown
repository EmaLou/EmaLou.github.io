---
layout: post
title: "AngularJS-从入门到放弃"
date: 2016-10-10 10:29:11 +0800
comments: true
categories: 
---

##**入门angular**

AngularJS是一个比较成熟的前端框架，笔者自己用过一段时间，还记得刚从刀耕火种的纯手写JS切换到当年还是高大上的angularJS的时候，兴奋程度无以复加，以前写20行的代码，后来只用两行，感觉世界都变得美好了。这里要说下20行代码是如何变成2行的（熟悉angular的客官们请移步“放弃”阶段）

angularJS是一个**前端MVVM**（Model-View-ViewModel）框架，实现了数据、视图、逻辑的分离。来看一个例子：

		index.html
		<div>
			<p>dinner: {{cookbook.potato}}
		</div>


		controller.js
		function cookbookController($scope) {
			$scope.cookbook = {potato: '酸辣土豆丝'};
		}

		output:
		dinner: 酸辣土豆丝


html的部分就是一个简单的视图层，而controller.js中的cookbook就是model-存储数据的地方，本例中逻辑很简单，所以并没有发挥controller的作用。但实际开发中，由于网页中功能庞杂，且与后台交互越来越密切，所以controller中的逻辑复杂程度丝毫不亚于后端。

angular还有一个重要的功能是**双向绑定** - 同一个变量，不管在页面中还是在js中对它进行修改，其更改都会在另一方生效。如上例所示，如果controller中的cookbook增加了一个菜谱，或者修改了原有的菜谱，其修改都会立刻反应到前端页面。


当应用程序需要前后端交互的时候，使用angular无疑是一个很好的选则。试想一下，当你需要在前端页面操作一些东西，而操作的内容需要实时同步到服务端并写入数据库，用angular就可以这样实现

		index.html:
		<div>
		   <input ng-change="syncToServer()"> {{$scope.text}}</input>
		</div>

		controller.js:
		$scope.syncToServer = function(){
			$http.post('/serverUrl', $scope.text, config).then(successCallback, errorCallback);
		}


简单起见，这里并没有将service的部分从controller中抽出来，在实际开发中，一般会将service的部分抽出来放在一个单独的js文件中，这样做是为了让前端业务逻辑和与后台交互的部分分开。同时，angular中的service的逻辑都比较简单，很多时候只是为了给后台发请求，这种时候单独的service可以大大提高代码复用性。

这里的**ng-change**是angular提供的**directive**，它可以监测当前元素的变化，并在元素发生变化的时候调用syncToServer函数，从而实现数据同步。类似的，angular还实现了很多其它directive帮我们做各种常用的工作，常用的有：

- ng-bind: 将scope中的变量绑定到页面上
- ng-controller: 绑定controller到当前元素
- ng-repeat: 监测循环，需要更新列表的时候很方便
- ng-click: 监测点击事件
- ng-class: 绑定CSS class
- ng-focus: 监测focus事件

更多内容，请参考angular [官方文档](https://docs.angularjs.org/api/ng/directive/)

除了强大的默认directive，angular还支持自定义directive，这个功能让angular如虎添翼，你可以写出自己的“HTML元素”！

除此之外，angular还有很多好用的插件，比如选色器，日历等，用的时候再网上下一个，还是很方便的。

可见，angularJS的功能确实强大，所以当前段时间需要起一个有很多前端交互功能更的新项目时，前端部分首选的就是angular。


##**放弃angular**

在搭完前端框架之后，使用angular开发，起初觉得很好用，大家都在飞速的写各种directive，让代码模块化。一片欣欣向荣的景象。但是在项目第一个MVP release之后，开始加新功能的时候，隐隐觉得有些吃力...

####**directive不那么好用了**

为了将各种大大小小的功能模块化，我们写了大量directive，其中不乏嵌套directive（在一个directive中使用另一个directive）。那么这时就牵扯到变量在directive中互相传递的问题，所以当一个父directive中的变量需要在子directive中执行一些操作，并将操作结果返回到父directive时，代码就变得复杂了，逻辑也更难以掌控了。

为了让代码更加模块化，基本每一个directive都会配备一个controller来处理其相应的逻辑。对于功能类似的模块来说，不仅directive“看似”重复，controller也看似重复。

同时，大量的directive也增加了查找和添加新directive的难度。

####**controller太过复杂**
由于前端逻辑复杂，controller的数量不仅巨大，而且互相之间交互也很多，经常会有变量在两到三个controller中互相传递做交互，这么一来，我们会发现一个变量在多个地方被修改，而一个地方的修改依赖另一个地方的修改，原本模块化的代码变得耦合度很高。


####**效率问题开始出现**
还记得上面讲到的很多angular提供的好用的directive吗？起初用起来很方便，但如果代码中充斥着大量的ng-xx,执行效率会变得很低。其原因就在于，每个ng-xx背后都有一个监听事件，它会监听浏览器中事件的变化，然后修改对应的model，调用对应的事件。我们都知道，写js的时候要尽量少用监听事件，因为这些监听事件都会不断消耗浏览器的资源。在angular中，每触发一个事件，背后都可能调用了上百个函数，执行效率和自然会降低，尤其是当有很多这样的监听事件的时候。

经验教训告诉我们，当前后端有大量数据交互，并且前端逻辑及其复杂的情况下，一定要小心谨慎的使用angular，多问自己为什么。也许当前的实践并不是最好的，follow下去会让项目越来越难以维护。


##**怎么才能不放弃angular?**

为了更好在项目中使用angularJS这个强大的工具，在开发中要注意：

- controller中的功能要尽量独立，防止后期功能太复杂加强controller之间的耦合
- 减少双向绑定（eg：当需要显示一些不会改变的内容，如title，dashboard等，尽量不用双向绑定，angularjs在1.3之后的版本中加入了单次绑定的功能，通过{{::data}}可以实现单次绑定。这样可以减少数据监听，提升性能。）
- 在需要多个controller对同一个数据进行修改的时候，可以使用service来存储数据，而每个需要修改数据的controller只是简单的从service中读取数据，修改，再放回service

