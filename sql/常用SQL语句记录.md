# 常用SQL语句记录

## 1. 导出表为文件

```mysql
SELECT * FROM USER INTO OUTFILE 'F:/sql/sqltest.txt'
    FIELDS
        TERMINATED BY '\、'
        OPTIONALLY ENCLOSED BY '\"'
    LINES
        STARTING BY '\>'
        TERMINATED BY '\r\n';
```

## 2. 查询数据库所有表名

```mysql
SHOW TABLES FROM my_test_db01 ;
```

```mysql
SELECT table_name AS ts FROM information_schema.tables WHERE table_schema='my_test_db01';
```

## 3. 存储过程：遍历所有表名

```mysql
-- 改变结尾符号；为//
DELIMITER //
-- 创建存储过程
CREATE PROCEDURE traverse_all_tables()
BEGIN
DECLARE each_table_name VARCHAR(90);
DECLARE ergodic INT DEFAULT 1;
-- 声明游标
DECLARE getcategory CURSOR FOR SELECT table_name FROM information_schema.tables WHERE table_schema='my_test_db01';
-- 声明越界标识
DECLARE EXIT HANDLER FOR NOT FOUND SET ergodic:=0;
-- 打开游标
OPEN getcategory;
-- 遍历
REPEAT
FETCH getcategory INTO each_table_name;
SELECT each_table_name;
UNTIL ergodic=0 END REPEAT;
-- 关闭游标
CLOSE getcategory;
END //
DELIMITER ;

-- 调用存储过程
CALL traverse_all_tables ;
-- 删除存储过程
DROP PROCEDURE traverse_all_tables ;
```

## 4. 拼接SQL语句

```mysql
SET @sql=CONCAT("select * from user into outfile 'F:/java/mysql/",DATE_FORMAT(NOW(),'%Y%m%d%H%i00'),".txt'");
PREPARE execsql FROM @sql;
EXECUTE execsql;
```

## 5. 存储过程：遍历所有表，导出为文件，文件名为`表名_当前时间.txt`

```mysql
DELIMITER //
CREATE PROCEDURE traverse_all_tables()
BEGIN
DECLARE each_table_name VARCHAR(90);
DECLARE ergodic INT DEFAULT 1;
DECLARE getcategory CURSOR FOR SELECT table_name FROM information_schema.tables WHERE table_schema='my_test_db01';
DECLARE EXIT HANDLER FOR NOT FOUND SET ergodic:=0;
OPEN getcategory;
REPEAT
FETCH getcategory INTO each_table_name;
-- select each_table_name;
SET @sql=CONCAT("select * from ",each_table_name," into outfile 'F:/java/mysql/",each_table_name,"_",DATE_FORMAT(NOW(),'%Y%m%d%H%i00'),".txt'");
PREPARE execsql FROM @sql;
EXECUTE execsql;
UNTIL ergodic=0 END REPEAT;
CLOSE getcategory;
END //
DELIMITER ;

CALL traverse_all_tables ;
DROP PROCEDURE traverse_all_tables ;
```

## 6. 中文表名乱码解决

```mysql
set names gbk;
```

## 7. 用SQL命令查看Mysql数据库大小,要想知道每个数据库的大小的话，步骤如下：

1. 进入information_schema 数据库（存放了其他的数据库的信息）

   ```mysql
   use information_schema;
   ```

2. 查询所有数据的大小

   ```mysql
   select concat(round(sum(data_length/1024/1024),2),'MB') as data from tables;
   ```

3. 查看指定数据库的大小（比如查看数据库metrosavingexpert的大小）

   ```mysql
   select concat(round(sum(data_length/1024/1024),2),'MB') as data from tables where table_schema='metrosavingexpert';
   ```

4. 查看指定数据库的某个表的大小（比如查看数据库home中 members 表的大小）

   ```mysql
   select concat(round(sum(data_length/1024/1024),2),'MB') as data from tables where table_schema='home' and table_name='members';
   ```

5. 查看指定数据库所有表记录数

   ```mysql
   SELECT table_name,table_rows FROM TABLES WHERE TABLE_SCHEMA = 'metrosavingexpert' ORDER BY table_rows DESC;
   ```

   

