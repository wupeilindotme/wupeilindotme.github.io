# ElasticSearch

## 前言

版本：7.6

全文检索引擎

SQL查询时， 大数据情况下速度很慢，在加索引都无法满足的情况下，可以选择ES作为解决方案



### Lucene

Lucene是一套信息检索工具jar包，不包含搜索引擎系统

包含：

- 索引结构
- 排序功能
- 搜索规则



Lucene和ES的关系： ES是基于Lucene做了一些封装和增强



### ElasticSearch概述

ES是一个开源**高扩展**的分布式全文搜索引擎，可以近乎**实时**的

存储、检索数据； 本身**扩展性**很好，可以扩展到上百台服务器。ES使用java开发的Lucene作为核心实现所有的索引和搜索的功能，通过简单的 **RESTful API**来简化复杂的Lucene，更容易上手。



### Solr概述

Solr采用java开发，基于Lucene的全文搜索服务器。Solr可以独立运行jetty, tomcat等web容器中。提供了WebService API



### ES和Solr的差别

从性能上：

- 对单纯已有数据进行搜索时，solr更快
- 当实时建立索引时，solr会产生IO阻塞，查询性能较差
- 随着数据量的增加，solr的搜索效率会变低，而es变化不大

从功能上：

- es基本是开箱即用
- solr利用zookeeper进行分布式管理，而es自带分布式协调管理功能
- solr支持多格式数据如 JSON/XML/CSV，而es只支持JSON



> Q: 如何选择ES和Solr
>
> A: 无论是从性能还是从功能上，ES都是更好的选择





## 安装

### ElasticSearch

**7.6版本 JDK最低要求1.8**

