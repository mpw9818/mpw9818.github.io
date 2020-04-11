---
title: 解决Get/Post请求中文乱码问题
date: 2019-12-04 22:09:21
tags:
   Get/Post
categories:
  javaWeb
---
## 给服务器设置编码
### Get请求  
&emsp;&emsp;在url地址栏输入回车是get请求，form表单不写method="post",默认就是get请求。
&emsp;&emsp;get请求过来的数据，在url地址栏上就已经经过编码了，所以我们取到的就是乱码。<font color="red">Tomcat8.0之前</font>，用get请求访问tomcat时的编码方式，默认的编码方式使用ISO-8859-1。
&emsp;&emsp;解决方式：
&emsp;&emsp;1.先让文字回到ISO-8859-1对应的字节数组，然后再按utf-8组拼字符串，
```java
String name = new String(request.getParameter("name").getBytes("ISO-8859-1"),"utf-8")
```
&emsp;&emsp;2.在Tomcat配置文件server.xml中添加URIEncoding="utf-8",但是一般不用.定位到 
```java
<Connector connectionTimeout="20000" port="8080" protocol="HTTP/1.1" redirectPort="8443" URIEncoding="utf-8"/>
```
&emsp;&emsp;<font color="green">从tomcat8.0开始，用get请求访问tomcat时的编码方式不再是ISO-8859-1，而变成了UTF-8 所有Tomcat8.0不用处理get请求乱码问题</font>

### Post请求
&emsp;&emsp;是以二进制流的形式发送到的服务器。服务器收到数据后。默认以iso-8859-1进行编码。 
&emsp;&emsp;解决方式：
&emsp;&emsp;1.<font color="red">这种方法必须获取请求参数之前调用</font>
```java
request.setCharacterEncoding("utf-8");  //设置请求体里面的文字编码
```
&emsp;&emsp;2.
```java
String name = new String(request.getParameter("参数名").getBytes("iso-8859-1"),"utf-8");
```
 ## 给浏览器设置编码 (常用)
```java
response.setContentType("text/html;charset=utf-8"); //(UTF-8是国际通用编码)
```