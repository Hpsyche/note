[TOC]

![](D:\Work\TyporaNotes\note\前端知识\Ajax\pict\ajax原理.png)

## 原理图

![](D:\Work\TyporaNotes\note\前端知识\Ajax\pict\交互图.png)

XHR：XmlHttpRequest

## 开发步骤

```
<html>
<head>
<script type="text/javascript">
function loadXMLDoc()
{
var xmlhttp;
//1.得到XmlHttpRequest对象
if (window.XMLHttpRequest)
  {// code for IE7+, Firefox, Chrome, Opera, Safari
  xmlhttp=new XMLHttpRequest();
  }
else
  {// code for IE6, IE5
  xmlhttp=new ActiveXObject("Microsoft.XMLHTTP");
  }
//2.设置回调函数
xmlhttp.onreadystatechange=function()
  {
  	if (xmlhttp.readyState==4 && xmlhttp.status==200)
    {
    }
  }
//3.open，method：请求的类型；GET 或 POST，url：文件在服务器上的位置，async：true（异步）或 false（同步），为避免得到的是浏览器的缓存，可向 URL 添加一个唯一的 ID，如：
xmlhttp.open("GET","/ajax/demo_get.asp" + Math.random(),true);
//4.它是用于发送请求的
xmlhttp.send();
}
```

在回调函数中处理数据
	1.XmlHttpRequest对象由一个属性，readyState
		它代表的是XmlHttpRequest对象的状态。
			0.代表XmlHttpReuqest对象创建
			1.open操作
			2.send操作
			3.接收到了相应数据，但是只有响应头，正文还没有接收
			4.所有http响应接收完成

​	2.status
​	由服务器返回的http状态码，如200表示成功

​	3.如需获得来自服务器的响应，使用 XMLHttpRequest 对象的 responseText 或 responseXML 属性。 

对于ajax操作中请求参数的方式，参数设置
	1.对于get请求方式，参数设置（直接在url后面拼接）
	xmlhttp.open("GET","${pageContext.request.contextPath}/ajax?name=123")

​	2.对于post请求方式，参数设置
​	xmlhttp.open("POST","${pageContext.request.contextPath}/ajax")
​	xmlhttp.send("name=123");
​	注意：如果是POST请求方式，还需要一个http请求头
​	xmlhttp.setRequestHeader("content-type","application/x-www-form-urlencoded");

<hr/>

## Ajax案例一

异步判断用户名是否被使用过

## Ajax案例二

异步请求返回数组

## 关于json插件使用
在java中，可以通过jsonlib插件，在java对象与json之间做转换。

关于jsonlib插件使用:
		1.导包(6个包)
		2.将java对象转换成json
			<font color=red>对于数组，List集合，要想转换成json</font>
				<font color=red>JSONArray.fromObject(java对象); ["value1","value2"]</font>
			<font color=red>对于javaBean，Map</font>
				<font color=red>JSONObject.fromObject(javaBean对象); {name1:value1,name2:value2}</font>
		对于json数据，它只有两种格式
			1.[值1,值2,...]  ------>这就是javascript中的数组
			2.{name:value,....} ---->就是javascript中的对象。
			但是这两种格式可以嵌套.
				[{},{},{}]
				{name:[],name:[]}
		如果javaBean中有一个属性，不想生成在json中，怎样处理?
			JsonConfig config = new JsonConfig();
			config.setExcludes(new String[] { "type" });
			JSONArray.fromObject(ps, config).toString();
		上述代码就是在生成json时，不将type属性包含.				

### 省市联动

#### 前端页面

```jsp
<%@ page language="java" import="java.util.*" pageEncoding="UTF-8"%>
<!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN">
<html>
<head>
    <title>ajax开发步骤</title>
    <script type="text/javascript" src="/utils.js"></script>
    <script type="text/javascript">
        var msg;
        window.onload=function(ev) {
            var pro = document.getElementById("province");
            var xmlhttp = getXmlHttp();
            msg = eval(xmlhttp.responseText);
            xmlhttp.onreadystatechange = function () {
                //5.处理响应数据  当信息全部返回，并且是成功
                if (xmlhttp.readyState == 4 && xmlhttp.status == 200) {
                    msg=eval(xmlhttp.responseText);
                    for(var i=0;i<msg.length;i++){
                        var pname= msg[i].name;
                        var option=document.createElement("option");
                        option.text=pname;
                        pro.add(option);
                    }
                }
            }
            //3.open
            xmlhttp.open("GET", "/Ajax4Servlet");
            // xmlhttp.setRequestHeader("content-type","application/x-www-form-urlencoded");
            //4.发送请求 send
            xmlhttp.send(null);
        }

        function fillCity() {
            var province = document.getElementById("province")
            var city = document.getElementById("city");
            city.innerHTML="<option>--城市--</option>";
            var pname=province.options[province.selectedIndex].text;
            for(var i=0;i<msg.length;i++){
                var pElementName=msg[i].name;
                if(pElementName==pname){
                    var cites=msg[i].list;
                    for(var j=0;j<cites.length;j++){
                        var cname=cites[j].name;
                        var option=document.createElement("option");
                        option.text=cname;
                        city.add(option);
                    }
                }
            }
        }
    </script>
</head>
<body>
<select id="province" onchange="fillCity()">
    <option>--省份--</option>
</select>
<select id="city">
    <option>--城市--</option>
</select><br>
</body>
</html>

```

#### 后台代码

```java
@WebServlet("/Ajax4Servlet")
public class Ajax4Servlet extends HttpServlet {
    @Override
    protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {

    }
    @Override
    protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        City city1=new City("1");
        City city2=new City("2");
        City city3=new City("3");
        List<City> list=new ArrayList<>();
        list.add(city1);
        list.add(city2);
        list.add(city3);
        Province p1=new Province("广东",list);

        City city4=new City("4");
        City city5=new City("5");
        City city6=new City("6");
        List<City> listt=new ArrayList<>();
        listt.add(city4);
        listt.add(city5);
        listt.add(city6);
        Province p2=new Province("广2",listt);
        List<Province> list1=new ArrayList<>();
        list1.add(p1);
        list1.add(p2);

        String json=JSONArray.fromObject(list1).toString();
        response.getWriter().write(json);
        response.getWriter().close();
    }
}
```

