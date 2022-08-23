---
title: 使用Certbot申请Let's Encrypt SSL证书并设置自动更新
date: 2022-08-23 09:44:56
tags: [基础设施, SSL, HTTPS]
categories: 基础设施
---

Certbot可以非常简便的从Let's Encrypt申请免费SSL证书，同时也提供了很多种不同机制。由于笔者网站采用了docker容器化的Nginx部署方式，所以没有办法直接使用Certbot内置插件进行证书的自动更新。后来经过试验，确定采用webroot方式的ACME认证，可以做到证书申请和自动更新。同时网站（Nginx）不需要停机维护。

### 1. Nginx配置域名ACME

```
load_module /usr/lib/nginx/modules/ngx_stream_module.so;
pid /home/path/nginx.pid;
worker_processes auto;
events {
    worker_connections  1024;
    accept_mutex on;
  }
error_log /var/log/nginx/error.log;
http {
  include mime.types;
  default_type application/octet-stream;
  server {
    server_name www.xxx.com;
    listen 443 ssl http2;
    ssl_certificate /etc/letsencrypt/live/xxx.com/fullchain.pem;
    ssl_certificate_key /etc/letsencrypt/live/xxx.com/privkey.pem;
    ssl_protocols TLSv1 TLSv1.1 TLSv1.2 TLSv1.3;
    listen 80;
    include /home/webpath/letsencrypt-acme-challenge.conf;
    if ($scheme = http) {
      return 301 https://$host:443$request_uri;
      }
    location / {
      proxy_pass http://localhost:3000/;
      proxy_set_header Host $host;
      proxy_set_header X-Real-IP $remote_addr;
      proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
      proxy_set_header X-Forwarded-Host $http_host;
      proxy_set_header X-Forwarded-Port $server_port;
      proxy_set_header X-Forwarded-Proto $scheme;
    }
  }
}
```

其中需要特别注意的是这三行：
```
    listen 80;
    include /home/path/letsencrypt-acme-challenge.conf;
    if ($scheme = http) {
      return 301 https://$host:443$request_uri;
    }
```

第一行设置监听80端口（HTTP默认端口，ACME验证需要使用）；第二行设置ACME路径；第三行设置其他正常HTTP请求跳转HTTPS。

letsencrypt-acme-challenge.conf内容如下：
```
location ^~ /.well-known/acme-challenge/ {
    default_type "text/plain";
    root         /home/nginxWebUI;
    break;
}

location = /.well-known/acme-challenge/ {
    return 404;
}
```

## 2. 使用Certbot申请证书
Certbot的安装方式不再赘述，网上有很多。

安装完成之后，使用命令：
```
sudo certbot certonly --webroot -w /home/webpath -d www.xxx.cn -d xxx.cn
```

使用了`--webroot`子命令后需要使用`-w`参数指定网站路径，笔者由于使用了docker版的nginx，并将用户/home某目录挂载到了容器中的网站路径下，所以跟一般nginx默认路径不同。请注意要将letsencrypt-acme-challenge.conf文件放在对应路径下。

每一个子域名可以使用`-d`参数来指定，当前子域名个数限制为每周最多申请50个。具体请见Let's Encrypt官网。

### 3. 使用申请好的证书

在第一段中的Nginx配置中有如下几行：
```
    listen 443 ssl http2;
    ssl_certificate /etc/letsencrypt/live/xxx.com/fullchain.pem;
    ssl_certificate_key /etc/letsencrypt/live/xxx.com/privkey.pem;
    ssl_protocols TLSv1 TLSv1.1 TLSv1.2 TLSv1.3;
```
中间证书的路径需要改成对应的证书路径。

### 4. 更新证书

直接运行命令
```
sudo certbot renew
```
会自动更新所有证书，可以将上述语句放入crontab中，目前证书有效期为90天。