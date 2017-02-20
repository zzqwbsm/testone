# testone
第一个测试项目
Try to modify
---
title: js的Scoping和Hoisting
categories:
- JS
tags:
- javascript
- 阶段一
---


-----------
## <i class="menu-item-icon fa fa-fw zzq_cion" style="background: #624116;border-radius: 50%;height: 16px;width: 16px;"> </i>   作用域（Scoping）

在ES5中，js只有两种形式的作用域：全局作用域和函数作用域。

函数对象作用域跟c的局部变量作用域是不同的，它的作用域是整个函数范围，不论他是在函数的任意位置申明的！这就是所谓的hoisting，也就是变量提升的概念。


## <i class="menu-item-icon fa fa-fw zzq_cion" style="background: #624116;border-radius: 50%;height: 16px;width: 16px;"> </i>   不一样的值

首先，可以试一试这个js代码的输出是怎样？

```javascript

var fo = 1;
function bar_fo() {
	if (!fo) {
		var fo = 10;
	}
	console.log(fo);
}
bar_fo();
```
一般的思路，我们知道
 - 外层的fo赋值了 = 1
 - 由于内层有fo（根据作用域链，此时是外层传递的），所以 if不会执行
 - 所以所以因该输出 1

但是你去执行，你会发现奇怪的是，它输出的值既然是 10

## <i class="menu-item-icon fa fa-fw zzq_cion" style="background: #624116;border-radius: 50%;height: 16px;width: 16px;"> </i>   在探 var声明定义 和 Hoisting

可能有的老司机已经发现了，“var fo = 1”这个地方了，他就是导致这个不一样的值得罪魁祸首。
这也是我们需要理解Hoisting的地方

我们来看

```javascript

var fo; //这个是“变量的声明”
仔细理解“变量声明”，它是指解析器开辟了一个空间，至于里面到底放什么，并不知道。

var fo = 1;//这个是“定义变量”也叫做 初始化变量
它在变量声明的基础上 定义好了里面的东西是什么。
所以应该是两个步奏的合体
var fo;  和  
fo = 1;
```


那么上面的js可以翻译成这样了
```javascript

var fo = 1;
function bar_fo() {
	var fo ;   //奇怪 为什么会在这里呢？？？
	if (!fo) {
		// var fo;不是应该在这里吗？？？
		fo = 10;
	}
	console.log(fo);
}
bar_fo();
```

这里就是要说的Hoisting了，作用区域内变量的声明也会提前。虽然var fo = 10是在下面定义的，所以var fo会自动提前到“该作用域”的顶部。
所以当时fo值为 undefined，于是 if 语句条件判断结果为真，接着为新的变量 fo 赋值为 10。


再看看以下代码
```javascript
var fo = 1;
function bar_fo() {
	console.log(fo);
	var fo = 10;//当有这段代码时候,输出的是undefined，
	//其实就是声提前了。而没有的时候输出 1，此时就是 外层的fo的值了
}
console.log(fo);
bar_fo();

```

所以，函数作用域的“顶部”，所以我们在写代码的时候，一定要将声明变量放在作用域的顶部声明。

js提供了4种方法，可以让命名进入作用域，都会触发Hosting（有优先级）
 - 1、语言定义的命名：比如 this 或者 arguments，它们在所有作用域内都有效且优先级最高，所以在任何地方你都不能把变量命名为 this 之类的，这样是没有意义的
 - 2、形式参数：函数定义时声明的形式参数会作为变量被 hoisting 至该函数的作用域内。所以形式参数是本地的，不是外部的或者全局的。当然你可以在执行函数的时候把外部变量传进来，但是传进来之后就是本地的了
 - 3、函数声明：函数体内部还可以声明函数，不过它们也都是本地的了
 - 4、变量声明：这个优先级其实还是最低的，不过它们也都是最常用的


## <i class="menu-item-icon fa fa-fw zzq_cion" style="background: #624116;border-radius: 50%;height: 16px;width: 16px;"> </i>    Hosting 只提前了命名，没有提前定义

在之前的案例中，var fo = 10;中，Hosting只是触发了 var fo;将它提前到作用域最顶端，而没有触发fo = 10;所以他 还是原来的计算位置。

再看看这两个
```javascript
+function(){
	foo();
	console.log(typeof foo);//undefined
    function foo() {
        console.log("可以执行");
    }
    console.log(typeof foo);//function
}();

```

```javascript
+function(){
	foo();
	console.log(typeof foo);//function
     var foo = function() {
        console.log("不能执行");
    }
    console.log(typeof foo);//function
}();

```

为什么会出现这种情况呢？

这里就可以看出，这两种声明方式不同了。在刚刚说的4种方式中，前者是：函数声明的方式，而后者是：变量声明 的方式。
当函数声明时:声明就会被提升，而且解析器这个时候是知道foo是一个函数（因为是函数声明）！所以在执行 foo() 前，作用域就知道函数 foo 的存在了。
当函数声明时:提升的仅仅是变量名 foo,因此在执行 foo() 之前，作用域只知道 foo 的命名，不知道它到底是什么，所以执行会报错（通常会是：undefined is not a function）。这叫做函数表达式（Function Expression），函数表达式只有命名会被提升，定义的函数体则不会。


这里也看得出：***Hosting 只提前了命名，没有提前定义！***


文章参考了 n͛i͛g͛h͛t͛i͛r͛e͛ 和 Ben Cherry 文章。在此基础上加入了学习文章后对的一些个人理解，如有兴趣请移步原作，[点击](http://www.adequatelygood.com/JavaScript-Scoping-and-Hoisting.html)，感谢。




在ES6中，新增了一个块级作用域（最近的大括号涵盖的范围），但是仅限于let方式申明的变量。
