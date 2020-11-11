---
title: 搭建aria2+ariaNg+onindex并自动上传到onedrive网盘
date: 2020-11-08 22:16:04
tags:
   aria2
categories:
  aria2
---

VPS安装宝塔面板、oneindex、Aria2 & AriaNg，下载完成自动上传到onedrive
## 提前准备
VPS,域名，并解析3个域名到VPS的IP、onedrice账号

vps.xx.xx作为宝塔面板地址 <br>
pan.xx.xx作为oneindex网站地址 <br>
xiazai.xx.xx作为下载网站地址

## 1.安装宝塔

Centos系统
```linux
yum install -y wget && wget -O install.sh http://download.bt.cn/install/install.sh && sh install.sh
```
Ubuntu系统
```linux
wget -O install.sh http://download.bt.cn/install/install-ubuntu.sh && sudo bash install.sh
```
Debian系统
```linux
wget -O install.sh http://download.bt.cn/install/install-ubuntu.sh && bash install.sh
```
记住Bt-Panel地址,username和password。

在浏览器登录宝塔面板，安装Nginx1.8、PHP7.0。


## 2.安装Oneindex

添加域名: 宝塔面板左侧> 点击“网站”> “添加站点”

输入pan.xx.xx，点击提交

### 安装Oneindex 

使用ssh工具 进入网站目录

cd /www/wwwroot/pan.xx.xx 

然后根据不同的系统，安装git

Debian/Ubuntu系统
```linux
apt-get install git -y
```
CentOS系统
```linux
yum install git -y
```
安装oneindex
```linux
git clone https://github.com/avedu/oneindex.git
```

移动文件
```linux
mv oneindex/* ./
```
赋予执行权限
```linux
chmod 777 ./config && chmod 777 ./cache
```

宝塔>“网站”>点击我们的域名右侧的“设置”>“伪静态”，粘贴代码，然后保存。
```
location / {
if (!-f $request_filename){
set $rule_0 1$rule_0;
}
if (!-d $request_filename){
set $rule_0 2$rule_0;
}
if ($rule_0 = "21"){
rewrite ^/(.*)$ /index.php?/$1 last;
}
}
```
“SSL>”  申请Let's Encrypt证书。
### 挂载OneDrive 

打开Oneindex的域名（pan.xx.xx），进行安装和绑定OneDrive密匙,

点击"下一步"，在下一个页面，点击“获取应用ID和机密”

登陆我们想要绑定的onedrive账号

登陆完成以后，会出现另外一个页面，复制应用机密并填入oneindex网页中的应用机密栏里

回到应用机密那个网页，点击“知道了，返回到快速启动” 找到App ID并填入oneindex网页中的应用ID栏里

点击下一步，之后的页面里点击“绑定账号”

选择我们要绑定的账号，点击接受

