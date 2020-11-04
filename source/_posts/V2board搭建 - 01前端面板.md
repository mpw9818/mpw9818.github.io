---
title: v2board搭建-01前端面板
date: 2020-10-15 10:47:26
tags: v2board
categories:
  v2board搭建
---

V2board是一个开源且易于管理V2Ray程序的可视化用户管理系统，集成了web网站前端+后端，多个v2ray节点+多用户管理+支付+邮件系统，支持TCP、WS+CDN、WS+TLS等协议，前端面板简洁易用。这篇教程主要记录下搭建使用V2board的方法，主要分为：面板搭建、节点对接、支付对接、邮件对接等教程。

机器配置要求最低1核512M内存，建议选择1G内存及以上服务器，选择debian等消耗资源比较少的Linux系统。


## 宝塔面板搭建网站

0、通过SSH工具连接上服务器，更新下软件源和安装开发者工具包（可选）
```linux
yum update -y  ## Debian系统把yum改为apt-get
yum -y groupinstall "Development Tools"  ## Debian系统把yum改为apt-get
```
1、然后在命令行输入下列命令进行安装宝塔面板
```linux
##CentOS系统安装命令：
yum install -y wget && wget -O install.sh http://download.bt.cn/install/install_6.0.sh && sh install.sh
##Debian安装命令：
wget -O install.sh http://download.bt.cn/install/install-ubuntu_6.0.sh && bash inst
```
2、安装好宝塔面板后在浏览器输入提供的地址进入面板，选择LNMP安装以下web环境，大于等于以下环境版本即可：
* Nginx 1.17
* MySQL 5.6
* PHP 7.3

3、安装web环境后打开PHP版本的设置，安装redis扩展
![](https://i.niupic.com/images/2020/10/15/8SpZ.webp "安装redis扩展")

4、解除被禁用的函数：putenv ， proc_open ， pcntl_alarm ， pcntl_signal
![](https://i.niupic.com/images/2020/10/15/8Sq2.webp "解除被禁用的函数")


5、点击宝塔左侧的网站，选择添加站点，输入网站域名或IP地址，域名需解析指向到该服务器IP，站点根目录的文件名不要有点号之类的特殊符号，否则后面可能无法执行队列服务；
![](https://i.niupic.com/images/2020/10/15/8Sq5.webp "添加站点")


## 安装V2board
1、通过SSH工具连接上服务器，cd到网站根目录，执行下列命令，注意替换文件名：
```linux
cd /www/wwwroot/站点文件名
chattr -i .user.ini
rm -rf .htaccess 404.html index.html
```
2、执行命令从 Github 克隆到当前目录（站点根目录），并把v2board文件夹的文件全部剪切到站点根目录文件夹（自行在宝塔站点文件目录查看剪切粘贴）：
```linux
git clone https://github.com/v2board/v2board.git
```
3、执行命令下载 composer.phar 到当前目录（站点根目录）：
```linux
wget https://getcomposer.org/download/1.9.0/composer.phar
```
4、执行命令进行包安装：
```linux
php composer.phar install
```
5、安装v2board，根据提示输入数据库地址（127.0.0.1）和用户名（v2_maoblog_tk）及设置默认的面板管理员账户密码：
```linux
php artisan v2board:install
```
6、避免后面打开网站出现500错误提示，给目录重新赋予权限，站点根目录执行下列命令，如执行后还显示500错误，可进一步尝试重启web环境和检查redis是否运行：
```linux
chown -R www:www *
```
7、后期v2board面板升级更新，命令行在站点根目录执行下列命令：
```linux
sh update.sh
```

## 配置网站目录和伪静态规则
1、回到宝塔面板，左侧网站-设置-网站目录，取消防跨站攻击，目录设置为/public 并保存。
![](https://i.niupic.com/images/2020/10/15/8Sqm.webp "配置网站目录")

2、继续选择旁边的伪静态，输入以下规则并保存，如后面打开网站后台显示404报错可以把伪静态的规则改成thinkphp的规则：
```linux
location /downloads {
}
location / {
    try_files $uri $uri/ /index.php$is_args$query_string;
}
location ~ .*\.(js|css)?$
{
    expires 1h;
    error_log off;
    access_log /dev/null;
}
```

## 配置定时任务和添加守护队列

1、在宝塔面板左侧选择计划任务，任务类型shell、任务名称v2board，周期每一分钟1次，脚本内容输入：
```linux
php /www/wwwroot/站点文件名/artisan schedule:run
```
2、v2board的邮件和支付都依赖队列服务，在宝塔面板左侧软件商店搜索PM2，找到PM2 Manager进行安装，然后添加项目，项目根目录选择站点根目录，启动文件名：pm2.yaml，项目名称：v2board，然后确定添加：
![](https://i.niupic.com/images/2020/10/15/8Squ.webp "添加守护队列")


这时候就搭建好一个完整的v2board前端网站，在浏览器输入域名即可访问v2board面板前端网站了，域名后面加/admin则进入管理员面板，同时也可以在宝塔面板的网站设置开启SSL证书访问。

