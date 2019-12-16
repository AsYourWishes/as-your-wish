# Hadoop相关问题记录

### 1. yarn REST API相关问题

1. 使用Cluster New Application API时报错

   URI

   ```
   http://nn:8088/ws/v1/cluster/apps/new-application
   ```

   报错

   ```json
   {"RemoteException":{"exception":"AuthorizationException","message":"Unable to obtain user name, user not authenticated","javaClassName":"org.apache.hadoop.security.authorize.AuthorizationException"}}
   ```


### 2. 权限问题

报错：

```shell
Exception in thread "main" org.apache.hadoop.security.AccessControlException: Permission denied: user=root, access=WRITE, inode="/user/root/.sparkStaging/application_1528693980561_0018":hdfs:hdfs:drwxr-xr-x
```

解决办法：

修改权限，在hdfs-site.xml中加入以下参数

```xml
<property>
  <name>dfs.permissions</name>
  <value>false</value>
  <description>
    If "true", enable permission checking in HDFS.
    If "false", permission checking is turned off,
    but all other behavior is unchanged.
    Switching from one parameter value to the other does not change the mode,
    owner or group of files or directories.
  </description>
</property>
```