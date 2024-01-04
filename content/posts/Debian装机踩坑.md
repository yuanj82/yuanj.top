---
title: Debian 装机踩坑
tags:
  - "Linux"
  - "Nvidia"
  - "Debian"
slug: n2l6i3c4
date: 2023-12-29T18:08:05+08:00
---

前两天在台式机上装了 Debian12，说真的，坑不少啊，一个英伟达驱动折腾的我死去活来的 ...

<!--more-->

最初使用的是 KDE 环境的，根据官方的 Wiki，直接使用 non-free 源安装 Nvidia 显卡驱动：

```bash
sudo apt install linux-headers-amd64 nvidia-driver firmware-misc-nonfree nvidia-cuda-dev nvidia-cuda-toolkit libnvoptix1
```

重启后无法登入系统 ...

按 Ctrl+Alt+F2 进入 TTY 模式卸载安装的包，然后重启，在 [英伟达官网](https://www.nvidia.cn/Download/index.aspx?lang=cn) 下载对应显卡的安装包来安装驱动，先要禁用 nouveau 驱动：

```bash
sudo nano /etc/modprobe.d/blacklist-nouveau.conf
```

写入以下内容：

```bash
blacklist nouveau
options nouveau modeset=0
```

更新内核的 initramfs 文件：

```bash
sudo update-initramfs -u
```

重启系统，进入无图形界面安装：

```bash
sudo telinit 3
```

完成后重启，还是登不进去 ... 查了半天也不知道为啥，但是使用 GNOME 桌面安装是可以的，后来仔细一看，KDE 登陆界面左下角显示使用的是 Wayland 窗口系统，而 GNOME 默认是 X11，于是把 KDE 切换到 X11，安装后重启，就可以登入系统了。

X 与 Wayland 的主要差异在于它们的图形系统架构。在 X 系统中，存在一个 X 服务器（通常使用 Xorg）。X 服务器负责管理所有窗口的控件，接收鼠标、键盘等输入设备的信号，并与窗口管理器协调以获取窗口位置信息。X 服务器还负责计算需要更新的区域并指导显卡进行绘图。此外，X 系统通常需要一个专门的合成管理器来绘制窗口阴影（有时由窗口管理器兼职）。随着技术的发展，X 服务器的任务变得越来越繁重，需要不断打补丁，导致维护变得困难。

相比之下，Wayland 采用了更简洁的架构。在 Wayland 中，服务端的角色较小，主要由 Wayland 合成器（相当于 X 系统中的窗口管理器）担当。当接收到输入信号时，Wayland 合成器直接将信号发送给相应的窗口，然后由应用程序自己处理输入和绘图。绘图完成后，应用程序将图像传递给合成器，由合成器将各个窗口的图像叠加在一起以显示在屏幕上。这种架构的另一个优势是增强了应用程序之间的隔离性，因为每个应用程序只知道自己窗口的情况，从而提高了系统的安全性。

但是 Wayland 还是不能完全取代 X。

试了一下 KDE，感觉不是很舒服，于是切换到 GNOME，感觉 GNOME 美化一下还是不错的~

![](https://gcore.jsdelivr.net/gh/yuanj82/static/blog/202312291827075.png)

使用插件：

[AppIndicator and KStatusNotifierItem Support](https://extensions.gnome.org/extension/615/appindicator-support/) 显示任务栏托盘图标

[OpenWeather](https://extensions.gnome.org/extension/750/openweather/) 任务栏显示天气

[Clipboard Indicator](https://extensions.gnome.org/extension/779/clipboard-indicator/) 任务栏剪贴板历史记录

[Dash to Dock](https://extensions.gnome.org/extension/307/dash-to-dock/) 更好用的 dock 栏

主题忘了是什么了，后来图标换成了 [vimix-icon-theme](https://github.com/vinceliuice/vimix-icon-theme)，主题是 [vimix-gtk-themes](https://github.com/vinceliuice/vimix-gtk-themes)

---

**2023/01/04 更新**

启用 non-free 源之后直接使用 apt 安装显卡驱动即可，效果更好！