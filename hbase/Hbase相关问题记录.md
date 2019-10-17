# Hbase相关问题记录

### 1. spark job 莫名阻塞

   相关链接：

   1. https://issues.apache.org/jira/browse/PHOENIX-3204
   2. https://issues.apache.org/jira/browse/PHOENIX-3121
   3. https://blog.csdn.net/wuxintdrh/article/details/96983949
   4. https://www.cnblogs.com/chuanzhang053/p/8514477.html

   job会阻塞100分钟，阻塞过程会一直重复打印以下信息：

   ```
   19/09/20 14:28:06 INFO ConnectionQueryServicesImpl: Lease renewed for scanner: TableResultIterator [htable=EMS5:tbl_building;hconnection-0x3d13f968, scan={"loadColumnFamiliesOnDemand":true,"filter":"FilterList AND (2/2): [FirstKeyOnlyFilter, \"A.:id\" IN (124,125,126,127,128,129,130,131,132,133,134,135,136,137,138,139,140,141,142,143,144,145,146,147,148,149,150,151,152,153,154,155,156,157,158,159,160,161,162,163,164,165,166,167,168,169,170,...)]","startRow":"\\x05\\x00\\x00\\x00\\x00\\x00\\x00\\x00\\x00","stopRow":"","batch":-1,"cacheBlocks":true,"totalColumns":1,"maxResultSize":2097152,"families":{"L#0":["_0"]},"caching":1000,"maxVersions":1,"timeRange":[0,9223372036854775807]}]
   19/09/20 14:28:06 INFO ConnectionQueryServicesImpl: Lease renewed for scanner: TableResultIterator [htable=EMS5:tbl_building;hconnection-0x3d13f968, scan={"loadColumnFamiliesOnDemand":true,"filter":"FilterList AND (2/2): [FirstKeyOnlyFilter, \"A.:id\" IN (124,125,126,127,128,129,130,131,132,133,134,135,136,137,138,139,140,141,142,143,144,145,146,147,148,149,150,151,152,153,154,155,156,157,158,159,160,161,162,163,164,165,166,167,168,169,170,...)]","startRow":"\\x04\\x00\\x00\\x00\\x00\\x00\\x00\\x00\\x00","stopRow":"\\x05\\x00\\x00\\x00\\x00\\x00\\x00\\x00\\x00","batch":-1,"cacheBlocks":true,"totalColumns":1,"maxResultSize":2097152,"families":{"L#0":["_0"]},"caching":1000,"maxVersions":1,"timeRange":[0,9223372036854775807]}]
   19/09/20 14:28:06 INFO ConnectionQueryServicesImpl: Lease renewed for scanner: TableResultIterator [htable=EMS5:tbl_building;hconnection-0x3d13f968, scan={"loadColumnFamiliesOnDemand":true,"filter":"FilterList AND (2/2): [FirstKeyOnlyFilter, \"A.:id\" IN (124,125,126,127,128,129,130,131,132,133,134,135,136,137,138,139,140,141,142,143,144,145,146,147,148,149,150,151,152,153,154,155,156,157,158,159,160,161,162,163,164,165,166,167,168,169,170,...)]","startRow":"\\x01\\x00\\x00\\x00\\x00\\x00\\x00\\x00\\x00","stopRow":"\\x02\\x00\\x00\\x00\\x00\\x00\\x00\\x00\\x00","batch":-1,"cacheBlocks":true,"totalColumns":1,"maxResultSize":2097152,"families":{"L#0":["_0"]},"caching":1000,"maxVersions":1,"timeRange":[0,9223372036854775807]}]
   19/09/20 14:28:06 INFO ConnectionQueryServicesImpl: Lease renewed for scanner: TableResultIterator [htable=EMS5:tbl_building;hconnection-0x3d13f968, scan={"loadColumnFamiliesOnDemand":true,"filter":"FilterList AND (2/2): [FirstKeyOnlyFilter, \"A.:id\" IN (124,125,126,127,128,129,130,131,132,133,134,135,136,137,138,139,140,141,142,143,144,145,146,147,148,149,150,151,152,153,154,155,156,157,158,159,160,161,162,163,164,165,166,167,168,169,170,...)]","startRow":"","stopRow":"\\x01\\x00\\x00\\x00\\x00\\x00\\x00\\x00\\x00","batch":-1,"cacheBlocks":true,"totalColumns":1,"maxResultSize":2097152,"families":{"L#0":["_0"]},"caching":1000,"maxVersions":1,"timeRange":[0,9223372036854775807]}]
   19/09/20 14:28:06 INFO ConnectionQueryServicesImpl: Renewed leases for 4 scanner/s in 11 ms
   ```

   有设置hbase.client.scanner.timeout.period参数，阻塞100分钟之后，报错：

   ```
   java.sql.SQLTimeoutException: Operation timed out.
           at org.apache.phoenix.exception.SQLExceptionCode$15.newException(SQLExceptionCode.java:406)
           at org.apache.phoenix.exception.SQLExceptionInfo.buildException(SQLExceptionInfo.java:150)
           at org.apache.phoenix.iterate.BaseResultIterators.getIterators(BaseResultIterators.java:1343)
           at org.apache.phoenix.iterate.BaseResultIterators.getIterators(BaseResultIterators.java:1239)
           at org.apache.phoenix.iterate.RoundRobinResultIterator.getIterators(RoundRobinResultIterator.java:176)
           at org.apache.phoenix.iterate.RoundRobinResultIterator.next(RoundRobinResultIterator.java:91)
           at org.apache.phoenix.jdbc.PhoenixResultSet.next(PhoenixResultSet.java:805)
           at com.thtf.bigdata.functions.PhoenixFunctions$.getItemMaxValue(PhoenixFunctions.scala:130)
           at com.thtf.bigdata.spark.CalculateKafkaData$$anonfun$main$1.apply(CalculateKafkaData.scala:155)
           at com.thtf.bigdata.spark.CalculateKafkaData$$anonfun$main$1.apply(CalculateKafkaData.scala:143)
           at org.apache.spark.streaming.dstream.DStream$$anonfun$foreachRDD$1$$anonfun$apply$mcV$sp$3.apply(DStream.scala:628)
           at org.apache.spark.streaming.dstream.DStream$$anonfun$foreachRDD$1$$anonfun$apply$mcV$sp$3.apply(DStream.scala:628)
           at org.apache.spark.streaming.dstream.ForEachDStream$$anonfun$1$$anonfun$apply$mcV$sp$1.apply$mcV$sp(ForEachDStream.scala:51)
           at org.apache.spark.streaming.dstream.ForEachDStream$$anonfun$1$$anonfun$apply$mcV$sp$1.apply(ForEachDStream.scala:51)
           at org.apache.spark.streaming.dstream.ForEachDStream$$anonfun$1$$anonfun$apply$mcV$sp$1.apply(ForEachDStream.scala:51)
           at org.apache.spark.streaming.dstream.DStream.createRDDWithLocalProperties(DStream.scala:416)
           at org.apache.spark.streaming.dstream.ForEachDStream$$anonfun$1.apply$mcV$sp(ForEachDStream.scala:50)
           at org.apache.spark.streaming.dstream.ForEachDStream$$anonfun$1.apply(ForEachDStream.scala:50)
           at org.apache.spark.streaming.dstream.ForEachDStream$$anonfun$1.apply(ForEachDStream.scala:50)
           at scala.util.Try$.apply(Try.scala:192)
           at org.apache.spark.streaming.scheduler.Job.run(Job.scala:39)
           at org.apache.spark.streaming.scheduler.JobScheduler$JobHandler$$anonfun$run$1.apply$mcV$sp(JobScheduler.scala:257)
           at org.apache.spark.streaming.scheduler.JobScheduler$JobHandler$$anonfun$run$1.apply(JobScheduler.scala:257)
           at org.apache.spark.streaming.scheduler.JobScheduler$JobHandler$$anonfun$run$1.apply(JobScheduler.scala:257)
           at scala.util.DynamicVariable.withValue(DynamicVariable.scala:58)
           at org.apache.spark.streaming.scheduler.JobScheduler$JobHandler.run(JobScheduler.scala:256)
           at java.util.concurrent.ThreadPoolExecutor.runWorker(ThreadPoolExecutor.java:1149)
           at java.util.concurrent.ThreadPoolExecutor$Worker.run(ThreadPoolExecutor.java:624)
           at java.lang.Thread.run(Thread.java:748)
   ```

   删除hbase.client.scanner.timeout.period参数，阻塞100分钟之后，报错：

   ```
   19/09/20 16:05:00 WARN ScannerCallable: Ignore, probably already closed. Current scan: {"loadColumnFamiliesOnDemand":true,"filter":"FilterList AND (2/2): [FirstKeyOnlyFilter, \"A.:id\" IN (124,125,126,127,128,129,130,131,132,133,134,135,136,137,138,139,140,141,142,143,144,145,146,147,148,149,150,151,152,153,154,155,156,157,158,159,160,161,162,163,164,165,166,167,168,169,170,...)]","startRow":"","stopRow":"\\x01\\x00\\x00\\x00\\x00\\x00\\x00\\x00\\x00","batch":-1,"cacheBlocks":true,"totalColumns":1,"maxResultSize":2097152,"families":{"L#0":["_0"]},"caching":1000,"maxVersions":1,"timeRange":[0,9223372036854775807]} on table: EMS5:tbl_building
   org.apache.hadoop.hbase.UnknownScannerException: org.apache.hadoop.hbase.UnknownScannerException: Unknown scanner '-8767951189578287340'. This can happen due to any of the following reasons: a) Scanner id given is wrong, b) Scanner lease expired because of long wait between consecutive client checkins, c) Server may be closing down, d) RegionServer restart during upgrade.
   If the issue is due to reason (b), a possible fix would be increasing the value of'hbase.client.scanner.timeout.period' configuration.
           at org.apache.hadoop.hbase.regionserver.RSRpcServices.getRegionScanner(RSRpcServices.java:2930)
           at org.apache.hadoop.hbase.regionserver.RSRpcServices.scan(RSRpcServices.java:3287)
           at org.apache.hadoop.hbase.shaded.protobuf.generated.ClientProtos$ClientService$2.callBlockingMethod(ClientProtos.java:42002)
           at org.apache.hadoop.hbase.ipc.RpcServer.call(RpcServer.java:409)
           at org.apache.hadoop.hbase.ipc.CallRunner.run(CallRunner.java:131)
           at org.apache.hadoop.hbase.ipc.RpcExecutor$Handler.run(RpcExecutor.java:324)
           at org.apache.hadoop.hbase.ipc.RpcExecutor$Handler.run(RpcExecutor.java:304)
   
           at sun.reflect.NativeConstructorAccessorImpl.newInstance0(Native Method)
           at sun.reflect.NativeConstructorAccessorImpl.newInstance(NativeConstructorAccessorImpl.java:62)
           at sun.reflect.DelegatingConstructorAccessorImpl.newInstance(DelegatingConstructorAccessorImpl.java:45)
           at java.lang.reflect.Constructor.newInstance(Constructor.java:423)
           at org.apache.hadoop.hbase.ipc.RemoteWithExtrasException.instantiateException(RemoteWithExtrasException.java:100)
           at org.apache.hadoop.hbase.ipc.RemoteWithExtrasException.unwrapRemoteException(RemoteWithExtrasException.java:90)
           at org.apache.hadoop.hbase.shaded.protobuf.ProtobufUtil.makeIOExceptionOfException(ProtobufUtil.java:359)
           at org.apache.hadoop.hbase.shaded.protobuf.ProtobufUtil.handleRemoteException(ProtobufUtil.java:347)
           at org.apache.hadoop.hbase.client.ScannerCallable.close(ScannerCallable.java:316)
           at org.apache.hadoop.hbase.client.ScannerCallable.rpcCall(ScannerCallable.java:237)
           at org.apache.hadoop.hbase.client.ScannerCallable.rpcCall(ScannerCallable.java:58)
           at org.apache.hadoop.hbase.client.RegionServerCallable.call(RegionServerCallable.java:127)
           at org.apache.hadoop.hbase.client.ScannerCallableWithReplicas.call(ScannerCallableWithReplicas.java:136)
           at org.apache.hadoop.hbase.client.ScannerCallableWithReplicas.call(ScannerCallableWithReplicas.java:58)
           at org.apache.hadoop.hbase.client.RpcRetryingCallerImpl.callWithoutRetries(RpcRetryingCallerImpl.java:192)
           at org.apache.hadoop.hbase.client.ClientScanner.call(ClientScanner.java:268)
           at org.apache.hadoop.hbase.client.ClientScanner.close(ClientScanner.java:557)
           at org.apache.phoenix.iterate.ScanningResultIterator.close(ScanningResultIterator.java:81)
           at org.apache.phoenix.iterate.TableResultIterator.close(TableResultIterator.java:144)
           at org.apache.phoenix.iterate.LookAheadResultIterator$1.close(LookAheadResultIterator.java:42)
           at org.apache.phoenix.iterate.BaseResultIterators.close(BaseResultIterators.java:1440)
           at org.apache.phoenix.iterate.BaseResultIterators.getIterators(BaseResultIterators.java:1352)
           at org.apache.phoenix.iterate.BaseResultIterators.getIterators(BaseResultIterators.java:1239)
           at org.apache.phoenix.iterate.RoundRobinResultIterator.getIterators(RoundRobinResultIterator.java:176)
           at org.apache.phoenix.iterate.RoundRobinResultIterator.next(RoundRobinResultIterator.java:91)
           at org.apache.phoenix.jdbc.PhoenixResultSet.next(PhoenixResultSet.java:805)
           at com.thtf.bigdata.functions.PhoenixFunctions$.getItemMaxValue(PhoenixFunctions.scala:130)
           at com.thtf.bigdata.spark.CalculateKafkaData$$anonfun$main$1.apply(CalculateKafkaData.scala:157)
           at com.thtf.bigdata.spark.CalculateKafkaData$$anonfun$main$1.apply(CalculateKafkaData.scala:145)
           at org.apache.spark.streaming.dstream.DStream$$anonfun$foreachRDD$1$$anonfun$apply$mcV$sp$3.apply(DStream.scala:628)
           at org.apache.spark.streaming.dstream.DStream$$anonfun$foreachRDD$1$$anonfun$apply$mcV$sp$3.apply(DStream.scala:628)
           at org.apache.spark.streaming.dstream.ForEachDStream$$anonfun$1$$anonfun$apply$mcV$sp$1.apply$mcV$sp(ForEachDStream.scala:51)
           at org.apache.spark.streaming.dstream.ForEachDStream$$anonfun$1$$anonfun$apply$mcV$sp$1.apply(ForEachDStream.scala:51)
           at org.apache.spark.streaming.dstream.ForEachDStream$$anonfun$1$$anonfun$apply$mcV$sp$1.apply(ForEachDStream.scala:51)
           at org.apache.spark.streaming.dstream.DStream.createRDDWithLocalProperties(DStream.scala:416)
           at org.apache.spark.streaming.dstream.ForEachDStream$$anonfun$1.apply$mcV$sp(ForEachDStream.scala:50)
           at org.apache.spark.streaming.dstream.ForEachDStream$$anonfun$1.apply(ForEachDStream.scala:50)
           at org.apache.spark.streaming.dstream.ForEachDStream$$anonfun$1.apply(ForEachDStream.scala:50)
           at scala.util.Try$.apply(Try.scala:192)
           at org.apache.spark.streaming.scheduler.Job.run(Job.scala:39)
           at org.apache.spark.streaming.scheduler.JobScheduler$JobHandler$$anonfun$run$1.apply$mcV$sp(JobScheduler.scala:257)
           at org.apache.spark.streaming.scheduler.JobScheduler$JobHandler$$anonfun$run$1.apply(JobScheduler.scala:257)
           at org.apache.spark.streaming.scheduler.JobScheduler$JobHandler$$anonfun$run$1.apply(JobScheduler.scala:257)
           at scala.util.DynamicVariable.withValue(DynamicVariable.scala:58)
           at org.apache.spark.streaming.scheduler.JobScheduler$JobHandler.run(JobScheduler.scala:256)
           at java.util.concurrent.ThreadPoolExecutor.runWorker(ThreadPoolExecutor.java:1149)
           at java.util.concurrent.ThreadPoolExecutor$Worker.run(ThreadPoolExecutor.java:624)
           at java.lang.Thread.run(Thread.java:748)
   Caused by: org.apache.hadoop.hbase.ipc.RemoteWithExtrasException(org.apache.hadoop.hbase.UnknownScannerException): org.apache.hadoop.hbase.UnknownScannerException: Unknown scanner '-8767951189578287340'. This can happen due to any of the following reasons: a) Scanner id given is wrong, b) Scanner lease expired because of long wait between consecutive client checkins, c) Server may be closing down, d) RegionServer restart during upgrade.
   If the issue is due to reason (b), a possible fix would be increasing the value of'hbase.client.scanner.timeout.period' configuration.
           at org.apache.hadoop.hbase.regionserver.RSRpcServices.getRegionScanner(RSRpcServices.java:2930)
           at org.apache.hadoop.hbase.regionserver.RSRpcServices.scan(RSRpcServices.java:3287)
           at org.apache.hadoop.hbase.shaded.protobuf.generated.ClientProtos$ClientService$2.callBlockingMethod(ClientProtos.java:42002)
           at org.apache.hadoop.hbase.ipc.RpcServer.call(RpcServer.java:409)
           at org.apache.hadoop.hbase.ipc.CallRunner.run(CallRunner.java:131)
           at org.apache.hadoop.hbase.ipc.RpcExecutor$Handler.run(RpcExecutor.java:324)
           at org.apache.hadoop.hbase.ipc.RpcExecutor$Handler.run(RpcExecutor.java:304)
   
           at org.apache.hadoop.hbase.ipc.AbstractRpcClient.onCallFinished(AbstractRpcClient.java:387)
           at org.apache.hadoop.hbase.ipc.AbstractRpcClient.access$100(AbstractRpcClient.java:95)
           at org.apache.hadoop.hbase.ipc.AbstractRpcClient$3.run(AbstractRpcClient.java:410)
           at org.apache.hadoop.hbase.ipc.AbstractRpcClient$3.run(AbstractRpcClient.java:406)
           at org.apache.hadoop.hbase.ipc.Call.callComplete(Call.java:103)
           at org.apache.hadoop.hbase.ipc.Call.setException(Call.java:118)
           at org.apache.hadoop.hbase.ipc.NettyRpcDuplexHandler.readResponse(NettyRpcDuplexHandler.java:161)
           at org.apache.hadoop.hbase.ipc.NettyRpcDuplexHandler.channelRead(NettyRpcDuplexHandler.java:191)
           at org.apache.hbase.thirdparty.io.netty.channel.AbstractChannelHandlerContext.invokeChannelRead(AbstractChannelHandlerContext.java:362)
           at org.apache.hbase.thirdparty.io.netty.channel.AbstractChannelHandlerContext.invokeChannelRead(AbstractChannelHandlerContext.java:348)
           at org.apache.hbase.thirdparty.io.netty.channel.AbstractChannelHandlerContext.fireChannelRead(AbstractChannelHandlerContext.java:340)
           at org.apache.hbase.thirdparty.io.netty.handler.codec.ByteToMessageDecoder.fireChannelRead(ByteToMessageDecoder.java:310)
           at org.apache.hbase.thirdparty.io.netty.handler.codec.ByteToMessageDecoder.channelRead(ByteToMessageDecoder.java:284)
           at org.apache.hbase.thirdparty.io.netty.channel.AbstractChannelHandlerContext.invokeChannelRead(AbstractChannelHandlerContext.java:362)
           at org.apache.hbase.thirdparty.io.netty.channel.AbstractChannelHandlerContext.invokeChannelRead(AbstractChannelHandlerContext.java:348)
           at org.apache.hbase.thirdparty.io.netty.channel.AbstractChannelHandlerContext.fireChannelRead(AbstractChannelHandlerContext.java:340)
           at org.apache.hbase.thirdparty.io.netty.handler.timeout.IdleStateHandler.channelRead(IdleStateHandler.java:286)
           at org.apache.hbase.thirdparty.io.netty.channel.AbstractChannelHandlerContext.invokeChannelRead(AbstractChannelHandlerContext.java:362)
           at org.apache.hbase.thirdparty.io.netty.channel.AbstractChannelHandlerContext.invokeChannelRead(AbstractChannelHandlerContext.java:348)
           at org.apache.hbase.thirdparty.io.netty.channel.AbstractChannelHandlerContext.fireChannelRead(AbstractChannelHandlerContext.java:340)
           at org.apache.hbase.thirdparty.io.netty.channel.DefaultChannelPipeline$HeadContext.channelRead(DefaultChannelPipeline.java:1359)
           at org.apache.hbase.thirdparty.io.netty.channel.AbstractChannelHandlerContext.invokeChannelRead(AbstractChannelHandlerContext.java:362)
           at org.apache.hbase.thirdparty.io.netty.channel.AbstractChannelHandlerContext.invokeChannelRead(AbstractChannelHandlerContext.java:348)
           at org.apache.hbase.thirdparty.io.netty.channel.DefaultChannelPipeline.fireChannelRead(DefaultChannelPipeline.java:935)
           at org.apache.hbase.thirdparty.io.netty.channel.nio.AbstractNioByteChannel$NioByteUnsafe.read(AbstractNioByteChannel.java:138)
           at org.apache.hbase.thirdparty.io.netty.channel.nio.NioEventLoop.processSelectedKey(NioEventLoop.java:645)
           at org.apache.hbase.thirdparty.io.netty.channel.nio.NioEventLoop.processSelectedKeysOptimized(NioEventLoop.java:580)
           at org.apache.hbase.thirdparty.io.netty.channel.nio.NioEventLoop.processSelectedKeys(NioEventLoop.java:497)
           at org.apache.hbase.thirdparty.io.netty.channel.nio.NioEventLoop.run(NioEventLoop.java:459)
           at org.apache.hbase.thirdparty.io.netty.util.concurrent.SingleThreadEventExecutor$5.run(SingleThreadEventExecutor.java:858)
           at org.apache.hbase.thirdparty.io.netty.util.concurrent.DefaultThreadFactory$DefaultRunnableDecorator.run(DefaultThreadFactory.java:138)
           ... 1 more
   ```

   

