

# Tomcat配置Manager功能并开启远程访问

* Manager功能访问用户权限配置

  修改conf/tomcat-users.xml文件，添加以下内容：

  ```xml
  <!-- 页面访问manager -->
  <role rolename="manager-gui"/>
  <!-- 接口访问manager -->
  <role rolename="manager-script"/>
  <!-- 拥有访问manager权限的用户 -->
  <user username="tomcat" password="tomcat" roles="manager-gui,manager-script"/>
  ```

  >请注意，从Tomcat 7开始，使用管理器应用程序所需的角色已从单个管理器角色更改为以下四个角色。您将需要分配想要访问的功能所需的角色。
  >
  >* manager-gui-允许访问HTML GUI和状态页面
  >* manager-script-允许访问文本界面和状态页面
  >* manager-jmx-允许访问JMX代理和状态页面
  >* manager- status-仅允许访问状态页面
  >
  >HTML界面受CSRF保护，但text和JMX界面不受保护。维护CSRF保护：
  >
  >* 具有manager-gui角色的用户不应被授予manager-script或manager-jmx角色。
  >* 如果通过浏览器访问了文本或jmx接口（例如，由于这些接口是针对非人类的工具而进行测试），则此后必须关闭浏览器以终止会话。

* Manager功能访问地址权限配置，开启远程访问，有两种方式：

  1. 打开conf/Catalina/localhost/manager.xml文件，若没有则新建：

     ```xml
     <Context privileged="true" antiResourceLocking="false"   
              docBase="${catalina.home}/webapps/manager">  
                  <Valve className="org.apache.catalina.valves.RemoteAddrValve" allow="^.*$" />  
   </Context> 
     ```
  
  2. 修改webapps/manager/META-INF/context.xml文件：
  
     添加允许访问的IP地址：
  
     ```xml
     <Context antiResourceLocking="false" privileged="true" >
       <Valve className="org.apache.catalina.valves.RemoteAddrValve"
              allow="127\.\d+\.\d+\.\d+|::1|0:0:0:0:0:0:0:1|10.10.82.73" />
       <Manager sessionAttributeValueClassNameFilter="java\.lang\.(?:Boolean|Integer|Long|Number|String)|org\.apache\.catalina\.filters\.CsrfPreventionFilter\$LruCache(?:\$1)?|java\.util\.(?:Linked)?HashMap"/>
     </Context>
     ```
  
     或，注释掉value标签（允许所有外部IP访问）：
  
     ```xml
     <Context antiResourceLocking="false" privileged="true" >
       <!--
       <Valve className="org.apache.catalina.valves.RemoteAddrValve"
              allow="127\.\d+\.\d+\.\d+|::1|0:0:0:0:0:0:0:1" />
       -->
       <Manager sessionAttributeValueClassNameFilter="java\.lang\.(?:Boolean|Integer|Long|Number|String)|org\.apache\.catalina\.filters\.CsrfPreventionFilter\$LruCache(?:\$1)?|java\.util\.(?:Linked)?HashMap"/>
     </Context>
     ```
  
     