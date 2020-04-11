---
title: Tomcat服务器和eclipse部署Web应用
date: 2019-12-02 22:16:04
tags:
   Web
categories:
  javaWeb
---

## 一、下载和安装Tomcat
&emsp;&emsp;官方站点：http://tomcat.apache.org/index.html 获取Tomcat安装程序包.

&emsp;&emsp;tar.gz文件是Linux操作系统下的压缩版本，解压到一个非中文、无空格目录即可。zip文件也是压缩版本，可用于Linux和Windows，解压到一个非中文、无空格目录即可。exe文件是Windows系统下的安装版本，双击运行按照向导进行安装。无论是解压还是安装的，都需要配置JAVA_HOME环境变量，该变量的值是JDK的安装目录

### Tomcat目录结构：
![](https://i.loli.net/2019/12/02/zl1KSnZYO8PHJyo.png "tomcat目录结构")

## 二、eclipse下部署Web应用
步骤：
&emsp;&emsp;1.创建Runtime：开发WEB应用之前，必须要创建一个Runtime。Runtime并不是JRE，而是指WEB应用在开发的时候使用的应用服务器（如Tomcat等）。
&emsp;&emsp;操作流程：Window——>Preferences——>Server——>Runtime Environment ，Add对应的Tomcat版本，next， 将Tomcat的安装根路径加进来，选上自己的jdk版本，然后Finish完成。

&emsp;&emsp;2.创建Server：创建了Runtime，只是说明开发时使用的WEB应用运行时环境。在这个环境的基础上，eclipse要托管服务器，才能真正在eclipse里面集成开发。eclipse底下的server窗口将Tomcat托管上。

&emsp;&emsp;3.创建web项目：File——>New——>Dynamic Web Object,

![](https://i.loli.net/2019/12/02/oQpvJDX6x8lF5C7.png "创建web项目")

&emsp;&emsp;4.部署WEB应用:
&emsp;![](https://i.loli.net/2019/12/02/TjsVe1LUWa6DP2I.png "部署WEB应用")
&emsp;![](https://i.loli.net/2019/12/02/n4TjLsMKaF9812q.png "部署WEB应用过程成功")
<br>
&emsp;&emsp;5.运行WEB应用：项目run on server
<br>
### web工程目录结构：
![](https://i.loli.net/2019/12/02/RIpTjkMYF9hAPlv.png "web工程目录结构")


### 部署war包到Tomcat
&emsp;&emsp;war文件：war表示一个WEB应用压缩包。是一个可直接运行的web模块，通常用于网站，将其打成包部署到容器中。以Tomcat来说，将war包放在其\webapps\目录下，然后启动Tomcat，这个包就自动解压，相当于发布项目。直接可以用eclipse导出war file
&emsp;&emsp;1.一般是开发打war包给测试，比如说现在测试拿到一个war包，名字叫Servlet01.war。
&emsp;&emsp;2.打开Tomcat的安装路径，假设是“D:\Tomcat\apache-tomcat-7.0.68”，进入到其webapps文件夹中，把 Servlet01.war放到 webapps文件夹。
&emsp;&emsp;3.启动Tomcat。（双击Tomcat-bin-startup.bat）
&emsp;&emsp;4.在浏览器输入“http:localhost:tomcat_port/Servlet01即可打开Servlet01项目的 index.html页面（port是tomcat的端口号）。如果Servlet01项目没有index.html页面，那就需要打开其他相应的页面。


