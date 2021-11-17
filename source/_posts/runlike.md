---
title: 使用runlike复制容器运行命令
date: 2021-11-17 08:20:01
tags: [docker, 基础设施]
categories: 基础设施
---

我们在使用docker的过程中经常会遇到需要创建一个与已经存在的容器相同或相似的新容器实例。通过一些图形界面管理工具（例如Portainer），可以很方便的复制或更新一个已有容器。但是当我们没有安装相应的管理工具，或没有办法通过网页进行管理的时候，则需要一个命令行的工具，来提取已经存在容器的创建参数了。docker inspect相对来说过于详细，而且需要人为将相关的参数抓出来，比较麻烦。有热心开发人员开源了脚本工具runlike，可以帮助我们提取docker run命令。

## runlike使用

``` bash
runlike <container-name>
```

增加参数 `-p` 可以输出经过美化的命令，例如

``` bash
$ runlike -p redis

docker run \
    --name=redis \
    -e "PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin" \
    -e "REDIS_VERSION=2.8.9" \
    -e "REDIS_DOWNLOAD_URL=http://download.redis.io/releases/redis-2.8.9.tar.gz" \
    -e "REDIS_DOWNLOAD_SHA1=003ccdc175816e0a751919cf508f1318e54aac1e" \
    -p 0.0.0.0:6379:6379/tcp \
    --detach=true \
    myrepo/redis:7860c450dbee9878d5215595b390b9be8fa94c89 \
    redis-server --slaveof 172.31.17.84 6379
```

## runlike安装
本节介绍如何不安装运行runlike( :-P )。
runlike 已经被制成了docker镜像: assaflavie/runlike.
直接运行：
``` bash
docker run --rm -v /var/run/docker.sock:/var/run/docker.sock \
    assaflavie/runlike YOUR-CONTAINER
```
也可以将下列同义词放入 ~/.profile 或 ~/.bashrc
``` bash
alias runlike="docker run --rm -v /var/run/docker.sock:/var/run/docker.sock assaflavie/runlike"
```

然后就可以直接使用本地命令行:
``` bash
runlike YOUR-CONTAINER
```