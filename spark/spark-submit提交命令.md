# spark-submit提交命令

## 1. 提交命令示例

```shell
spark-submit \
--class org.apache.spark.examples.SparkPi \
--master yarn \
--deploy-mode cluster \
--driver-memory 4g \
--executor-memory 2g \
--num-executors 2 \
--executor-cores 1 \
--queue thequeue \
--jars my-other-jar.jar,my-other-other-jar.jar \
examples/jars/spark-examples*.jar \
10
```

## 2. 相对灵活的提交命令脚本

```shell
#!/bin/sh

#spark所在路径
SparkPath=/usr/hdp/3.0.0.0-1634/spark2
#程序jar包所在路径
# JarPath=/home/thtf/sparktest/EMSBasedBigData
JarPath=.
#程序的jar包名
JarName=ems_bigdata-0.0.1-SNAPSHOT.jar
#Main方法所在类
MainClass=com.thtf.bigdata.spark.CalculateHisData

#提交任务
$SparkPath/bin/spark-submit \
--class $MainClass \
--master yarn \
--deploy-mode cluster \
--driver-memory 4g \
--executor-memory 4g \
--num-executors 2 \
--executor-cores 2 \
--jars $JarPath/lib/fastjson-1.2.24.jar \
--files $JarPath/baseconfiguration.properties \
--conf spark.streaming.kafka.maxRatePerPartition=20 \
--conf spark.default.parallelism=12 \
--conf spark.streaming.stopGracefullyOnShutdown=true \
--conf spark.serializer=org.apache.spark.serializer.KryoSerializer \
$JarPath/$JarName
```

## 3. 使用命令组装多个Jars

```shell
--jars $(echo /usr/local/spark/lib/*.jar | tr ' ' ',')
```

## 4. 通过脚本设置zk记录关闭spark程序

* Runtime.getRuntime ().addShutdownHook 这个里面调用streamingcontext stop方法

* 可以在driver端监听一下zk事件或者去定期扫数据库，来实现优雅的关闭

    设置代码监听zk节点flag开关的脚本：
    
```shell
    #!/bin/sh
    
    # zkPath=/usr/hdp/3.0.0.0-1634/zookeeper/bin
    Group=saveKafkaRecords-001
Topic=test-spark-001
    FlagPath=/consumers/$Group/offsets/$Topic/flag
    
    zookeeper-client -server nn:2181 << EOF
     set $FlagPath true
    EOF
    ```

