---
title: Debian 安装中文输入法
tags:
  - "Linux"
  - "Debian"
slug: z9g0s6i9
date: 2023-12-29T19:00:19+08:00
---

装上 Linux 之后，中文输入法自然是必不可少的，但是网上千奇百怪的教程实在让人眼花缭乱 ... 还是自个探索的好。

<!--more-->

首先先把自带的 fcitx 和 ibus 删了吧：

```bash
sudo apt purge fcitx*
sudo apt purge ibus*
```

哦对了，我使用的是英文的桌面环境，中文的高低会出点小小的问题，所以这里还需要配置下 locales，Debian 默认安装，如果没有，apt 安装就行。

```bash
sudo dpkg-reconfigure locales
```

使用键盘选择 zh_CN.UTF-8，但是后面选择默认语言的时候还是选 en_US.UTF-8，重启后编辑 locale：

```bash
sudo nano /etc/default/locale
```

内容写为：

```bash
LANG=en_US.UTF-8
LC_CTYPE=zh_CN.UTF-8
```

然后就可以开始安装中文输入法了！ 直接安装 fcitx5 的中文插件：

```bash
sudo apt install fcitx5-chinese-addons
```

再安装 rime 输入法：

```bash
sudo apt install fcitx5-rime
```

也是有 ibus 的，但是据说 ibus 停止维护了，而 fcitx5 现在对中文的支持还算不错，就用 fcitx5 了。

安装完之后 rime 还需要简单配置，这里我用了 GitHub 上的自动化配置脚本（GitHub 大法好啊！）：

这是一个 ruby 脚本，所以先安装 ruby：

```bash
sudo apt install ruby
```

然后直接克隆仓库运行脚本即可，运行完之后在在 rime 输入法那里点一下`deploy`即可。

```bash
git clone --depth=1 https://github.com/Mark24Code/rime-auto-deploy.git --branch latest
cd rime-auto-deploy

./installer.rb
```

实测 Debian12 上面 rime 的皮肤不生效，但是可以直接用 fcitx5 的皮肤 [fcitx5-themes](https://github.com/thep0y/fcitx5-themes)。此外，前面自动化脚本会启用模糊音，但是对于我来说明显是有点多此一举，删除配置目录下的`rime_ice.custom.yaml`重新部署即可。
