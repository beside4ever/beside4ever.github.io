---
layout: post
title: "Angular JS 学习小记"
date: 2015-07-14 23:58:15 +0800
comments: true
categories: javascript
---
## 概述 ##

Codecademy新开了Angular的课程，赶紧去入门了一下，以前一直没接触，现在工作正好需要用。把教程里的东西简要归纳一下，供以后回顾使用。

## 基础 ##

### 简介 ###

Angular JS是一种为了加快前端开发效率的javascript web框架。

### 工作流 ###

一个典型App的文件目录可能如下：

- app.js 项目入口文件，定义App
- index.html 模板文件，用ng-*属性定义和angular有关的绑定
- MainController.js 控制器文件，定义Angular中的控制器

<!--more-->

### 双向数据绑定 ###

在MainController.js中，定义控制器如下：

	app.controller('MainController',
       ['$scope', function($scope) {
         $scope.title = 'Top Sellers in Books Likes';
         $scope.promo = 'What is this';
         $scope.product = {name: 'The Book of Trees', price: 19};
       }]);

在index.html中，数据绑定部分的代码段如下：

	<div class="main" ng-controller="MainController">
      <div class="container">
        <h1>{{ title }}</h1>
        <h2>{{ promo }}</h2>
        <div class="col-md-6">
          <div class="thumbnail">
            <img src="img/the-book-of-trees.jpg">
            <p class="title">{{product.name}}</p>
            <p class="price">{{product.price}}</p>
            <p class="date"> </p>
          </div>
        </div>
      </div>
    </div>

这里的scope即是angular中的"作用域"概念。在html中，标示`ng-controller="MainController"`的div内部就是`MainController`这个控制器的作用域，可以使用该作用域内的变量`title`,`promo`,`product`。

所谓双向数据绑定，也就是说，控制器内的变量变化会直接反映到html上，同样html上的数据变化也会返回到控制器中。

## 过滤器 ##

前面的输出价格部分的代码如下：

	{{product.price}}

如果我们希望价格不是纯数字数据，而是带货币符号，且有2位小数，这个格式我们可以通过`currency`过滤器来实现：

	{{product.price | currency}}

于是价格从`19`变成了`$19.00`。

`|`符号是Angular中的”管道“，这让我想到了linux中的管道，还是一样的配方，还是熟悉的味道，因此理解为附加操作就可以了。Angular中有很多内置过滤器，可参见：

