# IDEA创建SpringBoot项目，并部署外部tomcat

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

### 6.配置外部tomcat

配置外部tomcat，需要修改以下三个位置：

* 修改打包方式

  在pom.xml文件中，将`packaging`标签内容改为war，如果没有此标签，直接添加即可

  ```
  <packaging>war</packaging>
  ```

* 在maven中设置tomcat相关依赖，有两种方法

  1. 排除springboot自带tomcat

     **注意：采用此种方法，将无法使用通过启动springboot入口类（Application.java）来部署项目的方式！**

     ```xml
     <dependency>
         <groupId>org.springframework.boot</groupId>
         <artifactId>spring-boot-starter-web</artifactId>
         <exclusions>
             <exclusion>
                 <groupId>org.springframework.boot</groupId>
                 <artifactId>spring-boot-starter-tomcat</artifactId>
             </exclusion>
         </exclusions>
     </dependency>
     ```

  2. 设置springboot自带tomcat依赖的作用范围

     推荐使用此种方法，这样配置可以使用内嵌tomcat调试，同时也可以打包部署到外部tomcat

     ```xml
     <dependency>
         <groupId>org.springframework.boot</groupId>
         <artifactId>spring-boot-starter-tomcat</artifactId>
         <!-- 在编译和测试时使用，不会打包到lib目录 -->
         <scope>provided</scope>
     </dependency>
     ```

* 将springboot入口类（Application.java）继承`SpringBootServletInitializer`类并重写`configure()`方法

    ```java
    @SpringBootApplication
    public class Application extends SpringBootServletInitializer {

        public static void main(String[] args) {
            SpringApplication.run(Application.class, args);
        }

        @Override
        protected SpringApplicationBuilder configure(SpringApplicationBuilder builder) {
            return builder.sources(Application.class);
        }
    }
    ```

以上操作完成，就可以将项目部署到外部tomcat服务器来运行了。

### 7.技巧延伸

此外，通过springboot内嵌服务器部署时，默认是项目名缺省的。可以通过配置application.yml文件来给web项目加上前缀。其中prefix为前缀名。

```
server:
  servlet:
    context-path: /prefix/
```

这个前缀会在war包中失效，取而代之的是war包名称，如果war包名称和prefix相同的话，那么调试环境和正式部署环境就是一个request地址了。