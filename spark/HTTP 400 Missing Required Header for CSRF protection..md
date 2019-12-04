# HTTP 400 "Missing Required Header for CSRF protection."

### 背景

最近有个需求，通过发送HTTP请求给Apache Livy服务，来管理spark任务。

### 问题描述

使用IDEA插件HTTP Client，发送POST请求时报错”Missing Required Header for CSRF protection.“

详细信息如下：

```htm
<html>
<head>
<meta http-equiv="Content-Type" content="text/html;charset=ISO-8859-1"/>
<title>Error 400 </title>
</head>
<body>
<h2>HTTP ERROR: 400</h2>
<p>Problem accessing /sessions. Reason:
<pre>    Missing Required Header for CSRF protection.</pre></p>
<hr /><a href="http://eclipse.org/jetty">Powered by Jetty:// 9.3.24.v20180605</a><hr/>
</body>
</html>
```

### 解决办法

添加一个请求头：

```
X-Requested-By: admin
```

### 相关解释

https://github.com/MicrosoftDocs/azure-docs/issues/10457

>如果没有额外的头，由于Ambari中严格的安全性，POST请求可能会失败。
>
>如果需要避免代码更改，可以放宽安全性。您可以在Ambari配置中禁用Livy的CSRF，这可能会稍微降低安全性，因此这可能是不可取的。