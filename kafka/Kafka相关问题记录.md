# Kafka相关问题记录

1. Eclipse中调试spark streaming程序读取kafka数据，出现问题：

   ```
   WARN [Consumer clientId=consumer-1, groupId=console-consumer-950] Connection to node -1 could not be established. Broker may not be available.
   ```

   原因：同时连接有线和无线

   解决办法：关闭电脑无线网络
   
2. kafka手动控制offset，第一次消费没有读取到offset时，设置的offset为0，报错；同时，用kafka命令消费时出错

   原因：topic中有数据过期
