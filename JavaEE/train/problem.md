* 为什么要放在WEB-INF中？

外界不可访问，如果不放在里面，例如用户未登录，直接访问要拦截时，会闪过要访问的页面。



* 技术

JavaSE基础、SSM框架、git、svn、 maven（项目）、SSH框架（学习）、第三方框架、spring boot（三大框架的简化开发）



* postman



* 删除用户后不能直接调用list方式

因为如果在list中转发到list页面，地址栏不会改变，若此时再刷新一次，将会重复删除。(若使用了iframe框架，也可以直接调用)



* post和get乱码问题

```java
response.setContentType("text/html;charset=UTF-8");
request.setCharacterEncoding("UTF-8");
```

以上代码要写在doPost和doGet中获取第一个参数的前面!!!!!



用户分页模块

商品类别模块