# maven依赖

1. 版本信息

   ```
   hadoop		3.1.1.3.1.0.0-78
   spark		2.3.2.3.1.0.0-78
   kafka		2.0.0.3.1.0.0-78
   storm		1.2.1.3.1.0.0-78
   hbase		2.0.2.3.1.0.0-78
   phoenix		5.0.0.3.1.0.0-78
   zk			3.4.6.3.1.0.0-78
   
   hive		3.1.0
   sqoop		1.4.7
   oozie		4.3.1
   zeppelin	0.8.0
   ignite		2.7.5
   ```

2. maven镜像地址

   ```xml
   <repositories>
       <repository>
           <id>hortonworks</id>
           <name>hortonworks Repository</name>
           <url>http://repo.hortonworks.com/content/repositories/releases/</url>
       </repository>
   
       <repository>
           <id>SpringPlugins</id>
           <name>Spring Plugins</name>
           <url>http://repo.spring.io/plugins-release/</url>
       </repository>
   </repositories>
   ```

3. pom文件中依赖的版本控制

   ```xml
   <!--  spring  -->
   	<spring.version>4.3.18.RELEASE</spring.version>
   <!--  Apache  -->
   	<scala.version>2.11.11</scala.version>
       <scala.compat.version>2.11</scala.compat.version>
       <hadoop.version>3.1.1.3.1.0.0-78</hadoop.version>
       <spark.version>2.3.2.3.1.0.0-78</spark.version>
       <kafka.version>2.0.0.3.1.0.0-78</kafka.version>
       <hbase.version>2.0.2.3.1.0.0-78</hbase.version>
       <phoenix.version>5.0.0.3.1.0.0-78</phoenix.version>
   <!--  alibaba  -->
       <fastjson.version>1.2.62</fastjson.version>
   ```

4. pom文件中的依赖

   - spring

       ```xml
       <dependency>
           <groupId>org.springframework</groupId>
           <artifactId>spring-webmvc</artifactId>
           <version>${spring.version}</version>
       </dependency>
       ```

   - spark

     ```xml
     <!--  Apache  -->
     <dependency><!-- spark -->
       <groupId>org.apache.spark</groupId>
         <artifactId>spark-core_${scala.compat.version}</artifactId>
         <version>${spark.version}</version>
         <scope>provided</scope>
     </dependency>
     <dependency>
         <groupId>org.apache.spark</groupId>
         <artifactId>spark-streaming-kafka-0-10_${scala.compat.version}</artifactId>
         <version>${spark.version}</version>
     </dependency>
     <dependency>
         <groupId>org.apache.spark</groupId>
         <artifactId>spark-streaming_${scala.compat.version}</artifactId>
         <version>${spark.version}</version>
     </dependency>
     <dependency>
         <groupId>org.apache.spark</groupId>
         <artifactId>spark-sql_${scala.compat.version}</artifactId>
         <version>${spark.version}</version>
     </dependency>
     ```

   - kafka

     ```xml
     <dependency><!-- kafka -->
         <groupId>org.apache.kafka</groupId>
         <artifactId>kafka_2.11</artifactId>
         <version>${kafka.version}</version>
     </dependency>
     <dependency>
         <groupId>org.apache.kafka</groupId>
         <artifactId>kafka-streams</artifactId>
         <version>${kafka.version}</version>
     </dependency>
     ```

   - hbase

     ```xml
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

   - phoenix

     ```xml
     <dependency><!-- phoenix -->
         <groupId>org.apache.phoenix</groupId>
         <artifactId>phoenix-core</artifactId>
         <version>${phoenix.version}</version>
     </dependency>
     ```

   - json

     ```xml
     <dependency>
         <groupId>com.alibaba</groupId>
         <artifactId>fastjson</artifactId>
         <version>${fastjson.version}</version>
     </dependency>
     ```

     