### 2. 报错：org.apache.hadoop.hbase.DoNotRetryIOException

   ```
   WARN AsyncRequestFutureImpl: id=1, table=EMS5:tbl_energy_other_hour, attempt=2/36, failed=6ops, last exception=org.apache.hadoop.hbase.DoNotRetryIOException: org.apache.hadoop.hbase.DoNotRetryIOException: ERROR 2008 (INT10): ERROR 2008 (INT10): Unable to find cached index metadata.  key=6586705866314228252 region=EMS5:tbl_energy_other_hour,\x05\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00,1563523804064.f49435970e66f28199dc25de79f76c16.host=snn-4.hadoop,16020,1568013739816 Index update failed
   
   Caused by: java.sql.SQLException: ERROR 2008 (INT10): Unable to find cached index metadata.  key=6586705866314228252 region=EMS5:tbl_energy_other_hour,\x05\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00,1563523804064.f49435970e66f28199dc25de79f76c16.host=snn-4.hadoop,16020,1568013739816
   
   19/09/09 17:00:04 WARN MutationState: Swallowing exception and retrying after clearing meta cache on connection. java.sql.SQLException: ERROR 2008 (INT10): Unable to find cached index metadata.  ERROR 2008 (INT10): ERROR 2008 (INT10): Unable to find cached index metadata.  key=6586705866314228252 region=EMS5:tbl_energy_other_hour,\x05\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00,1563523804064.f49435970e66f28199dc25de79f76c16.host=snn-4.hadoop,16020,1568013739816 Index update failed
   ```