官网下载 [ElasticSearch][Download Elasticsearch | Elastic](https://www.elastic.co/cn/downloads/elasticsearch)

> windows下安装

- 下载压缩包解压到目标文件夹

- 目录结构

  ```
  bin 可执行文件等
  config 配置文件
  	- log4j2.properties 日志配置
  	- jvm.options jvm参数，需要配置运行内存，默认1GB运存
  	- elasticsearch.yml es的配置文件
  	
  lib  相关jar包
  modules 功能模块
  plugins 插件  ik分词器等插件
  logs  日志
  ```



- 使用 windows下 执行 bin\elasticsearch.bat
- 访问测试， 默认端口9200，访问 http://localhost:9200

### ElasticSearch Head可视化

> elasticsearch-head

下载地址：[GitHub - mobz/elasticsearch-head: A web front end for an elastic search cluster](https://github.com/mobz/elasticsearch-head)

是一个开源前端web工程，详细的可以直接看github上的文档，这里做下简单介绍

需要nodejs

- 克隆仓库到本地

```powershell
git clone https://github.com/mobz/elasticsearch-head
```

- 安装依赖

```powershell
npm i
```

- 启动

```powershell
npm run start
```

- 访问  http://localhost:9100

  直接打开会有跨域问题，这时需要配置一下，在`elasticsearch.yml`中加入以下配置

```yml
http.cors.enabled: true
http.cors.allow-origin: "*"
```

重启elasticsearch即可



### Kibana

针对ES开源分析的可视化平台，用于搜索、查看、管理ES中的数据。

[Kibana：数据的探索、可视化和分析 | Elastic](https://www.elastic.co/cn/kibana)

Ps：  Kibana的版本和ES要一致

- 下载7.6版本的Kibana，windows下解压即可

- 汉化

  配置kibana.yml，添加配置`i18n: "zh-CN"`





## ES核心概念

### **物理设计**

es在后台把每个索引划分成多个分片，每份分片可以在集群中的不同服务器之间迁移。

一个集群至少有一个节点，而一个节点就是一个es进程，节点可以有多个索引。 在创建索引时，索引会有5个默认分片（也称主分片），每一个主分片又会有一个副本（也称复制分片）

![image-20230818175536165](elasticsearch/image-20230818175536165.png)

如图是一个有3个节点的集群，可以看到主分片和对应的复制分片不会都在同一个节点上，这样可以保证数据不会丢失。 

一个分片是一个Lucene索引，一个包含倒排索引的文件目录，**倒排索引**的结构使es在不扫描全部文档的清空下，就能告诉哪些文档包含特定的关键字。

> 倒排索引

倒排索引适用于快速的全文搜索，一个索引由文档中所不重复的列表构成，对于每一个词都由一个包含它的文档列表

如有以下两个文档

```
Good good study, day day up  # 文档1 doc_1
Day day up, good good study  # 文档2 doc_2
```

为创建倒排索引，会将每个文档拆分为独立的词（也称词条或tokens)，然后创建一个没有重复词条的排序列表，列出每个词条出现在哪个文档：

| 词条  | doc_1 | doc_2 |
| ----- | ----- | ----- |
| Good  | √     | ×     |
| Day   | ×     | √     |
| good  | √     | √     |
| day   | √     | √     |
| study | √     | √     |
| up    | √     | √     |

倘若此时查找good词条， 文档1只命中1次， 文档2则命中2次，文档2的权重更高。

总的来说，一个ES的索引（库）是由多个Lucene倒排索引构成



### **逻辑设计**

ES是面向文档 （一个JSON对象），和关系型数据库有如下的对比关系：

| 关系型数据库     | ElasticSearch                        |
| ---------------- | ------------------------------------ |
| 数据库(database) | 索引 (indices)                       |
| 表(tables)       | 类型 (types) (8.x.x版本以上已经弃用) |
| 行(rows)         | 文档 (documents)                     |
| 字段(columns)    | 属性 (fields)                        |

- 文档

  es是面向文档的，意味着索引和搜索数据的最小单位是文档，其中，文档有一些重要属性

  1. 自我包含，一篇文档同时包含字段和对应的值，也就是同时包含key : value
  2. 可以是层次型的，文档中包含其他文档
  3. 灵活的结构，不依赖于预先定义的模式

- 类型

  每个field都会有对应的类型，可以提前设置，也可以不设置，es会自动去匹配类型，但也有可能匹配错误。

- 索引

  对应 数据库(database) ，es中的索引是一个很大的文档集合





## 插件

### IK分词器

分词：把一段文本划分为一个个的关键字，默认的中文分词器是一个汉字分一个词，（如：面向生活，会拆分为“面” “向” “生” “活”）这显然不符合汉语要求。

使用IK分词器可以解决这个问题

下载链接：[IK分词器][[GitHub - medcl/elasticsearch-analysis-ik: The IK Analysis plugin integrates Lucene IK analyzer into elasticsearch, support customized dictionary.]

1. 下载.zip包到ES的plugins目录下解压。
2. 重启es
3. 通过elasticsearch-plugin 命令查看加载成功的插件



测试：

打开Kibana -> 开发工具进行测试‘

- 最简分词

```json
GET _analyze
{
  "analyzer": "ik_smart",  // ik分词器， 最简拆分
  "text": "面向生活编程"
}

```

得到结果

```json
{
  "tokens" : [
    {
      "token" : "面向",
      "start_offset" : 0,
      "end_offset" : 2,
      "type" : "CN_WORD",
      "position" : 0
    },
    {
      "token" : "生活",
      "start_offset" : 2,
      "end_offset" : 4,
      "type" : "CN_WORD",
      "position" : 1
    },
    {
      "token" : "编程",
      "start_offset" : 4,
      "end_offset" : 6,
      "type" : "CN_WORD",
      "position" : 2
    }
  ]
}
```

- 最细分词

```json
GET _analyze
{
  "analyzer": "ik_max_word",
  "text": "面向生活编程"
}
```

​	得到结果

```json
  "tokens" : [
    {
      "token" : "面向",
      "start_offset" : 0,
      "end_offset" : 2,
      "type" : "CN_WORD",
      "position" : 0
    },
    {
      "token" : "生活",
      "start_offset" : 2,
      "end_offset" : 4,
      "type" : "CN_WORD",
      "position" : 1
    },
    {
      "token" : "编程",
      "start_offset" : 4,
      "end_offset" : 6,
      "type" : "CN_WORD",
      "position" : 2
    }
  ]
}

```

- 意外情况： 一些无法识别的汉语词汇需要自己加到分词器词典里面，否则就按照汉字逐一拆分

- 在ik分词器插件的config文件中可以配置词典文件

  新建一个文件 `my.dic` ;

  在`IKAnalyzer.cfg.xml`中指定该文件如下：

  ```xml
  <?xml version="1.0" encoding="UTF-8"?>
  <!DOCTYPE properties SYSTEM "http://java.sun.com/dtd/properties.dtd">
  <properties>
  	<comment>IK Analyzer 扩展配置</comment>
  	<!--用户可以在这里配置自己的扩展字典 -->
  	<entry key="ext_dict">my.dic</entry>
  	 <!--用户可以在这里配置自己的扩展停止词字典-->
  	<entry key="ext_stopwords"></entry>
  	<!--用户可以在这里配置远程扩展字典 -->
  	<!-- <entry key="remote_ext_dict">words_location</entry> -->
  	<!--用户可以在这里配置远程扩展停止词字典-->
  	<!-- <entry key="remote_ext_stopwords">words_location</entry> -->
  </properties>
  
  ```

  

  此时可以在my.dic文件中添加一些自定义的词汇，再重启ES，分词时就能识别到啦。

