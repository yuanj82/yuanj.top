---
title: 当我换上了 Debian12 Linux
tags:
  - "Linux"
slug: a5q9j1e7
date: 2024-01-02T12:59:49+08:00
---

历经千辛万苦，最终还是换上了 Debian ...

<!--more-->

先来张桌面图

![](https://images.yuanj.top/202401021323392.png)

安装的软件有：

- Linux QQ，不得不说，腾讯这一个做的还是很不错的，和 Windows 下的新版 QQ 基本上一样了
- 腾讯会议， 官网有 deb 包
- wine 微信，有 UOS 适配的所谓原生微信，但是不管是功能还是体验，比 wine 差的太多，虽说 wine 也不咋地 ...
- wine 企业微信，很难评
- obsidian, 原生 deb 安装包安装，与 Windows 一样
- VScode, 微软官方源安装，与 Windows 一样
- fcitx5 输入法，调教一下还不错，搜狗输入法没有更新 Debian/Ubuntu 的新版，只能用这个，除了操作习惯不太一样之外其他面前够用，[fcitx5 词库](https://github.com/wuhgit/CustomPinyinDictionary)，[fcitx5 皮肤](https://github.com/sxqsfun/fcitx5-sogou-themes)
- WPS2019, 金山的良心之作，装好字体之后还是很好用的
- sublime Text, 官网安装包安装，与 Windows 一样
- Chrome, 谷歌官方源安装，与 Windows 一样
- 洛雪音乐助手， GitHub 下载 deb 安装包安装，换上六音音源还可以用
- Free Download Manager, 用于代替 IDM，毕竟浏览器自带的下载太鸡肋
- Zotero, 用于替代 Endnote, 有开发者做了 deb 包，还不错，[GitHub 地址](https://github.com/retorquere/zotero-deb)
- GIMP， 用于代替 PS
- OBS studio， 屏幕录制
- inscape, 代替 AI，矢量图制作
- Kdenlive， 用于代替 PR
- flameshot, 截图工具，时间戳为`%Y%m%d%H%M%S`
- envolution, 邮件客户端
- plank， 系统 dock 栏
- gromacs GPU 版， 用于分子动力学模拟
- mamba, 代替 conda, 速度快了不少，用于生物信息学软件安装
- git, 不必多说
- zsh, 比 bash 好用一些
- neofetch, 查看系统信息
- picgo-core, linux 下的图形版 picgo 不太好用
- hugo， 博客框架
- [clash](https://github.com/doreamon-design/clash), 懂的都懂，Debian GNOME 没有管理开机程序的 GUI 软件，所以需要用 systemctl 设置开机自启，在`/etc/systemd/system/`目录新建 clash.service 文件，写入以下内容：

```bash
[Unit]
Description=Clash Service
After=network.target

[Service]
ExecStart=/usr/local/bin/clash # clash 的可执行文件路径
Restart=on-failure
RestartSec=3
User=yuanj # 系统用户名

[Install]
WantedBy=default.target
```

然后设置开机启动：

```bash
sudo systemctl enable clash.service 
```

至于系统信息，直接看下图

![](https://images.yuanj.top/202401021340831.png)

不过有一说一，Debian 确实稳定，但是使用起来体验还是真的不如 POP_OS 这种经过优化的，后面考虑换到 POP_OS，Ubuntu 现在夹杂了太多私货，实在不敢恭维了 ...

Linux 用起来实际上跟 Windows 差别以已经不时很大了，主要是使用习惯的问题，比如 fcitx5 切换输入法是快捷键 Ctrl+空格，而 Windows 是 Ctrl+Shift，还有文件系统也不一样，Linux 里一切皆文件，不管什么，改改配置文件就好，Linux 差的最大的就是软件生态了，不过目前看 ... 似乎暂时够用了，如果需要 Windows, 我还有一台笔记本～

---

**2024/01/04 更新**

昨天换蓝牙驱动的时候不小心把 GNOME 桌面删掉了 ... 于是切换到 cinnamon 桌面环境，用上官方的 non-free 源，蓝牙驱动居然就好了！？我的台式电脑是外置 USB 插入的蓝牙设备，之前 ubuntu 上面就会经常卡顿，断断续续的，昨天用官方的源全面更新了一下之后居然很完美！！！没有丝毫问题，之前在 windows 的时候都会偶尔卡顿～

更爱 debian 了！cinnamon 桌面唯独有问题的就是 wine 应用图标不显示和登录页面比较丑，但是这俩似乎不是很大的问题？还是可以调教的。字体换上了 ubuntu 的字体，很好看，终端字体是 windows 的 Consolas，很不错。

![](https://images.yuanj.top/20240104105911.png)

下面的软件是 GNOME 环境下可以卸掉的没用的软件：

```bash
sudo apt purge transmission* goldendict pidgin kasumi hexchat* simple-scan gnome-games brasero* imagemagick* gnote gnome-font-viewer debian-reference* firefox* libreoffice* cheese* gnome-sound-recorder gnome-logs gnome-calculator gnome-characters gnome-online-accounts gnome-screenshot gnome-user-docs gnome-user-share xiterm+thai mozc* remmina shotwell galculator gimp-help*
```
`sudo apt purge`卸载掉之后`sudo apt autoremove`清理一下依赖。

还有就是这输入法 ... fcitx5 中文输入法能够输入的标点符号少了很多，还需要再调教一下。

---

**2024/01/06 更新**

登陆界面可以使用 lightdm 的 lightdm-settings 来修改，直接使用 apt 安装，然后把背景图片放在`/usr/share/images/`下，打开程序菜单里的`Login Window`来进行修改，主题也在这里修改。

**2024/02/03 更新**

笔记本上用 LMDE 更友好一些~

卸载无用软件：

```bash
sudo apt purge sticky hexchat* redshift-gtk firefox* libreoffice-* simple-scan hypnotix webapp-manager onboard* warpinator thingy mint-l-theme mint-themes gnome-icon-theme papirus-icon-theme mint-y-icons mint-l-icons mint-x-icons numix-icon-theme
```

解决蓝牙耳机无法自动连接：

```bash
git clone https://github.com/jrouleau/bluetooth-autoconnect.git
sudo cp bluetooth-autoconnect/bluetooth-autoconnect.service /etc/systemd/system/
sudo cp bluetooth-autoconnect/bluetooth-autoconnect /usr/bin/
sudo systemctl enable bluetooth-autoconnect.service 
sudo systemctl start bluetooth-autoconnect.service
sudo cp bluetooth-autoconnect/pulseaudio-bluetooth-autoconnect.service /etc/systemd/user/
systemctl --user enable pulseaudio-bluetooth-autoconnect
```

解决部分应用（WPS）无法显示鼠标光标主题样式：

```bash
sudo update-alternatives --config x-cursor-theme
```