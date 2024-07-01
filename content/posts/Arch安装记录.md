---
title: Arch 安装记录
tags:
  - "Linux"
slug: n2f9s6o0
date: 2024-03-20T18:30:31+08:00
---

最近微信出新版的原生 Linux 客户端了，加上为了 aur 和 wiki，装上了 ArchLinux。

<!--more-->

![](https://images.yuanj.top/202403201841486.png)

启动盘用的是 Ventoy，由于安装介质中的控制台不能用校园网（或者说是我不会），使用 USB 共享网络。下面便是安装与配置的步骤。

## 禁用 reflector 服务

2020 年，archlinux 安装镜像中加入了 reflector 服务，它会自己更新 mirrorlist。在特定情况下，它会误删某些有用的源信息。这里进入安装环境后的第一件事就是将其禁用。也许它是一个好用的工具，但是很明显，因为地理上造成的特殊网络环境，这项服务并不适合加入到守护进程中。使用下列命令禁用：

```bash
systemctl stop reflector.service
```

## 确认启动模式

```bash
ls /sys/firmware/efi/efivars
```

![](https://images.yuanj.top/202403201843195.png)

输出了一堆东西（efi 变量），则说明已在 UEFI 模式。

## 网络连接

使用 iwctl 连接无线网：

```bash
iwctl # 进入交互式命令行
device list # 列出无线网卡设备名，比如无线网卡看到叫 wlan0
station wlan0 scan # 扫描网络
station wlan0 get-networks # 列出所有 wifi 网络
station wlan0 connect wifi-name # 进行连接，注意这里无法输入中文。回车后输入密码即可
exit # 连接成功后退出
```

若是有线网络，只要插上一个已经联网的路由器分出的网线（DHCP），按理说直接就能使用有线网络。

可以等待几秒等网络建立连接后再进行下一步测试网络的操作。

测试网络是否连接：

```bash
ping baidu.com
```

## 更新系统时钟

```bash
timedatectl set-ntp true # 将系统时间与网络时间进行同步
```

## 换源

使用 reflector 来选择速度比较好的源：

```bash
reflector -p https -c China --delay 3 --completion-percent 95 --sort score --save /etc/pacman.d/mirrorlist
```

## 分区和格式化

以前用的是 ext4，为了方便保存系统快照，我用了 btrfs 文件系统，使用 cfdisk 新建两个分区`efi`和`/`，`efi`分区 500M 即可，剩下的都给`/`。

格式化 efi 分区：

```bash
mkfs.fat -F32 /dev/nvme0n1p1
```

格式化 btrfs 分区：

```bash
mkfs.btrfs -L Arch /dev/nvme0n1p2
```

这里的 Arch 只是指定的一个标签，没有什么特殊的作用。

把 btrfs 分区挂在到/mnt 下准备创建子卷：

```bash
mount -t btrfs -o compress=zstd /dev/nvme0n1p2 /mnt
```

分别创建`/`和`home`的子卷：

```bash
btrfs subvolume create /mnt/@ # 创建 / 目录子卷
btrfs subvolume create /mnt/@home # 创建 /home 目录子卷
```

检查子卷情况：

```bash
btrfs subvolume list -p /mnt
```

创建好之后卸载分区，准备挂载子卷：

```bash
umount /mnt
```

按照以下顺序挂载分区：

```bash
mount -t btrfs -o subvol=/@,compress=zstd /dev/nvme0n1p2 /mnt # 挂载 / 目录
mkdir -p /mnt/home # 创建 /home 目录
mount -t btrfs -o subvol=/@home,compress=zstd /dev/nvme0n1p2 /mnt/home # 挂载 /home 目录
mkdir -p /mnt/boot # 创建 /boot 目录
mount /dev/nvme0n1p1 /mnt/boot # 挂载 /boot 目录
```

## 安装系统

安装内核、系统基本包和一些需要的软件：

```bash
pacstrap /mnt base base-devel linux linux-firmware btrfs-progs networkmanager vim sudo zsh zsh-completions
# 如果使用 btrfs 文件系统，额外安装一个 btrfs-progs 包
```

## 生成 fstab 文件

fstab 用来定义磁盘分区。它是 Linux 系统中重要的文件之一。使用 genfstab 自动根据当前挂载情况生成并写入 fstab 文件：

```bash
genfstab -U /mnt > /mnt/etc/fstab
```

检查 fstab 文件内容：

```bash
cat /mnt/etc/fstab
```

Nvme 协议的硬盘应该输出以下这样的内容：

```bash
# /dev/nvme0n1p6  /  btrfs  rw,relatime,compress=zstd:3,ssd,space_cache,subvolid=256,subvol=/@,subvol=@ 0 0
UUID=d01a3ca5-0798-462e-9a30-97065e7e36e1 /  btrfs  rw,relatime,compress=zstd:3,ssd,space_cache,subvolid=256,subvol=/@,subvol=@  0 0

# /dev/nvme0n1p1  /boot vfat  rw,relatime,fmask=0022,dmask=0022,codepage=437,iocharset=ascii,shortname=mixed,utf8,errors=remount-ro      0 2
UUID=522C-80C6  /boot vfat  rw,relatime,fmask=0022,dmask=0022,codepage=437,iocharset=ascii,shortname=mixed,utf8,errors=remount-ro 0 2

# /dev/nvme0n1p6  /home btrfs rw,relatime,compress=zstd:3,ssd,space_cache,subvolid=257,subvol=/@home,subvol=@home 0 0
UUID=d01a3ca5-0798-462e-9a30-97065e7e36e1 /home btrfs rw,relatime,compress=zstd:3,ssd,space_cache,subvolid=257,subvol=/@home,subvol=@home 0 0

# /dev/nvme0n1p5  none  swap  defaults  0 0
UUID=8e40dbed-590f-4cb8-80de-5cef8343a9fc none  swap  defaults  0 0
```

## change root

```bash
arch-chroot /mnt
```

## 安装微码

```bash
pacman -S intel-ucode # Intel
pacman -S amd-ucode # AMD
```

## 安装 grub 引导

安装需要的包：

```bash
pacman -S grub efibootmgr 
```

双系统引导的话还需要安装 os-prober 包来识别 Windows。

安装 GRUB 到 efi 分区：

```bash
grub-install --target=x86_64-efi --efi-directory=/boot --bootloader-id=ARCH
```

编辑 grub 文件：

```bash
vim /etc/default/grub
```

去掉 GRUB_CMDLINE_LINUX_DEFAULT 一行中最后的 quiet 参数，把 loglevel 的数值从 3 改成 5，这样是为了后续如果出现系统错误，方便排错。再加入 nowatchdog 参数，这可以显著提高开关机速度。

![](https://images.yuanj.top/202403201858693.png)

如果要引导 Windows，还需要添加新的一行 GRUB_DISABLE_OS_PROBER=false。

```bash
# GRUB boot loader configuration

GRUB_DEFAULT=0
GRUB_TIMEOUT=5
GRUB_DISTRIBUTOR="Arch"
GRUB_CMDLINE_LINUX_DEFAULT="loglevel=5 nowatchdog"
GRUB_CMDLINE_LINUX=""
GRUB_DISABLE_OS_PROBER=false
...
```

最后生成 grub 配置：

```bash
grub-mkconfig -o /boot/grub/grub.cfg
```

## 设置主机名

```bash
vim /etc/hostname
```

写入自己想要的名字。

## 设置 hosts

```bash
vim /etc/hosts
```

写入：

```bash
127.0.0.1   localhost
::1         localhost
127.0.1.1   myarch.localdomain myarch
```

## 设置时区

```bash
ln -sf /usr/share/zoneinfo/Asia/Shanghai /etc/localtime
```

同步时间到硬件：

```bash
hwclock --systohc
```

## 语言配置

编辑 /etc/locale.gen，去掉 en_US.UTF-8 UTF-8 以及 zh_CN.UTF-8 UTF-8 行前的注释。

```bash
vim /etc/locale.gen
```

然后生成 locale-gen：

```bash
locale-gen
```

在/etc/locale.conf 输入内容：

```bash
echo 'LANG=en_US.UTF-8'  > /etc/locale.conf
```

## 设置 root 密码

```bash
passwd root
```

## 完成安装

```bash
exit # 退回安装环境
umount -R /mnt # 卸载新分区
reboot # 重启进入系统
```

重启后登录 root 账户。

## 新建用户

```bash
useradd -m -G wheel -s /bin/bash myusername
passwd myusername
```

然后给予新用户 root 权限：

```bash
EDITOR=vim visudo
```

去掉`#%wheel ALL=(ALL:ALL) ALL`的注释

![](https://images.yuanj.top/202403201907539.png)

切换到新用户：

```bash
su - myusername
```

启动网络管理器：

```bash
sudo systemctl enable --now NetworkManager
```

## 桌面环境的安装

我比较喜欢用 cinnamon 桌面+lightdm 管理器，英伟达显卡下 wayland 配置麻烦，索性用 X11：

```bash
sudo pacman -S cinnamon gnome-terminal xorg lightdm
```

添加 lightdm 守护进程并进入桌面环境：

```bash
sudo systemctl enable lightdm
sudo systemctl start lightdm
```

## 软件源配置

编辑 `/etc/pacman.conf` 文件

```bash
vim /etc/pacman.conf
```

去掉`multilib`一节中两行的注释，来开启 32 位库支持。

添加 archlinuxcn 仓库：

```bash
[archlinuxcn]
Server = https://mirrors.ustc.edu.cn/archlinuxcn/$arch # 中国科学技术大学开源镜像站
Server = https://mirrors.tuna.tsinghua.edu.cn/archlinuxcn/$arch # 清华大学开源软件镜像站
Server = https://mirrors.hit.edu.cn/archlinuxcn/$arch # 哈尔滨工业大学开源镜像站
Server = https://repo.huaweicloud.com/archlinuxcn/$arch # 华为开源镜像站
```

然后刷新数据库并安装密钥：

```bash
sudo pacman -Syyu
sudo pacman -S archlinuxcn-keyring
```

如果出现`error: archlinuxcn-keyring: Signature from "Jiachen YANG (Arch Linux Packager Signing Key) " is marginal trust`，需要在本地信任 farseerfc 的 GPG key：

```bash
sudo pacman-key --lsign-key "farseerfc@archlinux.org"
```

然后安装 archlinuxcn-keyring。

## 字体配置

默认是不带中文字体的，需要安装几个中文字体：

```bash
sudo pacman -S noto-fonts-cjk adobe-source-han-serif-cn-fonts wqy-zenhei noto-fonts noto-fonts-cjk noto-fonts-emoji noto-fonts-extra
```

~~还可以通过 aur 安装 Windows 字体：~~ 更推荐直接 copyWindows 的字体，把 ttf 和 ttc 的所有字体复制到 Linux 字体目录就够了。

```bash
yay -S ttf-ms-win11-auto
```

安装中文字体后，很多软件里是有异性的，需要手动调整字体优先级，在 `/etc/fonts/conf.d/`或 `/etc/fonts/conf.avail/` 下创建文件`64-language-selector-prefer.conf`：

```bash
sudo vim /etc/fonts/conf.d/64-language-selector-prefer.conf
```

写入以下内容：

```xml
<?xml version="1.0"?>
<!DOCTYPE fontconfig SYSTEM "fonts.dtd">
<fontconfig>
  <alias>
    <family>sans-serif</family>
    <prefer>
      <family>Noto Sans CJK SC</family>
      <family>Noto Sans CJK TC</family>
      <family>Noto Sans CJK JP</family>
    </prefer>
  </alias>
  <alias>
    <family>monospace</family>
    <prefer>
      <family>Noto Sans Mono CJK SC</family>
      <family>Noto Sans Mono CJK TC</family>
      <family>Noto Sans Mono CJK JP</family>
    </prefer>
  </alias>
</fontconfig>
```

---

20240629 更新：系统改为默认使用鸿蒙字体，所以字体优先级修改为：

```xml
<?xml version="1.0"?>
<!DOCTYPE fontconfig SYSTEM "fonts.dtd">
<fontconfig>
  <alias>
    <family>sans-serif</family>
    <prefer>
      <family>HarmonyOS Sans SC</family>
      <family>HarmonyOS Sans TC</family>
      <family>HarmonyOS Sans JP</family>
    </prefer>
  </alias>
  <alias>
    <family>monospace</family>
    <prefer>
      <family>Noto Sans Mono CJK SC</family>
      <family>Noto Sans Mono CJK TC</family>
      <family>Noto Sans Mono CJK JP</family>
    </prefer>
  </alias>
</fontconfig>
```

鸿蒙字体的包是`ttf-harmonyos-sans` `aur`。

## 中文输入法

```bash
sudo pacman -S fcitx5-im # 输入法基础包组
sudo pacman -S fcitx5-chinese-addons # 官方中文输入引擎
sudo pacman -S fcitx5-rime # rime 输入法
sudo pacman -S fcitx5-material-color # 输入法主题
```

使用 rime 的 [雾凇拼音](https://aur.archlinux.org/packages/rime-ice-pinyin-git)。

设置环境变量：

```bash
sudo vim /etc/environment
```

写入：

```bash
GTK_IM_MODULE=fcitx
QT_IM_MODULE=fcitx
XMODIFIERS=@im=fcitx
SDL_IM_MODULE=fcitx
GLFW_IM_MODULE=ibus
```

## 界面美化

GTK 主题使用`vimix-gtk-themes` 和 `matcha-gtk-theme`，图标使用`papirus-icon-theme`，都使用 yay 来安装。

lightdm 的 greeter 用 linuxmint 的`lightdm-slick-greeter`，`lightdm-settings`进行配置。

这里 lightdm 需要改一下默认的 greeter，参见 [wiki](https://wiki.archlinuxcn.org/wiki/LightDM#Greeter)

20240629 更新：鼠标光标用 Windows10 的，aur 包为`windows-10-cursor`，总感觉 Linux 下其他的光标怪怪的 ...

## 基础功能软件

- numlockx 使 lightdm 开机默认打开数字键盘 [Lightdm Wiki](https://wiki.archlinuxcn.org/wiki/LightDM#%E9%BB%98%E8%AE%A4%E6%89%93%E5%BC%80%E5%B0%8F%E9%94%AE%E7%9B%98)
- fastfetch 系统信息查看
- zsh 更好用的 shell
- yay aur 助手
- ntfs-3g 识别 NTFS 硬盘
- nomacs 图片查看器
- xed 文本编辑器
- xarchiver 图形化解压缩软件
- p7zip 命令行解压缩软件
- flameshot 截图工具，时间戳为`%Y%m%d%H%M%S`
- v2raya 透明代理（需要添加守护进程）`aur`
- mpv 播放器
- bluez bluez-utils blueman 蓝牙协议支持与管理
- nvidia nvidia-settings lib32-nvidia-utils 英伟达显卡驱动

## 日常软件

- ohmyzsh 增强 zsh [清华镜像](https://mirrors.tuna.tsinghua.edu.cn/help/ohmyzsh.git/)
- google-chrome Chrome 浏览器`aur`
- wemeet-bin 腾讯会议`aur`
- linuxqq QQ`aur`
- wps-office-cn wps-office-mui-zh-cn ttf-wps-fonts WPS 国内版及字体和符号支持`aur`
- zotero-bin 文献管理软件`aur`
- lx-music-desktop-bin 洛雪音乐助手`aur`
- visual-studio-code-bin 代码编辑器`aur`
- rstudio-desktop-bin Rstudio`aur`
- piclist-bin 图床工具`aur`
- wechat-uos-qt 微信`aur`
- mambaforge 更快的 conda 包管理器`aur`
- timeshift 系统快照

## 问题解决

**内核启动时无法识别部分外置 USB 蓝牙**：

```bash
rmmod btusb
modprobe btusb
```

**导出系统安装的包，方便一键安装**

```bash
pacman -Qqen > packages-repository.txt   # 导出
pacman --needed -S - < packages-repository.txt  # 安装 
```

```bash
pacman -Qqem > packages-AUR.txt   # 导出
cat packages-AUR.txt | xargs yaourt -S --needed --noconfirm  # 安装 
```