Nginx的常用策略：

​    1.轮询（默认）

​    2.权重

​    \3. ip hash

​    \4. url hash(第三方插件)

​    \5. fair（第三方插件)）



nginx与apache区别：

最核心的区别在于 Apache 是同步多进程模型，一个连接对应一个进程；Nginx 是异步的，多个连接（万级别）可以对应一个进程。

#### Nginx 相对 Apache 的优点

- 轻量级，同样起 web 服务，比 Apache 占用更少的内存及资源
- 抗并发，Nginx 处理请求是异步非阻塞的，而 Apache 则是阻塞型的，在高并发下 Nginx 能保持低资源低消耗高性能
- 高度模块化的设计，编写模块相对简单
- 社区活跃，各种高性能模块出品迅速啊

#### Apache 相对 Nginx 的优点

- rewrite，比 Nginx 的 rewrite 强大
- 模块超多，基本想到的都可以找到
- 少 bug，Nginx 的 bug 相对较多
- 超稳定

存在就是理由，一般来说，需要性能的 web 服务，用 Nginx。如果不需要性能只求稳定，那就 Apache 吧。后者的各种功能模块实现得比前者，例如 ssl 的模块就比前者好，可配置项多。

epoll(freebsd 上是 kqueue)网络 IO 模型是 Nginx 处理性能高的根本理由，但并不是所有的情况下都是 epoll 大获全胜的，如果本身提供静态服务的就只有寥寥几个文件，Apache 的 select 模型或许比 epoll 更高性能。当然，这只是根据网络 IO 模型的原理作的一个假设，真正的应用还是需要实测了再说的。

最后 Nginx 和 Apache 的差异总结成一句话就是：“Nginx 适合处理静态请求和反向代理，Apache 适合处理动态请求”。但这个差异化只有在请求量达到一定的阈值时表现差异才能表现出来，对于 WordPress 、 Typecho 等等这里动态站点来说某一天流量达到这个阈值的时候，还可以部署 LNMPA 这样的生产环境来应对和解决。所以流量阈值需求不到的时候，选择 Nginx 是性价比最好的选择了。





本文将要介绍的Nginx+Apache结构，其实就是Nginx做前端，Apache做后端，充分发挥他们各自的优势之处。Nginx对于高并发性能出众，Proxy功能强效率高，占用系统资源少，而Apache在高并发时对队列的处理比FastCGI（Nginx需要通过fastcgi等方式运行php）更好，并且在处理动态php页面时，mod_php也比php-cgi更稳定更高效。

也就是说，我们的目的是，由Nginx来接收客户端的请求，如果是动态页面请求，就交给Apache处理，然后经由Nginx再返回给客户端，其余的请求，则由Nginx自己处理，然后把结果返回给客户端，。当然了，你完全可以让Nginx只做Proxy功能，所有的请求都交给Apache,Tomcat等处理，本文使用前者。

但是，在本文中，我们实现的是在一台服务器里一个Nginx加一个Apache的简单结构，在实际应用中，可能前端是由一台或多台Nginx组成的代理服务器，后端则是多台Apache或其他Web服务器，再加上多种第三方软件而组成的集群。





  他们之间的关系大概是这样子：浏览器 [《《-》](https://www.baidu.com/s?wd=《《-》&tn=SE_PcZhidaonwhc_ngpagmjz&rsv_dl=gh_pc_zhidao)》 nginx或者apache [《《-》](https://www.baidu.com/s?wd=《《-》&tn=SE_PcZhidaonwhc_ngpagmjz&rsv_dl=gh_pc_zhidao)》 tomcat或者[php-fpm](https://www.baidu.com/s?wd=php-fpm&tn=SE_PcZhidaonwhc_ngpagmjz&rsv_dl=gh_pc_zhidao)

nginx和apache两者的作用相同，都是常见webserver服务器，[相互独立](https://www.baidu.com/s?wd=相互独立&tn=SE_PcZhidaonwhc_ngpagmjz&rsv_dl=gh_pc_zhidao)也可相符搭配，都是用于浏览器用户过来的[http请求](https://www.baidu.com/s?wd=http请求&tn=SE_PcZhidaonwhc_ngpagmjz&rsv_dl=gh_pc_zhidao)，然后把请求结果反应给浏览器。

apache是出现比较早的web server，90年代就有了，兼容性好文档全应用广泛。
nginx是后起之秀，2000年以后才有的，在web2.0年代性能远远超过apache，是时下比较流行的web server。

至于tomcat ，那是用来处理java程序的解释器。本身apache也好，nginx也好，都是无法直接处理[java语言](https://www.baidu.com/s?wd=java语言&tn=SE_PcZhidaonwhc_ngpagmjz&rsv_dl=gh_pc_zhidao)的，只能通过设置，当收到java文件请求时，传给后方tomcat处理，再把tomcat的反应回给浏览器。ps:上述中的[php-fpm](https://www.baidu.com/s?wd=php-fpm&tn=SE_PcZhidaonwhc_ngpagmjz&rsv_dl=gh_pc_zhidao)，是用来处理php程序的，作用跟tomcat差不多。

怎么选择搭配，这个就看各人的喜欢和开发需要了。我比较常用的就是nginx+[php-fpm](https://www.baidu.com/s?wd=php-fpm&tn=SE_PcZhidaonwhc_ngpagmjz&rsv_dl=gh_pc_zhidao)，apache+tomcat，nginx+tomcat。也试过nginx+apache+php-fpm+tomcat等复杂组合。

只要了解每个软件的功能和作用，就可以合理利用自由搭配。等等。希望对你有帮助。  





Lucence：ES和solr都是基于l以上进行扩展的。

ES封装了L，让使用变得更加简单，在高可用上面做得更好。

区别：ES的优点：

* 可用性：支持集群没有单点故障
* 扩展性：支持集群扩展
* 在集群环境使用：大项目使用
* 支持多种语言（L支持JAVA）
* 简单性

（github使用es）

SOlr与ES比较：

* Solr利用Zookeeper进行分布式管理，支持更多格式的数据，官方提供的功能
* Solr配置复杂，学习成本高
* Solr 支持更多格式的数据，而 Elasticsearch 仅支持json文件格式；
* Solr 在传统的搜索应用中表现好于 Elasticsearch，但在处理实时搜索应用时效率明显低于 Elasticsearch。
* 实时建立索引，Solr会io阻塞，查询性能较差



**入查询语句。**

**b) 对查询语句经过语法分析和语言分析得到一系列词(Term)。**

**c) 通过语法分析得到一个查询树。**

**d) 通过索引存储将索引读入到内存。**

**e) 利用查询树搜索索引，从而得到每个词(Term)的文档链表，对文档链表进行交，差，并得到结果文档。**

**f) 将搜索到的结果文档对查询的相关性进行排序。**

**g) 返回查询结果给用户。**



