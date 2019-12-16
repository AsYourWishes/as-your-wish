# Kafka相关问题记录

1. Eclipse中调试spark streaming程序读取kafka数据，出现问题：

   ```
   WARN [Consumer clientId=consumer-1, groupId=console-consumer-950] Connection to node -1 could not be established. Broker may not be available.
   ```

   原因：同时连接有线和无线

   解决办法：关闭电脑无线网络
   
2. kafka手动控制offset，第一次消费没有读取到offset时，设置的offset为0，报错；同时，用kafka命令消费时出错

   原因：topic中有数据过期

3. kafka`auto.offset.reset`参数设置各个值的含义解释

   * earliest 

     当各分区下有已提交的offset时，从提交的offset开始消费；无提交的offset时，从头开始消费 

   * latest 

     当各分区下有已提交的offset时，从提交的offset开始消费；无提交的offset时，消费新产生的该分区下的数据

   * none 
     topic各分区都存在已提交的offset时，从offset后开始消费；只要有一个分区不存在已提交的offset，则抛出异常