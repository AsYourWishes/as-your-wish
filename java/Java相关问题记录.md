# Java相关问题记录

1. Iterator中有数据，后续处理不到

   原因：前面调用了.size()方法，会迭代所有数据

   解决办法：使用.hasNext()方法代替

2. SimpleDateFormat线程不安全的解决方案

   ![](E:\学习笔记\image\SimpleDateFormat线程不安全问题解决方法.png)

#### 重要：

1. Java的ScriptEngine，处理Java动态脚本。