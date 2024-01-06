---
title: 修复 Debian 下 wine 应用没有图标的问题
tags:
  - "Linux"
  - "Debian"
slug: i8k9s3o3
date: 2024-01-06T16:28:15+08:00
---

换到 cinnamon 桌面后，[deepin-wine](https://github.com/zq1997/deepin-wine) 源安装的所有 wine 应用都没有程序菜单的图标了，写一个 desktop 文件即可解决。

<!--more-->

先 cd 到`/opt/apps `目录查看安装了哪些 wine 应用，比如我安装了微信和企业微信，那么这个目录下就有`com.qq.weixin.deepin `和`com.qq.weixin.work.deepin`两个目录，微信的启动脚本就是`/opt/apps/com.qq.weixin.deepin/files/run.sh`，其他的 wine 应用也是一样的，只是目录名不同而已，同理，微信的图标路径是`/opt/apps/com.qq.weixin.deepin/entries/icons/hicolor/48x48/apps/com.qq.weixin.deepin.svg`，也只是目录不同。

那么新建一个`WeChat.desktop`文件：

```desktop
[Desktop Entry]
Encoding=UTF-8
Name=微信
GenericName=微信
Comment=基于 deepin-wine 的 windows 版微信
Exec=/opt/apps/com.qq.weixin.deepin/files/run.sh
Icon=/opt/apps/com.qq.weixin.deepin/entries/icons/hicolor/48x48/apps/com.qq.weixin.deepin.svg
Terminal=false
Type=Application
Categories=Application;Programme;
```
Name、GenericName 和 Comment 自定义即可，其他的根据实际情况写，企业微信也同理，这里要确保图标的路径，有可能是 64x64, 我的机器上是 48x48。

文件编写好之后直接放到`/usr/share/applications/`目录下，就能在程序菜单看到了。