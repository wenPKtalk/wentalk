#### <center> Elastic Stack学习</center>
##### 1.Elastic Search 安装与运行
1. 获取elastic search 

``` 
(需要安装wget)	
wget https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-6.2.4.tar.gz
```

2. 解压elasticsearch

```
tar zxvf elasticsearch-6.2.4.tar.gz 
```

3. 运行elasticsearch

```
bin/elasticsearch
访问：127.0.0.1:9200
```

4. 出现以下信息

```
{
  "name" : "YSApVRi",  //node名
  "cluster_name" : "elasticsearch", //集群名称
  "cluster_uuid" : "LpM-q8GQSam1T5IEkZHXcg",
  "version" : {
    "number" : "6.2.4",
    "build_hash" : "ccec39f",
    "build_date" : "2018-04-12T20:37:28.497551Z",
    "build_snapshot" : false,
    "lucene_version" : "7.2.1",
    "minimum_wire_compatibility_version" : "5.6.0",
    "minimum_index_compatibility_version" : "5.0.0"
  },
  "tagline" : "You Know, for Search" //产品说明
}
```

5. elasticsearch配置说明：
	* 配置文件位于config目录中
	* elasticsearch.yml es的相关配置
	   i. cluster.name集群名称，以此作为是否同一集群的判断条件  
	   ii. node.name节点名称，一次作为集群中不同节点的区分条件  
	   iii. network.host/http.port网络地址和端口，用于http和transport服务使用  
	   iv. path.data数据存储地址  
	   v. path.log日志存储地址  
	* jvm.options jvm的相关参数
	* log4j2.properties 日志相关配置
	* Development 与Production模式说明
	   i. 以transport的地址是否绑定再localhost为标准判断network.host  
	   ii. Development模式下启动时会以warning方式提示配置检查异常  
	   iii. Production模式下启动时会以error的方式提示配置检查异常并退出  

6. Elasticsearch本地启动集群的方式

```
1. bin/elasticsearch
2. bin/elasticsearch -Ehttp.port=8200 -Epath.data=node2
3. bin/elasticsearch -Ehttp.port=7200 -Epath.data=node3

访问： 127.0.0.1:8200/_cat/nodes?v 查看节点信息
访问： 127.0.0.1:8200/_custer/stats 查看集群信息
```

7. kibana 下载安装（同elasticsearch）
8. kibana配置  
   i. 配置位于config文件夹中
   ii. server.host/server.port访问kibana用的地址和端口
   iii. elasticsearch,url 待访问elasticsearch的地址
9. kibana常用功能说明
   i. Discover数据搜索查看  
   ii. Visualize图表制作  
   iii. Dashboard仪表盘制作  
   iv. Timelion时序数据的高级可视化分析  
   v. DevTools开发者工具  
   vi. Management配置  
 
10. Elasticsearch常用术语
    i. Document文档数据
    ii. Index索引
    iii. Type索引中的数据类型  
    iv.Field字段，文档的属性  
    v. Query Dsl 查询语法  
    
11. ElasticSearch CRUD  

```

在kibana中的devToos
创建
POST /accounts/person/1 //accounts：index, person type, 1 id
{
	"name":"John", //
	"lastname":"Doe",
	"job_description":"Systems administrator and linux specialit"
}
返回结果
｛
   "_index":"accounts",
   "_type":"person",
   "_id":"1",
   "_version":"1",
   "_shards":{
     "total":2,
     "successful":1,
     "failed":0
   }
   "created":true
｝
//读取文档
Get accounts/person/1
//更新语法
POST /accounts/person/1/_update
{ 
  "doc":{
     "job_description":"Systems update"
  }
}
//删除
DELETE accounts/person/1
```
11. Elasticsearch Query  

```
Query String  
GET /accounts/person/_search?q=john
Query DSL 更强大
GET  /accounts/person/_search
{
  "query" :{
    "match":{
       "name":"john"
    }
  }
}
```

##### Beats简介

1. Lightweight Data Shipper   
   + Filebeat 日志文件  
   + Metricbeat 度量数据
   + Packetbeat 网络数据
   + Winlogbeat Windows数据
   + Heartbeat 健康检查
2. Filebeat input 配置
3. Filebeat Output 配置
4. Filebeat Filter 配置  
   i. Input时处理  
   ii. Output前处理  
5. Filebeat+Elasticsearch Ingest Node  
   + Filebeat缺乏数据转换能力
   + Elasticsearch Ingest Node
     i. 新增的node类型  
     ii. 在数据写入es前对数据进行处理转换  
     iii. pipeline api
 6. Filebeat Module简介  
    + 解决Filebeat配置复杂  
    + 对于社区常见需求进行配置封装增加易用性  i. nginx ii. apache iii. mysql  
    + 封装内容  i.filebeat.yml配置
    ii. ingest node pipeline 配置 iii. kibana dashboard 配置
    
  7. Packetbeat简介
     i.实时抓取网络包 ii.自动解析应用协议：http,
  8. Logstash入门  
  i. etl,Extract,Transform,Load 
  存在数据流 input, filter,output
  非结构化数据转换成结构化数据
  
##### 实战
* 收集Elasticsearch集群的查询语句  
* 分析查询语句的常用语句，响应时长等
1. 方案
	1. production cluster(业务集群) -->Packetbeat 抓包-->Logstash --> Monitoring cluster -->kibana(可视化分析)
    


