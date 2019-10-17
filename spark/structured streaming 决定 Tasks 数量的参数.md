# structured streaming 决定 Tasks 数量的参数

## 问题

今天在学习 spark structured streaming 时，测试官网的例子发现，job 的 task 数量太多，而测试的时候数据量比较少的话没有必要启动这么多。

![](https://upload-images.jianshu.io/upload_images/19310498-f75258e5d3d0b179.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 解决过程
根据使用 spark streaming 的经验，知道 spark.default.parallelism 默认为 2，所以 stage 0 的 task 数量为 2。

而 stage 1 的 task 数量为 200 不知道是哪个参数决定的，于是去 [spark configuration 官网](http://spark.apache.org/docs/latest/configuration.html) 查找，但是没有找到对应配置。

后来通过一些博客发现 stage 1 的 task 数量是由 spark.sql.shuffle.partitions 参数控制，默认为 200。

我的测试数据量很小，于是修改了以下参数：
```
spark.default.parallelism 1
spark.sql.shuffle.partitions 1
```
问题解决，测试速度也提升了不少。

![](https://upload-images.jianshu.io/upload_images/19310498-027efc3ac083c01f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 延伸
spark 官网没有 spark.sql.shuffle.partitions 这个参数的描述，但是发现了这样一段代码：
>Running the SET -v command will show the entire list of the SQL configuration.
>```
>// spark is an existing SparkSession
>spark.sql("SET -v").show(numRows = 200, truncate = false)
>```

于是在 IDE 中运行了这段代码，得到了spark SQL 对应的配置参数信息：

![](https://upload-images.jianshu.io/upload_images/19310498-7495aee2d629fa48.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