提示安装成功页面
![](https://i.niupic.com/images/2020/11/11/8Yvx.png "成功页面")

我们点击“管理后台”，输入初始密码“oneindex”，就可以进入我们的后台了

## 安装Aria2

返回ssh工具，回到主目录
```
cd /
```
下载并安装aria2
```
wget -N --no-check-certificate https://raw.githubusercontent.com/ToyoDAdoubi/doubi/master/aria2.sh && chmod +x aria2.sh && bash aria2.sh
```
备用地址
```
wget -N --no-check-certificate https://www.moerats.com/usr/shell/Aria2/aria2.sh && chmod +x aria2.sh && bash aria2.sh

```
输入1，等待安装，后面还有一个关于版本选择的，直接回车就好

安装完成后，记住这些信息。

修改密码可以使用命令来修改
```
bash aria2.sh
```
## 安装AriaNg
宝塔添加域名（xiazai.xx.xx）

上传程序

Debian/Ubuntu系统
```linux
apt-get install unzip -y
```
CentOS系统
```linux
yum install unzip -y
```
使用ssh工具

cd /www/wwwroot/xiazai.xx.xx

下载命令
```linux
wget https://www.moerats.com/usr/down/aria-ng-0.2.0.zip && unzip aria-ng-0.2.0.zip
```

打开你为AriaNg添加的域名（xiazai.xx.xx）

点击左边AriaNg设置，然后点击RPC，在下面RPC密匙中输入我们在安装Aria2时记下来的信息，然后右上角会蹦出来个“重新加载页面”，点击它

就可以看到我们的aria2状态已经连接上了

点击左边的”Aria2设置”，再点击”BitTorrent设置”，再拉到最后，把最小做种时间改成0，然后刷新一下网页。

## 配置上传

回到我们的宝塔面板，点击左侧“文件”，进入“root/.aria2”文件夹下，然后新建一个空白文件，文件名叫”oneindexup.sh”

然后将下面的代码复制进去，记住修改成自己的oneindex的域名，不然不会自动上传到onedrive，之后右下角保存

#!/bin/bash
path=$3
downloadpath='/usr/local/caddy/www/aria2/Download'
domain='pan.xx.xx'  #修改成自己域名
```
if [ $2 -eq 0 ]
		then
				exit 0
fi
while true; do
filepath=$path
path=${path%/*};
if [ "$path" = "$downloadpath" ] && [ $2 -eq 1 ]
	then
	php /www/wwwroot/$domain/one.php upload:file $filepath /$folder/
	rm -rf $filepath
	php /www/wwwroot/$domain/one.php cache:refresh
	exit 0
elif [ "$path" = "$downloadpath" ]
	then
	php /www/wwwroot/$domain/one.php upload:folder $filepath /$folder/
	rm -rf "$filepath/"
	php /www/wwwroot/$domain/one.php cache:refresh
	exit 0
fi
done
```

（下载完发现没有自动上传，看aria.log（/root/.aria2下）日志文件发现出了问题 Could not execute user command: /root/.aria2/oneindexup.sh: No such file or directory）

我们有时候在电脑上编辑一些脚本或者文件时，放到linux vps服务器上会出现问题，其实需要转换下格式就可以用了。

安装dos2unix
```
#Centos
yum install dos2unix

#Debian/Ubuntu
apt-get install dos2unix
```
若出现dos2unix安装失败，提示
```
Reading package lists... Done
Building dependency tree       
Reading state information... Done
You might want to run 'apt --fix-broken install' to correct these.
The following packages have unmet dependencies:
 linux-headers-4.11.8-041108-generic : Depends: libssl1.0.0 (>= 1.0.0) but it is not installable
E: Unmet dependencies. Try 'apt --fix-broken install' with no packages (or specify a solution)
```
则运行 apt --fix-broken install 后，重新 apt-get install dos2unix 一次就可以了

转换文件
```
dos2unix /root/xx   #注意文件位置填对
```
这样就能解决 Could not execute user command: /root/.aria2/oneindexup.sh: No such file or directory 的问题了。

然后在ssh工具里，分别输入下面的代码

```
chmod +x /root/.aria2/oneindexup.sh
```
```
echo "on-download-complete=/root/.aria2/oneindexup.sh" >>/root/.aria2/aria2.conf
```

再重启Aria2
```
service aria2 restart
```

## crontab定时刷新缓存

为了提高提高系统访问性能，我们需要定时刷新缓存，不然你存取的文件有的时候看不到，影响体验。

使用ssh工具
```
crontab -e
```
选择3

配置crontab文件,注意替换pan.xx.xx为oneinex域名
```
# 每十分钟后台刷新一遍缓存
0 * * * * php /www/wwwroot/pan.xx.xx/one.php token:refresh   # 每小时刷新一次token
```
```
# 每十分钟后台刷新一遍缓存
*/10 * * * * php /www/wwwroot/pan.xx.xx/one.php cache:refresh
```

ok,这样一个下载站就搭建完成了。之后要就解决bt下载没速度或速度慢的问题

## 下载提速

### 开放端口

```
# BT监听端口(TCP)
iptables -I INPUT -p tcp --dport 51413 -j ACCEPT

# DHT网络监听端口(UDP)
iptables -I INPUT -p udp --dport 6881:6999 -j ACCEPT

# 保存
iptables-save

# 安装iptables-persistent
apt-get install iptables-persistent


#保存规则持续生效
netfilter-persistent save
netfilter-persistent reload
```
### 添加 BitTorrent tracker

在 Aria2 配置文件 (aria2.conf) 所在目录（/root/.aria2）执行
```
bash <(curl -fsSL git.io/tracker.sh)
```
即可获取最新 tracker 列表并自动添加到配置文件中。

### 获取 DHT 网络节点数据
DHT 网络由无数节点组成，只要是开启 DHT 功能的 BT 客户端都是一个节点，所以你也可以是其中的一份子。当接触到一个节点，通过这个节点又能接触到更多的节点，接触的节点越多，你获取资源的能力就越强，下载的速度间接也就会有提升。

Aria2 有个 dht.dat 文件（开启 IPv6 还有个 dht6.dat），里面记录了 DHT 网络节点信息。但是！文件本身是不存在的，需要手动创建。如果你在没有对 Aria2 进行任何配置的情况下第一次运行时直接下载磁力链接或者冷门种子，会因为文件内没有任何数据，就无法获取到 DHT 网络中的节点，所以就会遇到无法下载的情况。

解决方案是生成 DHT 数据。找几个热门种子下载，比如 [Ubuntu 镜像](https://ubuntu.com/download/alternative-downloads")。下载后做种几个小时，你会发现 dht.dat 从空文件变成有数据了。

### 解决外部因素

#### 公网 IP
在没有公网 IP 的情况下其它的 BT 客户端很难发现你的存在，只有你去发现别人，所以这大大削弱了你的 BT 客户端的资源搜寻能力。

中国由于发展互联网的时间比较晚，所持有的公网 IPv4 地址并不是很多。早期因为接入互联网的家庭并不多，所以基本都会分配公网 IP 。然而随着互联网的快速发展，这些地址很快就不够用了，所以现在基本上运营都不会主动给家庭宽带分配公网 IP 。但这并不代表你不能获得公网 IP ，你所要做的是向运营商提出要求，若不满足可以尝试投诉。

另外目前 IPv6 网络已经建设得很好了，很多运营商虽然不提供 IPv4 公网，但 IPv6 都会是公网 IP ，所以如果你懂得如何去设置路由器，获取到 IPv6 公网地址，也算是一个曲线救国的方式。当然如果你能 IPv4 和 IPv6 公网双持，那么对于 BT 下载速度会有很大加成

#### UDP 受限
BT 下载部分通讯使用的是 UDP 协议。由于 BT 下载占用了很大的带宽，所以运营商会干扰 UDP 协议，从而导致 BT 下载出现问题。这在早期 BT 下载还未使用加密通讯时尤其严重。目前来说只要你开启加密选项，那么就可以在一定程度上避免这个情况

经过以上的一波操作，也许下载并没有多么的快，但至少正常了。可能有些非常老而且冷门的资源不会一直有人在做种，这种情况要么是一直挂着等待有缘人突然做种，BT 就是这样，能下载都是缘分。要么把种子放到百度或者 115 进行离线下载，你会看到那虚假的进度条比你正常用 BT 下热门资源还要快（毕竟资源已经在服务器上存在了，进度条只是给你一种下载速度快的幻觉）。


## 常用命令
```
# 按G单位列出目录及其子目录下所有目录和文件的大小
du -ah 目录

#  删除 /root/Download 目录下所有的文件
rm -rf /root/Download/*

# aria2服务重启
service aria2 restart

# 查看aria2服务状态
service aria2 status

```
