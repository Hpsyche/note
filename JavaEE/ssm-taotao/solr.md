# Solr管理索引库

## 添加文档

第一步：把solrJ的jar包添加到工程中。

第二步：创建一个SolrServer，使用HttpSolrServer创建对象。

第三步：创建一个文档对象SolrInputDocument对象。

第四步：向文档中添加域。必须有id域，域的名称必须在schema.xml中定义。

第五步：把文档添加到索引库中。

第六步：提交。

```java
@Test
	public void addDocument() throws Exception {
		// 第一步：把solrJ的jar包添加到工程中。
		// 第二步：创建一个SolrServer，使用HttpSolrServer创建对象。
		SolrServer solrServer = new HttpSolrServer("http://192.168.25.154:8080/solr");
		// 第三步：创建一个文档对象SolrInputDocument对象。
		SolrInputDocument document = new SolrInputDocument();
		// 第四步：向文档中添加域。必须有id域，域的名称必须在schema.xml中定义。
		document.addField("id", "test001");
		document.addField("item_title", "测试商品");
		document.addField("item_price", "199");
		// 第五步：把文档添加到索引库中。
		solrServer.add(document);
		// 第六步：提交。
		solrServer.commit();
	}
```

## 删除文档

### 根据id删除

第一步：创建一个SolrServer对象。

第二步：调用SolrServer对象的根据id删除的方法。

第三步：提交。

```java
@Test
	public void deleteDocumentById() throws Exception {
		// 第一步：创建一个SolrServer对象。
		SolrServer solrServer = new HttpSolrServer("http://192.168.25.154:8080/solr");
		// 第二步：调用SolrServer对象的根据id删除的方法。
		solrServer.deleteById("1");
		// 第三步：提交。
		solrServer.commit();
	}
```

