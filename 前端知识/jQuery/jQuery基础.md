[TOC]

# jQuery

流行的js 类库

## 核心

主要功能：javascript开发人员查找元素、操作DOM、处理事件、执行动画和开发Ajax的操作。


jQuery 优势：(宗旨：write less ，do more 写更少的代码，做更多的事情)

### 优点

* 轻量级 （js 库非常小）
* 强大的选择器（获取页面上面的dom 元素document.getElementById()
       操作dom 必须先得到dom）
* 出色的DOM操作的封装
* 可靠的事件处理机制(对事件进行了一个封装)
* 完善的Ajax（底层封装xmlhttprequest）
* 不污染顶级变量（在jquery 里面只有一个对象   jQuery  == $）
* 出色的浏览器兼容性
* 链式操作方式($("#ddd").addClass().removeClass())
* 隐式迭代 (显示迭代:迭代一个数组)
  显示迭代（for(var i=0;i<array.length;i++){}）
  隐身迭代屏蔽掉for 操作
* 行为层与结构层的分离
* 丰富的插件支持  后期扩展非常方便
* 完善的文档

## jQuery与dom

jQuery 只有一个对象 （jQuery == $） 

jQuery 与dom 对象之间的区别与联系：

* dom对象：dom 对象 是浏览器自带对象，dom 对象只能调用dom 里面的属性和方法，
  不能调用jQuery 对象里面的属性和方法

* jQuery对象：jquery 对象是通过jQuery 包装页面上面的元素或者dom
  而产生的一个新的 对象，jquery 对象时jQuery 独有的，不能调用dom
   对象里面的属性和方法，jQuery 对象是一个数组。

* jQuery 对象与dom 对象时可以相互转换的，转换之后它们就可以相互调用了

## 九大选择器

### 基本选择器

* 选择id为one的元素

   $("#one").css("background","red");

* 选择class为one的元素

  $(".mini").css("background","yellow");

* 选择元素名为div的元素

  $("div").css("background","green");

* 选择所有的元素

  $("*").css("background","blue");

* 选择所有span元素 和 id为two的元素

  $("span,#two").css("background","blue");

### 层次选择器

* 选择body内的所有div元素

  $("body div").css("background","blue");

* 在body内,选择子元素是div的

  $("body>div").css("background","blue");

* 选择id为one 的下一个div元素

  $("#one+div").css("background","blue");

* 选择id为two的元素的所有div兄弟元素

  $("#two").siblings("div").css("background","blue");

### 基本过滤选择器

* 选择第一个div元素

  $("div:first").css("background","red");

* 选择最后一个div元素

  $("div:last").css("background","green");

* 选择class不为one的所有div元素

  $("div:not('.one')").css("background","green");

* 选择索引值为奇数的div元素

  $("div:odd").css("background","green");

* 选择索引值等于3的元素

  $("div:eq(3)").css("background","green");

* 选择索引值大于3的元素

  $("div:gt(3)").css("background","green");

* 选择索引值小于3的元素

  $("div:lt(3)").css("background","red");

* 选择所有的标题元素

  $(":header").css("background","red");

* 选择当前正在执行动画的所有元素

  $("#mover").slideToggle("slow",move);

### 内容过滤选择器

* 选取含有文本“di”的div元素

  $("div:contains('di')").css("background","red");

* 选取不包含子元素(或者文本元素)的div空元素

  $("div:empty").css("background","red");

* 选取含有class为mini元素的div元素

  $("div:has('.mini')").css("background","red");

* 选取含有子元素(或者文本元素)的div元素

  $("div:parent").css("background","red");

### 可见性过滤选择器

* 选取所有可见的div元素

  $("div:visible").css("background","red");

* 选取所有不可见的元素, 利用 jQuery 中的 show() 方法将它们显示出来

  $("div:hidden").css("background","red").show(2000);

* 选取所有的文本隐藏域, 并打印它们的值

  $("input:hidden")

* 选取onediv所有的div, 并打印它们的值

  ```js
  $("#b4").click(function(){
       $("#onediv>div").each(function(index,dom){
     alert($(dom).text());
    })
  });
  ```

### 属性选择器

* 选取含有属性title的div元素

  $("div[title]").css("background","red");

* 选取属性title值等于“test”的div元素

  $("div[title=test]").css("background","red");

* 选取属性title值不等于“test"的div元素(没有属性title的也将被选中)

  $("div[title!=test]").css("background","red");

* 选取属性title值以“te”开始 的div元素

   $("div[title^=te]").css("background","red");

* 选取属性title值以“est”结束 的div元素

  $("div[title$=est]").css("background","red");

* 选取属性title值 含有“es”的div元素

  $("div[title*=es]").css("background","red");

* 组合属性选择器,首先选取有属性id的div元素，然后在结果中 选取属性title值 含有“es”的元素

   $("div[ id ] [ title*=es ]").css("background","red");

### 子元素过滤选择器

* 选取每个class为one的div,父元素下的第2个子元素

  $("div[class=one] :nth-child(2)").css("background","red")

* 选取每个class为one的div, 父元素下的第一个子元素

  $("div[class=one] :first-child").css("background","red");

* 选取每个class为one的div父元素下的最后一个子元素

  $("div[class=one] :last-child").css("background","red");

* 如果class为one的div父元素下的仅仅只有一个子元素，那么选中这个子元素

  $("div[class=one] :only-child").css("background","red");

### 表单对象属性过滤选择器

* 对表单内可用input 赋值操作

  $("input:enabled").val("卢雨");

* 对表单内不可用input 赋值操作

  $("input:disabled").val("卢雨");

* 获取多选框选中的个数

  alert($("input:checked").size());

