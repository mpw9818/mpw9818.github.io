---
title: v2board搭建-03邮箱验证
date: 2020-10-15 12:40:26
tags: v2board
categories:
  v2board搭建
---
V2board的邮件可用于注册验证、重置密码、订阅或群发邮件通知用户，设置不恰当会导致邮件无法发出，这篇文章主要介绍V2board如何正确设置邮件信息，对接阿里云的域名邮件服务。

1、如果自己有域名，可以到阿里云开通自己的域名邮件服务，也可以使用其它邮件服务，有的邮箱的发信容易进垃圾箱，自己选择即可，我使用的是阿里云企业邮箱。
    
登陆阿里云企业邮箱
    https://qiye.aliyun.com/alimail/auth/login?reurl=%2Falimail%2F%23h%3DWyJmbV8yIixbIjIiLCIiLHsiZklkIjoiNSJ9LHsibGFiZWwiOiLpgq7ku7YifV1d

2.组织与用户 -->员工账号管理
![](https://i.niupic.com/images/2020/10/15/8Srb.JPG "员工账号管理")

点击账号开启POP3/SMTP服务，然后保存
![](https://i.niupic.com/images/2020/10/15/8Srd.JPG "点击账号")

![](https://i.niupic.com/images/2020/10/15/8Sre.JPG "开启服务")


之后在网站根目录找到.env文件编辑修改以下内容；
```linux
MAIL_DRIVER=smtp     ##邮件发送方式
MAIL_HOST=smtp.qiye.aliyun.com  ##邮件发送服务器地址
MAIL_PORT=465          ##邮件发信端口
MAIL_USERNAME=postmaster@maoblog.tk   ##邮箱地址
MAIL_PASSWORD=Mm112233      ##邮箱密码
MAIL_ENCRYPTION=ssl        ##加密方式
MAIL_FROM_ADDRESS=postmaster@maoblog.tk   ##邮箱地址
MAIL_FROM_NAME=您的验证码         ##发信昵称，自定义修改
MAILGUN_DOMAIN=v2.maoblog.tk      ##域名地址
MAILGUN_SECRET=6ukYuSr2Zvrz+B9RC4VIsfT5t0C4PjV8IRMYSU7Bci0=     ## APP_KEY=base64:后面的内容
```

2、每次设置.env需要在网站根目录输入下列命令刷新缓存：
```linux
php artisan config:cache
```
并重启PM2队列服务。

3、之后注册下账户或重置下密码测试发信是否正常，发信模板可以在v2board管理中心的系统配置-邮件配置选择；

如上能收到邮件表示配置成功，如还无法收到邮件请检查配置和PM2队列服务启动情况（可尝试重启队列服务）。