### 3. 在集群上运行sparkstreaming程序通过phoenix写入hbase，报错：

   ```
   java.sql.SQLException: No suitable driver found for jdbc:phoenix:snn:/hbase-unsecure
   ```

   原因：除了phoenix相关包外，还缺少hbase相关jar包

   解决办法：--jars提交相关jar包

### 4. 通过jdbc连接phoenix

   phoenix提供了对hbase的SQL访问支持。一般客户端应该使用phoenix-xxx-client.jar。

   其driver和url为：

   ```
   org.apache.phoenix.jdbc.PhoenixDriver
   jdbc:phoenix:host:2181
   ```

   问题在于，phoenix-xxx-client.jar很大，含有大量第三方类，从而很容易引起类冲突。所以一个更好的方法是使用phoenix-xxx-thin-client.jar。这个jar很小，类冲突的可能性小多了。

   其driver和url为：

   ```
   org.apache.phoenix.queryserver.client.Driver
   jdbc:phoenix:thin:url=http://host:8765;serialization=PROTOBUF
   ```

### 5.  in和exists

   ```sql
   --使用in关键字
   SELECT "build_code","collector_code","timestamp" FROM EMS5."data_access" WHERE "type" = 2
   AND
   	("build_code","collector_code","timestamp")
   NOT IN
   	(SELECT "build_code","collector_code","timestamp" FROM EMS5."data_access" WHERE "type" = 1)
   
   --使用 exists关键字，没有关联，无结果
   SELECT "build_code","collector_code","timestamp" FROM EMS5."data_access" WHERE "type" = 2
   AND NOT EXISTS
   	(SELECT "build_code","collector_code","timestamp" FROM EMS5."data_access" WHERE "type" = 1)
   
   --使用 exists关键字
   SELECT "build_code","collector_code","timestamp" FROM EMS5."data_access" s WHERE "type" = 2
   AND NOT EXISTS
   	(SELECT "build_code","collector_code","timestamp" FROM EMS5."data_access" ss WHERE "type" = 1
   		AND (s."build_code",s."collector_code",s."timestamp")=(ss."build_code",ss."collector_code",ss."timestamp"))
   --		AND s."build_code"=ss."build_code" 
   --		AND s."collector_code"=ss."collector_code" 
   --		AND s."timestamp"=ss."timestamp")
   ```
