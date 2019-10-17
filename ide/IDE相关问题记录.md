# IDE相关问题记录

### IDEA

1. 使用SQL语句时出现警告

   ```
   No data sources are configured to run this SQL and provide advanced code assistance. Disable this inspection via problem menu (Alt+Enter). SQL dialect is not configured. 
   ```

   原因：SQL方言没有配置

   解决办法：

   1. 在 File->Setting->Languages & Frameworks->SQL Dialects中设置Project SQL Dialect 为自己的数据库
   2. 最好是项目关联到真正的数据库，在窗口右侧Database中配置，这样IDEA可以帮你检查SQL字段错误
   3. 标准SQL是以select等等开头，使用mybatis就不能用，只能关警告等

2. intelij idea 2019.2 中文字体渲染异常解决方法

   1. ctrl+shift+alt+/ 选择自己电脑里面的jdk 重启后再按一遍 重新选默认的jdk
   2. 如果第一步没有解决，可以重启电脑再打开IDEA，如果仍没有解决，再次重复第一步。

---

### Eclipse

1. 修改代码后，代码和逻辑均没有问题，运行出现问题

   原因：其他文件有错误代码时，编译器不会重新编译修改后的代码

   解决办法：修改或注释掉其他文件的错误代码，重新编译
   
2. Eclipse的java代码出错：The import org.apache cannot be resolved

   编写scala代码没问题，Java代码导包报错

   原因：相关jar包有问题，重新下载
   
3. Eclipse集成Scala IDE，编写代码时出现疑似编译问题

   ![](E:\学习笔记\问题记录需要整理\可能是编译问题\编译错误2.png)

   问题描述：

   1. 常见为代码块的一个`{`报错，报错信息为`def while$12():<error>`
   2. 编译时随机出现的问题
   3. 任意修改内容重新保存，报错可能消失，可能还在
4. 重新保存错误消失之后，下次修改内容保存的时候，同一位置的错误还可能出现
   
   解决办法：添加或删除任意空白内容，再次保存即可解决
