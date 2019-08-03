1. 钩子函数：Created可以用在权限控制一块

2. @click、:alt（属性绑定）

3. 生命周期中actived用在冒泡清除

4. blog-post与@mount的父子组件关系

5. 过滤（比如在 text加上 日志时间）

6. v-model.trim的使用

7. v-slot插槽的使用

   一般获取对象属性用props，但如果需要做更多的操作（如设置style，可以使用v-slot）

8. element-ui的使用

   * 注意版本问题
   * 显示气泡的方法

9. 双向绑定的实现：

   监听者模式中的发布订阅模式

   在defineProperty中劫持对象的get、set方法，实现双向绑定；

   数据变动时发消息给订阅者，触发相应的监听事件

10. let与var的区别

    * var 和 let 第一点不同就是 let 是块作用域，即其在整个大括号 {} 之内可见。如果使用 let 来重写上面的 for 循环的话，会报错：

      ![](D:\Work\TyporaNotes\note\前端知识\vue学习\pict\vue理解1.jpg)

      ![](D:\Work\TyporaNotes\note\前端知识\vue学习\pict\vue理解2.jpg)

    * let 和 var 的第二点不同是，在变量声明之前就访问变量的话，会直接提示 ReferenceError，而不像 var 那样使用默认值 undefined:

      ![](D:\Work\TyporaNotes\note\前端知识\vue学习\pict\vue理解3.jpg)

11. let与const对比

    const 和 let 的作用域是一致的，不同的是 const 变量一旦被赋值，就不能再改变了。

    一般来说，在Vue中，我们可以只用来声明常量，其它情况下一律使用 let 关键字。