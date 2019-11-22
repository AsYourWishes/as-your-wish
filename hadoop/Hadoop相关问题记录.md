# Hadoop相关问题记录

### yarn REST API相关问题

1. 使用Cluster New Application API时报错

   URI

   ```
   http://nn:8088/ws/v1/cluster/apps/new-application
   ```

   报错

   ```json
   {"RemoteException":{"exception":"AuthorizationException","message":"Unable to obtain user name, user not authenticated","javaClassName":"org.apache.hadoop.security.authorize.AuthorizationException"}}
   ```

   

