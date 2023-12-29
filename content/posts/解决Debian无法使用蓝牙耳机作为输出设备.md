---
title: 解决 Debian 无法使用蓝牙耳机作为输出设备的问题
tags:
  - "Linux"
  - "Debian"
slug: u1c6h9e1
date: 2023-12-29T18:46:49+08:00
---

Debian 上连接蓝牙耳机，能够正常连接但是不能作为输出设备使用，使用使用 PipeWire 驱动连接即可解决。

<!--more-->

首先还是先更新下系统，安装 PipeWire：

```bash
sudo apt update && sudo apt upgrade
sudo apt install pipewire -y
```

创建 `with-pulseaudio` 文件，让 PipeWire 接管 PulseAudio 的事件：

```bash
sudo mkdir -p /etc/pipewire/media-session.d/
sudo touch /etc/pipewire/media-session.d/with-pulseaudio
```

复制 Service 文件（如果没有就跳过）：

```bash
cp /usr/share/doc/pipewire/examples/systemd/user/pipewire-pulse.* /etc/systemd/user/
```

使 PipeWire 开机自启，不慎崩溃的时候也由 Systemctl 重新唤起：

```bash
//因为加入了新的 service 文件，需要重载 daemon
systemctl --user daemon-reload
//我们要让 PipeWire 接管原先的 PulseAudio，防止 2 个同时启动导致冲突，现在 PulseAudio 可以下岗了，关停和 PulseAudio 相关的所有服务
systemctl --user --now disable pulseaudio.service pulseaudio.socket
//启用并启动新的 pipewire-pulse 服务：
systemctl --user --now enable pipewire pipewire-pulse
```

把 PulseAudio 彻底禁用，防止因为某些程序自动唤起：

```bash
systemctl --user mask pulseaudio
```

重启电脑后输入命令：

```bash
LANG=C pactl info | grep '^Server Name'
```

若返回信息中包含`on PipeWire`则说明配置成功了。

再安装 PipeWire 所需的蓝牙支持模块：

```bash
# 安装 PipeWire 蓝牙支持模块
sudo apt install libspa-0.2-bluetooth
# 卸载 PulseAudio 蓝牙支持模块
sudo apt remove pulseaudio-module-bluetooth
```

最后安装 pavucontrol 来管理系统的声音输出设备：

```bash
sudo apt install pavucontrol
```

解决问题~