# IDEA创建SpringBoot项目

### 1.创建项目

选择Spring Initializr，勾选Default：http://start.spring.io

![](E:\学习笔记\image\idea创建springboot项目1.png)

springboot使用内嵌服务器，Packaging选什么无所谓，如果需要使用外部服务器，必须选择War。

![](E:\学习笔记\image\idea创建springboot项目2.png)

选择依赖，此项目只使用了Spring Web和Thymeleaf

![](E:\学习笔记\image\idea创建springboot项目3.png)

![](E:\学习笔记\image\idea创建springboot项目4.png)

### 2.配置项目

创建项目完成，需要等待maven下载相关依赖。

下面为项目自动生成的入口类，Application结尾，使用`@SpringBootApplication`注解，springboot的核心，帮助完成一些bean的自动注入和初始化。

![](E:\学习笔记\image\idea创建springboot项目5.png)

配置Project的SDK环境和Modules的classpath、resourcepath，默认的一般就可以使用了。

![](E:\学习笔记\image\idea创建springboot项目6.png)

创建常用目录，比如下面的项目结构。WebContent目录存放前端页面相关文件；后端代码所在目录要与springboot入口类平级或者子级，才能被自动扫描注入。

![](E:\学习笔记\image\idea创建springboot项目7.png)

配置项目的访问路径 web root

![](E:\学习笔记\image\idea创建springboot项目8.png)

### 3.配置Spring

修改spring配置文件，将application.properties文件后缀改为.yml

![](E:\学习笔记\image\idea创建springboot项目9.png)

添加配置项，这里只配置了默认端口

![](E:\学习笔记\image\idea创建springboot项目10.png)

### 4.代码测试

写一个controller类测试一下

![](E:\学习笔记\image\idea创建springboot项目11.png)

### 5.运行结果

运行入口类

![](E:\学习笔记\image\idea创建springboot项目12.png)

然后用浏览器访问 localhost:8081/hello，可以显示出hello world

![](E:\学习笔记\image\idea创建springboot项目13.png)