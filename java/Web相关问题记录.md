# Web相关问题记录

* curl

  curl发送get请求：

  ```
  curl -X GET "www.baidu.com"
  ```

  

  curl发送post请求：

  ```
  curl -X POST "http://nn:8088/ws/v1/cluster/apps/new-application"
  ```

* web部署相关问题

  在搭建好的springMVC项目中，加入以下依赖后，启动tomcat部署时，会报错：

  依赖：

  ```xml
  <!-- 部署web应用时，报错 -->
      <dependency><!-- Hbase -->
        <groupId>org.apache.hbase</groupId>
        <artifactId>hbase-client</artifactId>
        <version>${hbase.version}</version>
      </dependency>
      <dependency>
        <groupId>org.apache.hbase</groupId>
        <artifactId>hbase-common</artifactId>
        <version>${hbase.version}</version>
      </dependency>
      <dependency>
        <groupId>org.apache.hbase</groupId>
        <artifactId>hbase-server</artifactId>
        <version>${hbase.version}</version>
      </dependency>
  ```

  报错：

  ```
  严重 [RMI TCP Connection(3)-127.0.0.1] org.apache.tomcat.util.modeler.BaseModelMBean.invoke Exception invoking method [createStandardContext]
  	javax.management.RuntimeOperationsException: Exception invoking method [manageApp]
  
  Caused by: java.lang.IllegalStateException: Error starting child
  
  Caused by: org.apache.catalina.LifecycleException: Failed to start component [StandardEngine[Catalina].StandardHost[localhost].StandardContext[/springProject]]
  
  Caused by: java.lang.NoSuchMethodError: org.apache.jasper.compiler.Localizer.getMessage(Ljava/lang/String;Ljava/lang/String;)Ljava/lang/String;
  ```

  原因：依赖冲突，冲突的jar为：`javax.servlet.jsp-2.3.2.jar`

  注意：使用Maven Helper插件排查时，此jar包不报红

  解决办法：在`hbase-server`依赖中，排除此jar包依赖

  ```
  	<dependency>
        <groupId>org.apache.hbase</groupId>
        <artifactId>hbase-server</artifactId>
        <version>${hbase.version}</version>
        <exclusions>
          <exclusion>
            <artifactId>javax.servlet.jsp</artifactId>
            <groupId>org.glassfish.web</groupId>
          </exclusion>
        </exclusions>
      </dependency>
  ```

  

