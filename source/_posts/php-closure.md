---
title: 浅谈php闭包
date: 2018-09-20 15:36:56
tags:
---
在 js 和 php  中定义一个函数大同小异，需要提供 function  和函数名去定义。类似
```php
<?php

function foo()
{
    return 123;
}
```
当然我们也可以用匿名函数的方式去定义。

匿名函数：顾名思义，没有名称的函数。如下：
```php
<?php

$func = function()
{
    echo  123;
};
// 调用
$func(); // 输出123
```
匿名函数的玩法很多，我们可以将匿名函数：

 - 在普通函数中当做参数传入
```php
<?php

// 匿名函数当作参数传入
$func = function()
{
    echo 123;
};

function foo($param)
{
    $param();
}

foo($func);
```
 - 在普通函数中当作返回值返回
```php
<?php

function foo()
{

    $func = function () {
        return 123;
    };

    return $func;
}

$bar = foo();

echo $bar();
```
综上所述，一个匿名函数作为普通函数的返回值或者参数的时候，这就实现了一个闭包。

闭包内如何访问外部变量？答:用 **use**
```php
<?php

$a = 'foo';

$b = 'bar';

function test($param1,$param2,$dataHook = null){

    echo $param1;

    echo $param2;

    $dataHook('hello');
}

test(1,2,function($data)use($a,$b){

	echo $data;

	echo $a;

	echo $b;
});// 12hellofoobar

```

> 这里使用闭包的好处就是逻辑统一由闭包处理，调用的地方可以自定义闭包处理逻辑，类似于依赖注入。