[https://docs.angularjs.org/api/ng/filter](https://docs.angularjs.org/api/ng/filter)

## 循环 ##

我们在控制器里数据绑定可以绑定一个数组，为了遍历数组元素，可以使用`ng-repeat`来进行循环。基本语法如下：

	<div ng-repeat="product in products">
	  <p>{{ product.name }}</p>
	  <img ng-src="{{ product.cover }}">	  
	</div>

注意这里还用了`ng-src`属性来替代img原有的`src`属性。

## 指令 ##

### 内置指令 ###

前面提到的`ng-repeat`,`ng-controller`,`ng-src`等等都是AngularJS中的内置指令。指令用来指明AngularJS的大部分内置操作。前面我们所写的代码都是用Angular的方式展现静态数据，下面我们用指令来完成Angluar中的动态交互。

我们可以在控制器中，为作用域设定一个函数：

	$scope.plusOne = function(index) {
		$scope.products[index].likes += 1;
	}

在html中绑定如下：

	<p ng-click="plusOne($index)">+ {{product.likes}}</p>

此处有两个要点：

1. 通过`ng-click`指令绑定`<p>`元素的点击事件，就像jQuery中的`.click(function(){});`。
2. 由于本例中，参与交互的元素是在循环中遍历的，因此点击时需判断对应元素的索引，在调用时传入的索引为`$index`。

### 自定义指令 ###

我们可以在angular中自定义一个自己的指令：

	app.directive('appInfo', function() {
	  return {
	    restrict: 'E',
	    scope: {
	      info: '='
	    },
	    templateUrl:
	    'js/directives/appInfo.html'
	  };
	});

我们自定义了一个`appInfo`的指令，这个指令返回一个有三个属性的对象：

- `restrict`属性指明指令的使用方法。`E`表示其会被当做HTML新元素使用。
- `scope`属性表明我们将通过这个指令的`info`属性来传递数据。`=`表示指令将会寻找`info`属性，像这样：`<app-info info="shutterbugg"></app-info>`
- `templateUrl`指定需要展示数据的html路径。

在`appInfo.html`中，我们可以设定具体的数据展现方式，相当于一个子模板:

	<img class="icon" ng-src="{{ info.icon }}">
	<h2 class="title">{{ info.title }}</h2>
	<p class="developer">{{ info.developer }}</p>
	<p class="price">{{ info.price | currency }}</p>

在`index.html`中，我们通过新指令（相当于创建了一个自定义标签）`<app-info info="shutterbugg"></app-info>`就可以将`MainController`中的`shutterbugg`属性数据传给`app-info`指令的`info`，相当于作用域经过了一次传递。

自定义指令的好处主要在于**可读性**和**可复用性**。

自定义指令还可以绑定函数，其实就相当于动态子模板：

	app.directive('installApp', function() {
	  return {
	    restrict: 'E',
	    scope: {},
	    templateUrl: 'js/directives/installApp.html',
	    link: function(scope, element, attrs) { 
	      scope.buttonText = "Install", 
	      scope.installed = false,
	      scope.download = function() { 
	        element.toggleClass('btn-active'); 
	        if(scope.installed) { 
	          scope.buttonText = "Install"; 
	          scope.installed = false; 
	        } else { 
	          scope.buttonText = "Uninstall"; 
	          scope.installed = true; 
	        } 
	      } 
	    }
	  }

在这段代码中，我们给`installApp`指令设定了两个`scope`的初始属性`buttonText`和`installed`，同时设定了一个函数`download`。在HTML中，我们把这个函数绑定给`button`，就相当于在子模板中设定了点击规则：

	<button class="btn btn-active" ng-click="download()">
	  {{ buttonText }}
	</button>

## 服务 ##

指令解决了MVC中视图层和控制器交互的问题，那么数据层如何传递数据给控制器呢？这里就需要用服务来解决。

首先，我们创建一个如下的服务：

	app.factory('forecast', ['$http', function($http) { 
	  return $http.get('http://s3.amazonaws.com/codecademy-content/courses/ltp4/forecast-api/forecast.json') 
	            .success(function(data) { 
	              return data; 
	            }) 
	            .error(function(err) { 
	              return err; 
	            }); 
	}]);

`factory`函数用来创建一个新服务。在这个服务中，我们需要调用angular内置服务`$http`，因此我们在创建服务的时候进行了依赖调用。在服务内部，我们利用`$http.get`方法（类似jQuery中的`ajax.get`访问一个api地址，获取数据进行返回。

接下来，在控制器中，我们同样需要调用这个我们自建的服务：

	app.controller('MainController', ['$scope', 'forecast', function($scope, forecast) {
	  forecast.success(function(data) {
	    $scope.fiveDay = data;
	  });
	}]);

在控制器中，我们通过`forecast`服务异步获取数据，返回值赋值给`$scope`，这样就可以映射到视图层中了。

## 路由 ##

路由用来分离不同业务逻辑下，不同视图的路径问题。见以下代码：

	var app = angular.module('GalleryApp', ['ngRoute']);
	app.config(function ($routeProvider) { 
	  $routeProvider 
	    .when('/', { 
	      controller: 'HomeController', 
	      templateUrl: 'views/home.html' 
	    }) 
	    .when('/photos/:id', {
	      controller: 'PhotoController',
	      templateUrl: 'views/photo.html'
	    })
	    .otherwise({ 
	      redirectTo: '/' 
	    }); 
	});

在这个路由中，有三条路径：

1. 用户访问根目录`/`，对应控制器为`HomeController`，视图为`views/home.html`
2. 用户访问目录`/photos/`，对应控制器为`PhotoController`，视图为`views/photo.html`。在这个控制器中，我们用`$routeParam`服务去取页面参数`id`，利用这个参数进行实际操作。
3. 如果用户访问的目录在这两个目录之外，则重定向到`/`目录对应的控制器和视图。

整个`$routeProvider`语法比较类似于常见的`switch`，而`when`就相当于`case`，`otherwise`相当于`default`。