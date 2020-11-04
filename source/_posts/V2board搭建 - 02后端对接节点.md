---
title: v2board搭建-02后端对接节点
date: 2020-10-15 11:40:26
tags: v2board
categories:
  v2board搭建
---
前面介绍了v2board的前端网站搭建教程，本篇接上文！关于v2board如何对接后端v2ray节点，通过docker快速对接v2ray后端节点，v2ray后端对接脚本程序：Aurora、v2ray-poseidon、sogo三种对接脚本。

其中Aurora由官方人员维护，属于v2board的亲儿子，需付费258 USDT才能使用（永久版）；v2ray-poseidon又叫波塞冬，社区版可以免费提供50个有效用户，商业版年付65 USDT；sogo社区版提供免费88个有效用户，商业版年付65 USDT。

三者最大差别是Aurora仅能在v2board面板使用，v2ray-poseidon和sogo都可以对接v2board、vnetpanel、sspanel三种面板.

## 前端网站添加节点

1、打开v2board管理员面板，找到权限组，创建一个组，然后再找到节点管理-添加节点；

2、名称标签随意，倍率是用户使用流量按多少倍算，权限组就是刚才添加的组，节点地址和端口填需要对接的后端服务器IP或域名、自定义端口，连接端口和服务端口一般情况请保持一致，传输协议ws，然后提交确认；

3、选择系统配置找到服务端，设置通讯密钥（自定义16位数以上），社区版无授权文件，如有授权文件则填入进去；


## 后端服务器对接节点

### （一）、v2ray-poseidon后端

1、通过SSH连接上你的Linux后端节点服务器（需要性价比高的服务器在本站都有推荐），推荐使用CentOS7；安装内核加速，推荐使用bbr plus。先安装内核，选择2，重启后，开启加速，选择7，如需其它BBR加速脚本看本站提供的教程。
```linux
wget -N --no-check-certificate "https://raw.githubusercontent.com/chiakge/Linux-NetSpeed/master/tcp.sh"
chmod +x tcp.sh
./tcp.sh
```
2、同步时间为北京时间：一般不需要，保险起见，建议还是同步一下。
```linux
yum -y install ntpdate
timedatectl set-timezone Asia/Shanghai
ntpdate ntp1.aliyun.com
```
3、关闭防火墙：必须要做，否则大部分对接上节点但是连接都会无网络连接。
```linux
systemctl start supervisord
systemctl disable firewalld
systemctl stop firewalld
```
4、安装并启动 Docker/docker-compose。
```linux
curl -fsSL https://get.docker.com | bash
curl -L "https://github.com/docker/compose/releases/download/1.25.3/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
chmod a+x /usr/local/bin/docker-compose
rm -f `which dc` 
ln -s /usr/local/bin/docker-compose /usr/bin/dc 

systemctl start docker
service docker start
systemctl enable docker.service
systemctl status docker.service
```
5、从GitHub获取后端源码安装v2ray-poseidon。
```linux
yum install -y git
git clone https://github.com/ColetteContreras/v2ray-poseidon.git
```
6、修改配置文件，我这里对接的ws协议，如果需要对接其它协议就进入到v2board目录下的其它协议文件夹。config.json文件需要修改三项。docker-compose.yml文件需要修改端口。
```linux
cd /root/v2ray-poseidon/docker/v2board/ws
 
vi config.json
##"nodeId":6 // 面板里添加完节点后生成的自增ID
##"webapi": "https://zhujiget.com",// v2board 的域名信息或单独设置个专门对接后端不需要用户访问的域名
##"token": "zhujiget.com666666", // v2board 和 v2ray-poseidon 的通信密钥
 
vi docker-compose.yml
##'80:80'修改为前端面板设置的端口数字
```

7、赋予Docker权限，并且启动Docker：
```linux
chmod +x  /bin/dc
cd /root/v2ray-poseidon/docker/v2board/ws
dc up -d
```

8、到此为止你已经全部设置好了。接下来我们可以查看一下日志看看有没有报错。
```linux
dc logs
```
9、回到前端v2board面板网站，可以看到刚才添加的节点状态显示蓝色，表示已对接成功，红色则对接失败检查故障，最后打开显隐开关连接节点测试。



