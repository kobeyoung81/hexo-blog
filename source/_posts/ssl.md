---
title: 使用DNS申请Letsencrpt通配符证书
date: 2021-11-29 16:48:10
tags:
---

Let's Encrypt 宣布 ACME v2 正式支持通配符证书，并将继续清除 Web 上采用 HTTPS 的障碍，让每个网站轻松获取管理证书。下面我们一起来学习下吧！

配置环境：
　　操作系统：Ubuntu 18.04 LTS
　　配置域名：kobeyoung81.cn

具体步骤
## 1、获取certbot
不同的操作系统对于certbot的安装方式不尽相同，我这里直接使用了官方源中的包。
## 2、开始申请证书　　
sudo certbot -d "*.kobeyoung81.cn" --manual --preferred-challenges dns-01 certonly
## 3、根据提示设置ACME TXT
在DNS服务商解析规则中增加TXT类型的解析，域名是_acme-challenge.kobeyoung81.cn，值按照certbot提示输入
## 4、生成证书
回车后成功生成证书

简单！