@Test	**public** **void** deleteDocumentById() **throws** Exception {		// 第一步：创建一个SolrServer对象。		SolrServer solrServer = **new** HttpSolrServer("http://192.168.25.154:8080/solr");		// 第二步：调用SolrServer对象的根据id删除的方法。		solrServer.deleteById("1");		// 第三步：提交。		solrServer.commit();	}

### 根据查询删除

```java
@Test
	public void deleteDocumentByQuery() throws Exception {
		SolrServer solrServer = new HttpSolrServer("http://192.168.25.154:8080/solr");
		solrServer.deleteByQuery("title:change.me");
		solrServer.commit();
	}
```

## 查询索引库

查询步骤：

第一步：创建一个SolrServer对象

第二步：创建一个SolrQuery对象。

第三步：向SolrQuery中添加查询条件、过滤条件。。。

第四步：执行查询。得到一个Response对象。

第五步：取查询结果。

第六步：遍历结果并打印。

```java
@Test
	public void queryDocument() throws Exception {
		// 第一步：创建一个SolrServer对象
		SolrServer solrServer = new HttpSolrServer("http://192.168.25.154:8080/solr");
		// 第二步：创建一个SolrQuery对象。
		SolrQuery query = new SolrQuery();
		// 第三步：向SolrQuery中添加查询条件、过滤条件。。。
		query.setQuery("*:*");
		// 第四步：执行查询。得到一个Response对象。
		QueryResponse response = solrServer.query(query);
		// 第五步：取查询结果。
		SolrDocumentList solrDocumentList = response.getResults();
		System.out.println("查询结果的总记录数：" + solrDocumentList.getNumFound());
		// 第六步：遍历结果并打印。
		for (SolrDocument solrDocument : solrDocumentList) {
			System.out.println(solrDocument.get("id"));
			System.out.println(solrDocument.get("item_title"));
			System.out.println(solrDocument.get("item_price"));
		}
	}
```

#### 带高亮显示

```java
@Test
	public void queryDocumentWithHighLighting() throws Exception {
		// 第一步：创建一个SolrServer对象
		SolrServer solrServer = new HttpSolrServer("http://192.168.25.154:8080/solr");
		// 第二步：创建一个SolrQuery对象。
		SolrQuery query = new SolrQuery();
		// 第三步：向SolrQuery中添加查询条件、过滤条件。。。
		query.setQuery("测试");
		//指定默认搜索域
		query.set("df", "item_keywords");
		//开启高亮显示
		query.setHighlight(true);
		//高亮显示的域
		query.addHighlightField("item_title");
		query.setHighlightSimplePre("<em>");
		query.setHighlightSimplePost("</em>");
		// 第四步：执行查询。得到一个Response对象。
		QueryResponse response = solrServer.query(query);
		// 第五步：取查询结果。
		SolrDocumentList solrDocumentList = response.getResults();
		System.out.println("查询结果的总记录数：" + solrDocumentList.getNumFound());
		// 第六步：遍历结果并打印。
		for (SolrDocument solrDocument : solrDocumentList) {
			System.out.println(solrDocument.get("id"));
			//取高亮显示
			Map<String, Map<String, List<String>>> highlighting = response.getHighlighting();
			List<String> list = highlighting.get(solrDocument.get("id")).get("item_title");
			String itemTitle = null;
			if (list != null && list.size() > 0) {
				itemTitle = list.get(0);
			} else {
				itemTitle = (String) solrDocument.get("item_title");
			}
			System.out.println(itemTitle);
			System.out.println(solrDocument.get("item_price"));
		}
	}
```

## SSM引入

### 导入商品索引Service层

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
	xmlns:context="http://www.springframework.org/schema/context" xmlns:p="http://www.springframework.org/schema/p"
	xmlns:aop="http://www.springframework.org/schema/aop" xmlns:tx="http://www.springframework.org/schema/tx"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans-4.2.xsd
	http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context-4.2.xsd
	http://www.springframework.org/schema/aop http://www.springframework.org/schema/aop/spring-aop-4.2.xsd http://www.springframework.org/schema/tx http://www.springframework.org/schema/tx/spring-tx-4.2.xsd
	http://www.springframework.org/schema/util http://www.springframework.org/schema/util/spring-util-4.2.xsd">
	
	<bean id="httpSolrServer" class="org.apache.solr.client.solrj.impl.HttpSolrServer">
		<constructor-arg name="baseURL"
			value="http://192.168.25.154:8080/solr" />
	</bean>

</beans>
```

```java
@Service
public class SearchItemServiceImpl implements SearchItemService {

	@Autowired
	private SearchItemMapper searchItemMapper;
	@Autowired
	private SolrServer solrServer;
	
	@Override
	public TaotaoResult importAllItems() throws Exception{
		// 1、查询所有商品数据。
		List<SearchItem> itemList = searchItemMapper.getSearchItemList();
		// 2、创建一个SolrServer对象。
		// 3、为每个商品创建一个SolrInputDocument对象。
		for (SearchItem searchItem : itemList) {
			SolrInputDocument document = new SolrInputDocument();
			// 4、为文档添加域
			document.addField("id", searchItem.getId());
			document.addField("item_title", searchItem.getTitle());
			document.addField("item_sell_point", searchItem.getSell_point());
			document.addField("item_price", searchItem.getPrice());
			document.addField("item_image", searchItem.getImage());
			document.addField("item_category_name", searchItem.getCategory_name());
			document.addField("item_desc", searchItem.getItem_desc());
			// 5、向索引库中添加文档。
			solrServer.add(document);
		}
		//提交修改
		solrServer.commit();
		// 6、返回TaotaoResult。
		return TaotaoResult.ok();
	}
}
```

### 查询商品Service层

```java
@Service
public class SearchServiceImpl implements SearchService {

	@Autowired
	private SearchDao searchDao;
	
	@Override
	public SearchResult search(String queryString, int page, int rows) throws Exception {
		// 1、创建一个SolrQuery对象。
		SolrQuery query = new SolrQuery();
		// 2、设置查询条件
		query.setQuery(queryString);
		// 3、设置分页条件
		query.setStart((page - 1) * rows);
		query.setRows(rows);
		// 4、需要指定默认搜索域。
		query.set("df", "item_title");
		// 5、设置高亮
		query.setHighlight(true);
		query.addHighlightField("item_title");
		query.setHighlightSimplePre("<em style=\"color:red\">");
		query.setHighlightSimplePost("</em>");
		// 6、执行查询，调用SearchDao。得到SearchResult
		SearchResult result = searchDao.search(query);
		// 7、需要计算总页数。
		long recordCount = result.getRecordCount();
		long pageCount = recordCount / rows;
		if (recordCount % rows > 0) {
			pageCount++;
		}
		result.setPageCount(pageCount);
		// 8、返回SearchResult
		return result;
	}

}
```

### 查询商品DAO层

```java
@Repository
public class SearchDao {

	@Autowired
	private SolrServer solrServer;
	
	public SearchResult search(SolrQuery query) throws Exception {
		//根据query对象查询索引库
		QueryResponse response = solrServer.query(query);
		//取商品列表
		SolrDocumentList solrDocumentList = response.getResults();
		//商品列表
		List<SearchItem> itemList = new ArrayList<>();
		for (SolrDocument solrDocument : solrDocumentList) {
			SearchItem item = new SearchItem();
			item.setId((String) solrDocument.get("id"));
			item.setCategory_name((String) solrDocument.get("item_category_name"));
			item.setImage((String) solrDocument.get("item_image"));
			item.setPrice((long) solrDocument.get("item_price"));
			item.setSell_point((String) solrDocument.get("item_sell_point"));
			//取高亮显示
			Map<String, Map<String, List<String>>> highlighting = response.getHighlighting();
			List<String> list = highlighting.get(solrDocument.get("id")).get("item_title");
			String itemTitle = "";
			//有高亮显示的内容时。
			if (list != null && list.size() > 0) {
				itemTitle = list.get(0);
			} else {
				itemTitle = (String) solrDocument.get("item_title");
			}
			item.setTitle(itemTitle);
			//添加到商品列表
			itemList.add(item);
		}
		SearchResult result = new SearchResult();
		//商品列表
		result.setItemList(itemList);
		//总记录数
		result.setRecordCount(solrDocumentList.getNumFound());
		
		return result;
	}
}
```

