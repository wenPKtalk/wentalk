### <center>Hadoop分布式缓存</center>  

#### 问题：  

在执行MapReduce时，可能Mapper之间需要共享一些信息，如果信息量不大，可以将其从HDFS加载到内存当中，这就时Hadoop分布式缓存机制。  

#### 步骤：  

1. 在main方法中加载共享文件的HDFS路径，路径可以是目录也可以是文件。可以在路径末尾追加“#”+别名，在map阶段可以使用该别名。
  
```java
String cache = "hdfs://10.105.xxx.xxx:8020/cache/file";//目录或	文件  
cache = cache + "#myfile"; //myfile是文件的别名  
job.addcacheFile(new Path(cache).toUri().conf);//天骄到job设置

```

2. 在Mapper类或Reducer的setup方法中，用输入流获取分布式缓存中的文件。

```java
protected void setup(Context context) throws IOExcepiton,InterruptedException{
	FileReader reader = new FileReader("myfile");  
	BufferedReader br = new BufferedReader(reader);
}
```

加载到内存发生在job执行之前，每个从节点各自都缓存一份相同的共享数据。如果共享数据太大，可以将共享数据分批缓存，重复执行作业。

3. 实现矩阵相乘 
![矩阵相乘]()
右矩阵行转列