* 获取下拉框选中的内容

  ```js
  $("select>option:selected").each(function(index,dom){
        var title=$(dom).attr("title");
        alert(title);
  })
  ```

### dom 操作 (节点的查找)

页面上面的元素分为三种类型的节点

1：元素节点 （9 大选择器都是用来找元素节点）

2：属性节点 （先找到元素节点，然后调用attr()）

3：文本节点   (先找到元素节点然后调用text())

节点的创建： 元素节点的创建，属性节点，文本节点

### 节点操作

* 删除节点

  remove 删除当前节点以及下面所有的子节点..

  $("#wangyunfei").remove();

  清空.. 清空当前节点下面的子节点..

  $("#wangyunfei").empty();

* 克隆节点

  克隆匹配的 DOM 元素, 返回值为克隆后的副本. 但此时复制的新节点不具有任何行为.

   var p= $("p").clone();

  复制元素的同时复制元素里面的事件

  var p= $("p").clone(true);

* 取当前节点的文本节点以及子节点的文本节点
  alert($("div").text());

* 获取当前节点里面的html 内容
  alert($("div").html());
  alert( $("div").height());

* 获取某个属性的值
  alert($("div").attr("title"));


### 样式操作

* addClass 往当前元素上面追加一个样式

  操作样式的第一种方式，优先第一种

  $("div").addClass("cssbaidu")

  $("div").attr("class","cssbaidu")

  $("div").css("font-size","28");

  注意：一个元素的class 属性可以对应有多个值，（即可以叠加）

* 如果不加参数，删除当前元素上面的所有的class 值

  $("div").removeClass();

  如果加参数，根据参数名称删除指定 class

  $("div").removeClass("csshaosou");

* 判断当前元素是否具备某个样式

  var flag=$("div").hasClass("cssbaidu");

  alert(flag);

* 切换样式：控制样式上的重复切换.如果类名存在则删除它, 如果类名不存在则添加它。

  $("div").toggleClass("cssbaidu");

*  toggle  切换

  调用toggle 首先会先判断当前元素是否是隐藏状态

  如果是隐藏状态，则调用jQuery 的show方法显示

  如果是显示状态，则调用jQuery 的hide 方法隐藏

  $("div").toggle(3000);

### 遍历节点

* 获取当前节点下面的子节点

  alert($("ul").children().length);

* 获取当前节点的下一个（上一个节点）

  next();  prev();  

  alert($("#xiaoli").next().next().text());		

* 获取当前节点的兄弟节点

  alert($("#xiaoli").siblings("li").size());

### 包裹节点

* 将匹配到的元素使用指定标签进行逐个包裹...	

  $("p").wrap("< font color='red' >< font >");

* 将匹配到的元素使用指定标签进行整体包裹...	

  $("p").wrapAll("< font color='red' >< /font >")

* 将匹配到的元素使用指定标签进行进行内部逐个包裹...	

   $("p").wrapInner("< font color='red' >< /font > ")

## jQuery 事件

* 页面加载完毕执行 

  window.onload=function(){

  }

* 页面加载完毕执行 

  $(function(){

  })

* $().ready(function(){

  })

区别：window.onload 与 $(function(){}) 都是用来作于界面渲染完毕之后的初始化操作

​	window.onload 需要等待页面上面所有的元素都绘制完毕之后才执行，包含图片。

​	$(function(){}) 页面上面所有的dom 元素绘制完毕之后就执行，不包含图片。

```js
 $("#bindbutton").click(function(){
    $("div").bind("mouseover",function(){
      alert("a");
    })
})
$("#unbindbutton").click(function(){
//解绑所有的事件
    $("div").unbind();
      $("div").unbind("mouseover");
    })
```

## Jquery与Ajax

格式如下：

```js
jQuery(function(){
		$("#ajaxbutton").bind("click",function(){
			var options={
				url:"../../ajaxServlet",
				type:"POST",
				//data:"username=aa",//提交数据的第一个格式..  字符串
				//请求数据的第二种格式... 一般我们使用第二种
				data:{
					username:"aa"
				},
				success:function(data){
					alert(data);
				},
			//	dateType:"",//服务端预期的数据返回类型...
				timeout:"3000",//请求超时的时间
				error:function(){//请求失败时的一个回调函数..
					//可以给用户号的提示...
				}
			}
			$.ajax(options)
	})
})
```

* 如果有参数的时候，则使用post 方式提交
* 如果没有请求参数，则默认使用get 方式提交
* load方法它一般都是用于去载入一个静态的页面

## jQuery的getJson和getScript

* 通过ajax方法提交的时候有两种数据格式：字符串、json

* jQuery解析json时要求key上面有双引号！

* 序列化，将表单里面的选项序列化为一个字符串
  var data_d=$("#form1").serialize()
   序列化成一个数组
  var data_d=$("#form1").serializeArray()

* 在调试代码时，我们使用alert时，alert只能显示字符串等，不能显示详细的信息；
  此时浏览器给我们提供了一个对象console.info(data_d)可以打印里面更加详细的信息；

* ajax异步加载案例：

  当用户第一次访问界面的时候不需要加载页面上的全部资源，当用户想看的时候去加载

## jQuery接口

jQuery对外提供了一些接口，可以让我们来扩展jQuery，定义自己的功能

* 全部方法的插件

  validateUsername("")

* 局部方法的插件

  $("#diao").datagrid({})

* 选择器插件

## 问题总结

* jQuery返回的是数组，需要给出下标
  $("#loginReturnMsg")[0]

* response为{"data":null,"msg":"用户名或密码错误","status":400}时，

  ```
  //获取msg
  success:function(getData){
      var response=eval(getData);
      alert(response.msg);
  }
  ```






