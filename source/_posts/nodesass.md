---
title: node-sass 离线安装方法
date: 2021-09-24 09:01:11
tags:
---

由于公司内部网络不能联网，前端开发人员需要使用node-sass，安装过程非常痛苦。其中走了如下几个弯路：
#### 1. 尝试安装npm install windows-build-tools。
这个没有走通，原因是可以联网的机器是一台Linux机器，扑街。
#### 2. 尝试手动安装BuildTools和Python。
安装成功了，然后按照node-sass和node-gyp页面上的说明进行了尝试，编译失败，报错大致如下：

```
error MSB4019: The imported project "E:\Microsoft.Cpp.Default.props" was not found. Confirm that the path in the <Import> declaration is correct, and that the file exists on disk.
error MSB4019: 未找到导入的项目"E:\Microsoft.Cpp.Default.props"。请确认<Import>声明中的路径正确，且磁盘上存在该文件。
```
再次扑街。

## 正确姿势
#### 3. 直接为安装脚本提供二进制文件
其实事情的缘起就是node-sass在安装时的一个脚本install.js在断网状态无法获取相应编译好的二进制。然后安装脚本就展开了通过源码进行编译的作死行为。
```
> node-sass@4.6.1 install /src/node_modules/node-sass
> node scripts/install.js

Downloading binary from https://github.com/sass/node-sass/releas…
Cannot download "https://github.com/sass/node-sass/releas…":

HTTP error 404 Not Found
```
经过查看相应的安装脚本scripts/install.js后，发现在lib/extensions.js中有这么一段注释：
```
/**
 * Determine the URL to fetch binary file from.
 * By default fetch from the node-sass distribution
 * site on GitHub.
 *
 * The default URL can be overridden using
 * the environment variable SASS_BINARY_SITE,
 * .npmrc variable sass_binary_site or
 * or a command line option --sass-binary-site:
 *
 *   node scripts/install.js --sass-binary-site http://example.com/
 *
 * The URL should to the mirror of the repository
 * laid out as follows:
 *
 * SASS_BINARY_SITE/
 *
 *  v3.0.0
 *  v3.0.0/freebsd-x64-14_binding.node
 *  ....
 *  v3.0.0
 *  v3.0.0/freebsd-ia32-11_binding.node
 *  v3.0.0/freebsd-x64-42_binding.node
 *  ... etc. for all supported versions and platforms
 *
 * @api public
 */

function getBinaryUrl() {
........
```
原来可以通过设置环境变量覆盖默认的github下载路径，刚好手边就有现成的http服务器，将相应的xxx_binding.node文件扔上去，然后在要安装node-sass机器上设置SASS_BINARY_SITE环境变量就可以了。

注意：如果http文件服务器是http://XXX.XXX.XX:8080/YYY，则设置SASS_BINARY_SITE=http://XXX.XXX.XX:8080/YYY。接下来需要在相应目录下创建**版本号文件夹**，然后将相应的XXX_binding.node放上去。完整的下载路径应该类似于http://XXX.XXX.XX:8080/YYY/v4.14.1/win32-x64-83_binding.node

之后正常安装npm install node-sass即可。总算搞定了。

除过设置SASS_BINARY_SITE环境变量，还可以指定二进制的存放位置，将文件直接放在那个路径中即可。我心累了，请大家自行查看https://github.com/sass/node-sass/blob/master/lib/extensions.js文件。