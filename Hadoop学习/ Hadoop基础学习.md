# <center> Hadoop基础学习</center>

#### 学习目标：
[大数据形象讲解](http://note.youdao.com/noteshare?id=ffebe09fdd8c83ee99405b6f314e8ba4)  
1. 大数据包括了以hadoop和spark为代表的基础大数据框架  
2. 实时数据处理，离线数据处理，数据分析，数据挖掘和机器算法进行预测分析。



#### 基础学习：
1. Hadoop是一个开源的大数据框架  
   Hadoop是一个分布式计算的解决方案  
   Hadoop = HDFS(分布式文件系统）+MapReduce(分布式计算）  
2. HDFS分布式文件系统：存储是大数据技术的基础  
   MapReduce编程模型：分布式计算是大数据应用的解决方案。 
   
    
#### HDFS总结：
![HDFS读流程](/Users/wensir/Mydoc/HDFS.png)
1. 普通的成百上千的机器  
2. 按TB甚至PB为单位的大量数据  
3. 简单便捷的文件获取  
4. HDFS概念  
    i. 数据块:数据块是抽象块而非整个文件作为存储单元，默认大小为64MB，一般设置为128M,备份*3。比如：10MB独占数据块，300MB占128M    
    ii. NameNode:【主】  
       管理文件系统的命名空间，存放文件元数据  
       维护着文件系统的所有文件和目录，文件与数据块的映射  
       记录每个文件中各个块所在数据节点的信息  
    iii. DataNode:【从】  
       存储并检索数据块  
       向NameNode更新所存储块的列表  
       datanode定期向namenode发送心跳，如果namenode没收到则认为挂掉了，会分配到其他节点  
    iv. HDFS优点  
       适合大文件存储，支持TB、PB级别的数据存储，并有副本策略  
       可以构建在脸颊的机器上，并有一定的容错和恢复机制  
       支持流式数据访问，一次写入，多次读取最高效  
    v. HDFS缺点  
       不适合大量小文件存储  
       不适合并发写入，不支持文件随机修改  
       不支持随机读等低延时的访问方式  
    思考：（1）NameNode的容错机制，挂掉了怎么办？  
             HA(高可用)
    		(2)数据块大小多少合适？ 
    		一般为128M，太小查找耗费性能。  
    		
    1. HDFS的写流程：  
     Data|client <-->NameNode --> DataNode-1|DataNode-2|DataNode-3
     * 客户端向NameNode发起写数据请求
     * 分块写入DataNode节点，DataNode自动完成副本备份
     * dataNode向NameNode汇报存储完成，NameNode通知客户端
    2. HDFS的读流程：
     * 客户端向NameNode发起读数据的请求
     * NameNode找出距离最近的DataNode节点信息
     * 客户端从DataNode分块下载文件


#### 常用HDFS Shell命令
1. 类Linux系统：  
ls,cat,mkdir,rm,chmod,chown等
2. HDFS文件交互：
copyFromLocal,copyToLocal,get,put   

#### Hadoop基础架构
1. MapReduce简介  
MapReduce是一种编程模型，是一种编程方法，是抽象的理论。
2. YARN概念  
资源管理：i. ResourceManager: 分配和调度资源 启动并监控ApplicationMaster  监控NodeManager    
		  ii. ApplicationMaster：为MR类型的程序申请资源，并分配给内部任务。 负责数据的切分。 监控任务的执行及容错。  
		  iii. NodeManager： 管理单个节点的的资源。处理来自ResourceManager的命令。处理来自ApplicatonMaster的命令。
3. MapReduce编程模型  
   输入一个大文件，通过split之后，将其氛围多个分片  
   每个文件分片由单独的机器去处理，就是Map方法  
   将各个机器计算的结果进行汇总并得到最终的结果，这就是Reduce方法。  -----spark  
   * split阶段  
   * Map阶段（需要编码）
   * Shuffle阶段  
   * Reduce阶段（需要编码）


#### Hadoop总结
1. 存储（HDFS)与计算（MapReduce模型）  
2. 利用shell命令及Python程序操作HDFS


#### 思考：
1. 如何通过Hadoop存储小文件？
2. 当有节点故障的时候，集群是如何继续提供服务的，如何读？如何写？
3. 哪些是影响MapReduce性能的因素？


#### Hadoop生态圈
Hive --数据仓库  HBase --海量小文件（二次开发）
spark --    

#### HBase
高可靠，高性能，面向列，可伸缩，实时读写的**分布式数据库**
利用HDFS作为其文件存储系统，支持MR程序读取数据  
存储非结构化和半结构化数据  
RowKey:数据的唯一标识，按字典排序  （如何设计）  
Column Family:列族，多个列的集合，最多不要超过3个  
TimeStamp时间戳：支持多版本数据同时存在  


#### Spark 大数据计算框架
基于内存的大数据并行计算框架  
Spark是MapReduce的替代方案，兼容HDFS，Hive等数据源  
1. spark优势：
基于内存计算的分布式计算框架  
抽象出分布式内存存储数据结构 弹性分布式数据急RDD  
基于事件驱动，通过线程池复用线程提高性能  

#### MapReduce作业
![Hadoop节点图](/Users/wensir/Mydoc/Hadoop学习/hadoop.png)

MapRduce作业是一种大规模数据集的并行计算的编程模型。我们可以将HDFS中存放的海量数据，通过MapReduce作业进行计算，得到目标数据！  
i. split阶段 ii. Map阶段 iii. shuffle阶段 iv. Reduce阶段  

1. 输入文件保存在DataNode的block中： Hadoop 1.x默认block大小 64MB;Hadoop2.x默认的block大小：128MB 可以在hdfs-site.xml中设置参数：dfs.block.size  
2. split阶段： 420MB的文件存放在128MB的datanode中 128*3+36 = 420M 4块。
地址存在nameNode中。  
3. 由于NameNode内存有限，大量的小文件会给Hdfs带来性能上的问题，故Hdfs适合存放大文件，对于小文件，可以采用压缩，合并小文件的优化策略。例如，设置文件输入类型为CombineFileInputFormat格式  
4. 4个分片则启动4个Map任务，实际情况下，map任务的个数是收多个条件的制约，一般一个DataNode的map任务数量控制在10到100比较合适  
5. 如何控制节点Map任务：i.增加map个数，可增大mapred.map.tasks ii.减少map个数，可增大mapred.min.split.size iii. 如果要减少map个数，但有很多小文件可将文件合并成大文件，再使用准则2
##### 本地优化-combine
数据经过Map端输出后会进行网络混洗，经Shuffle后进入Reduce，在大数据量的情况下可能会造成巨大的网络开销。故可以在本地先按照key先进行一轮排序与合并。再进行网络混洗这个过程就是combine。  
在多数情况下，Combine的逻辑和Reduce的逻辑一致的，即都是安装key合并数据，故可以认为Combine是对本地数据的Reduce操作。这里复用Reducer的逻辑，也可以自己实现Combiner类。
job.setCombinerClass(MyReduce.class); //设置Combine
job.setReducerClass(MyReduce.class);//设置Reducer

从map到Reduce  
![图](/Users/wensir/Mydoc/Hadoop学习/Map-reduce.png )

在一个Reducer中，所有的数据都会被按照key值升序排序，故如果part输出文件中包含key值，则这个文件一定是有序的。

##### 一个MapReduce作业中，以下三者的数量总是相等的
1. Partitioner的数量
2. Reduce任务的数量
3. 最终输出文件（如：part-r-00000)

##### Reduce任务数量
在大数据量的情况下，如果只设置1个reduce任务，那么在reduce阶段，整个集群只有该节点在运行reduce任务，其他几点都将被闲置，效率十分低下，故建议将reduce任务数量设置成一个较大的值（最大值是72）。
一个节点上的Reduce任务数并不像Map任务数哪像受多个因素制约，可以通过调节参数mapred.reduce.task;
ii. 可以在代码中调用job.setNumReduceTasks(int n)方法


#### 总结
1.split阶段  
![阶段图](/Users/wensir/Mydoc/Hadoop学习/split-period.png)  
* 由于NameNode内存限制，Hdfs适合存放大文件  
* 对于小文件可采用压缩输入格式combineFileInputFormat  
* dfs.block.size可调节块大小  
* Map任务数量和mapred.map.tasks和mapred.min.split.size参数  
2. 本地合并---combine
  数据在Mapper输出后会进行和Reducer相似的操作，减少网络开销。  
  job.setCombinerClass(MyReduce.class);  
3. Mapper-Shuffle-Reduce  
* Combine本质是在Mapper缓冲区溢写文件的合并  
* Patition是在Reduce输入之前发生，相同的key值一定会进入同一个Patitioner,Reduce过程会按照key排序。  
* Partitioner,Reducer,输出文件三者数量是相等的。  
* 大数据量的情况下，Reducer数量不宜过少，可以通过代码：job.setNumReduceTasks(int n)和mapred.reduce.tasks设置数量  

