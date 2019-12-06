# Spark相关问题记录

1. sparkstreaming默认webUI地址：http://localhost:4040

2. Spark程序读取不到kafka数据

   提示：

   ```
   KafkaRDD: Beginning offset 17353267 is the same as ending offset skipping ems-test-read-sql 0
   ```

   Kafka运行消费者命令：

   ```sh
   ./kafka-console-consumer.sh --bootstrap-server nn:2181,snn:2181,dn0:2181,dn1:2181,dn2:2181 --topic ems-test-read-sql --from-beginning
   ```

   报警：

   ```
   [2019-03-08 11:14:31,600] WARN [Consumer clientId=consumer-1, groupId=console-consumer-25932] Connection to node -3 could not be established. Broker may not be available. (org.apache.kafka.clients.NetworkClient)
   [2019-03-08 11:14:31,607] WARN [Consumer clientId=consumer-1, groupId=console-consumer-25932] Connection to node -4 could not be established. Broker may not be available. (org.apache.kafka.clients.NetworkClient)
   ```

   原因：主题问题，主题不可用或主题数据丢失

   解决办法：重新向主题写入数据

3. Sparkstreaming读取kafka遇到报错

   ```
   ERROR StreamingContext: Error starting the context, marking it as stopped
   org.apache.kafka.common.protocol.types.SchemaException: Error computing size for field 'coordinator_key': Missing value for field 'coordinator_key' which has no default value.
   ```

   原因：没有设置groupID，报错没有有用信息

4. spark on yarn任务kill掉之后，再次提交任务一直state: ACCEPTED，至少要等半小时才能再次提交成功（每次等待时间不等）

   ```
   19/04/18 13:53:53 INFO Client: Application report for application_1555418192872_0010 (state: ACCEPTED)
   19/04/18 13:53:54 INFO Client: Application report for application_1555418192872_0010 (state: ACCEPTED)
   19/04/18 13:53:55 INFO Client: Application report for application_1555418192872_0010 (state: ACCEPTED)
   ```

   可能原因：
   
   1. 跟资源释放有关，spark是粗粒度的资源申请，直接kill掉，资源没有释放
   2. 有僵尸进程
   3. yarn集群的某个节点挂了（可能性较低）
   4. hdfs上有checkpoint留下的记录，启动失败（可能性较大）
   5. 提交到yarn的appName或者id冲突
   
   查找过程：
   
   1. 查看进程，没有发现僵尸进程
   
   2. 删除hdfs上一次任务留下的tmp记录（没有解决问题），程序不设置checkpoint（没有解决问题），删除
   
      本地tmp目录相关记录（没有解决问题）
   
   原因：
   
   解决办法：修改/etc/hadoop/conf/capacity-scheduler.xml文件中的参数：
   
   ```xml
   yarn.scheduler.capacity.maximum-am-resource-percent 0.1
   ```

5. spark的transformation算子延时执行特性

   ```scala
   val arr = Array(1,2,3)
   spark.sparkContext.parallelize(arr)
   	.foreachPartition(part => {
         var i = 0
         val a = part.map(each => {
           println("map")
           i = i + each
         })
         println("out" + i)
         // a.size触发part.map操作
         println("size" + a.size)
       })
   ```

### Livy

1. 启动session后，如果没有操作，1个小时后session会自动停止。