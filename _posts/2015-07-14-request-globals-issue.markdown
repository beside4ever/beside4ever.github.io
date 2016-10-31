---
layout: post
title: "检测$_REQUEST['GLOBALS']以处理register_globals问题"
date: 2015-07-14 23:57:55 +0800
comments: true
categories: php
---
全局php包含文件中最前面的代码：

    if (isset($_REQUEST['GLOBALS']) || isset($_FILES['GLOBALS'])) {
    	die('GLOBALS overwrite attempt detected');
    }

这段代码的作用是为了在php.ini中，设定register_globals为on的前提下，检测恶意注入变量的行为。

register_globals是php的一个配置指令，开启时将自动把用户提交的值注册成全局变量。比如用户的表单提交了一个`name="username"`的值，那么在后台就可以直接用`$username`取到。

这个配置显然是不安全的，比如用户验证：

    if(##用户验证条件##){
    	$valid = true;
    }

那么我在访问页面的时候可以自己加一个参数如`page.php?valid=1`，就轻松绕过验证了。

<!--more-->

回到开头的代码，同样，如果用户访问的时候设定了`GLOBALS`的值，那么就可以篡改内置超全局变量`$GLOBALS`的值。所以这段代码的目的就是检测用户的恶意篡改，如果有则终止脚本运行。

php4.2之后，`register_globals`配置已经默认为`off`了，5.4之后就直接废弃了这个特性，所以高版本php对这个问题倒不用太过关心。另外获取用户参数的时候也应该直接取`$_POST`,`$_GET`之类，这样就不会产生模糊不清的问题。

## 关于$_REQUEST的两点细节 ##

1. $_REQUEST最早是包含$_GET、$_POST、$_COOKIE、$_FILES的，4.3以后移除了$_FILES.
2. $_REQUEST中的内容并不会随着用户在脚本中修改$_GET/$_POST等元素的值而改变，如下示例：

	$_GET['foo'] = 'a';
	$_POST['bar'] = 'b';
	var_dump($_GET); // Element 'foo' is string(1) "a"
	var_dump($_POST); // Element 'bar' is string(1) "b"
	var_dump($_REQUEST); // Does not contain elements 'foo' or 'bar'

## 参考 ##
- http://php.net/manual/zh/faq.misc.php
- http://php.net/manual/zh/security.globals.php