---
title: 使用Goproxy解决golang.org模块无法下载的问题
date: 2021-09-24 08:55:56
tags: [go, goproxy.cn, golang.org]
categories: 开发环境
---

## 简介
Goproxy 中国完全实现了 Go 的模块代理协议。并且它是一个由中国备受信赖的云服务提供商七牛云支持的非营利性项目。目标是为中国和世界上其他地方的 Gopher 们提供一个免费的、可靠的、持续在线的且经过 CDN 加速的模块代理。

愉快地编码吧，Gopher 们！;-)

## 用法
### macOS 或 Linux
打开你的终端并执行：

``` bash
$ export GOPROXY=https://goproxy.cn
```

或者

``` bash
$ echo "GOPROXY=https://goproxy.cn" >> ~/.profile && source ~/.profile
```

完成。

### Windows
打开你的 PowerShell 并执行：

``` bash
C:\> $env:GOPROXY = "https://goproxy.cn"
```

或者

1. 打开“开始”并搜索“env”

2. 选择“编辑系统环境变量”

3. 点击“环境变量…”按钮

4. 在“<你的用户名> 的用户变量”章节下（上半部分）

5. 点击“新建…”按钮

6. 选择“变量名”输入框并输入“GOPROXY”

7. 选择“变量值”输入框并输入“https://goproxy.cn”

8. 点击“确定”按钮

重启命令行或者PowerShell
