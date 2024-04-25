---
title: "抛弃 WSL2 使用 scoop 搭建开发环境"
tags:
  - "WSL"
  - "Windows"
slug: u5b5f4n8
date: 2024-04-25T12:27:00+08:00
---

之前一直是用 WSL2 来作为开发环境的，博客、数据分析和编程等都在 WSL2 里进行，虽然 WSL2 已经及其方便，但是我仍然心里有疙瘩，因为两个原因：Hyper-V 的性能损失和无法自动释放内存/硬盘。

<!--more-->

实际上后面两个问题在 2.0.0 的 WSL 中（似乎）已经解决了，在去年的更新中，详情可见 [微软开发者 blog](https://devblogs.microsoft.com/commandline/windows-subsystem-for-linux-september-2023-update/)，我仔细看了一下，似乎大部分的实质性更新都只在 Windows11 中适用，但是根据描述，部分实验性更新在 Windows10 中是可以用的，毕竟 Windows11 还是太抽象（个人感觉），我最关注的两个功能即内存回收与虚拟硬盘空间释放，似乎在 Windows10 中可用，但我测试了一下，使用 Windows10 LTSC 2021 最新可更新的 WSL，两项功能在 .wslconfig 中可以正常启用，但没有效果。另一大问题就是性能损失问题了，据我个人测试，开启虚拟化之后 Windows 宿主机造成了大约 12%的性能损失。

下图是开虚拟化平台之前的跑分：

![](https://images.yuanj.top/202404251239401.png)

下图是开虚拟化平台之后的跑分：

![](https://images.yuanj.top/202404251239192.png)

为了保证跑分结果准确，我特意在两种情况下多跑了几次，分值都差不多。主要的损失来自于 CPU，而 CPU12%的性能损失还是挺严重的。

这实际上不是 WSL 的锅，而是因为 Hyper-V，开启虚拟化之后 Windows 宿主机也会变成一个 Hyper-V 的虚拟机，而这个变化通常情况下是无感的，但是 ... 我要打游戏 ...

于是只能去掉 WSL2，使用 scoop 来进行环境搭建。实际上 scoop 超乎了我的预期，它不仅可以装开发软件，甚至微信 QQ 这种日常生活软件也可以安装，而且第三方开发者提供的各种 buckets 包含了极多的软件！

我习惯将所有的软件都装在 C 盘，D 盘只保留个人数据，scoop 也去 C 盘吧。先改一下 powershell 的策略：

```powershell
Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope CurrentUser
```

然后安装 scoop，这一步还是上代理吧，gitee 的镜像如同 goushi：

```powershell
irm get.scoop.sh -outfile 'install.ps1'
.\install.ps1 -ScoopDir 'C:\Scoop' -ScoopGlobalDir 'C:\Scoop' -Proxy 127.0.0.1:7890
```

装好之后先配置个代理，因为很多安装脚本是从 GitHub 获得的，所以没代理简直用不了：

```powershell
scoop config proxy 127.0.0.1:7890
```

习惯了 bash 这类 shell，那么 powershell 也是很不习惯，况且 scoop 添加 buckets 是需要 git 的，所以先装个 git 和 GNU 的核心工具：

```powershell
scoop install git
scoop install coreutils
```

然后在装一个 Windows terminal 作为终端来用，并且把默认启动终端改成 git bash：

```powershell
scoop bucket add extras
scoop install windows-terminal
```

![](https://images.yuanj.top/202404251248248.png)

官方的 buckets 里面只有少数的软件，而我们要想安装其它国人日常使用的软件就需要添加几个 buckets：

```bash
scoop bucket add scoopet https://github.com/ivaquero/scoopet
scoop bucket add lemon https://github.com/hoilc/scoop-lemon
scoop bucket add dorado https://github.com/chawyehsu/dorado
```

添加了这三个 buckets 的话，基本上就能安装所有日常使用的软件了，除了一些生物信息学软件和游戏之外，其他的软件我基本都用 scoop 装了，干净也方便~

主要的开发工具还是用 VScode，这玩意是在是万金油，我主要用 R 和 Python，加上现在学 C 语言，都可以用 VScode，C 语言的话用 cmake 插件或者用 gcc 编译即可，Python 更方便，官方插件就可以识别解释器路径，而 R 语言需要配置一下。

在~目录新建一个。Rprofile 文件，写入以下内容：

```r
if (interactive() && Sys.getenv("TERM_PROGRAM") == "vscode") {
  if ("httpgd" %in% .packages(all.available = TRUE)) {        
    options(vsc.plot = FALSE)
    options(device = function(...) {
      httpgd::hgd(silent = TRUE)
      .vsc.browser(httpgd::hgd_url(history = FALSE), viewer = "Beside")
    })
  }
}
```

安装 languageserver 和 httpgd 包之后就可以很方便的出图了，与 VScode 集成在一起。

![](https://images.yuanj.top/202404251306446.png)

再给 CRAN 和 Bioconductor 换源：

```r
options("repos" = c(CRAN="https://mirrors.tuna.tsinghua.edu.cn/CRAN/"))
options(BioC_mirror="https://mirrors.tuna.tsinghua.edu.cn/bioconductor")
```

也就差不多了，哦对了，VScode 默认 shell 是 powershell，也给他换成 bash 吧。找到设置里的`terminal.integrated.profiles.windows`，编辑 settings.json，添加一个 bash 的路径：

```json
"Bash":{
    "path": "C:\\Scoop\\shims\\bash.exe"
}
```

再把 bash 设置为默认 shell：

```json
"terminal.integrated.defaultProfile.windows": "Bash",
```

这里备份一下我的`settings.json`吧：

```json
{
    "workbench.startupEditor": "none",
    "security.workspace.trust.enabled": false,
    "workbench.colorTheme": "GitHub Dark",
    "workbench.iconTheme": "material-icon-theme",
    "terminal.integrated.profiles.windows": {
        "PowerShell": {
            "source": "PowerShell",
            "icon": "terminal-powershell"
        },
        "Command Prompt": {
            "path": [
                "${env:windir}\\Sysnative\\cmd.exe",
                "${env:windir}\\System32\\cmd.exe"
            ],
            "args": [],
            "icon": "terminal-cmd"
        },
        "Git Bash": {
            "source": "Git Bash"
        },
        "Bash":{
            "path": "C:\\Scoop\\shims\\bash.exe"
        }
    },
    "terminal.integrated.defaultProfile.windows": "Bash",
    "r.rterm.windows": "C:\\Scoop\\shims\\r.exe",
    "r.rterm.option": [
        "--no-site-file"
    ],
    "r.bracketedPaste": true,
    "terminal.integrated.enableMultiLinePasteWarning": false,
    "r.lsp.diagnostics": false,
}
```

Windows Terminal 里用 git bash 的话，退格会有闪屏的情况，vim/vi 也是，这里需要添加一些内容：

```bash
echo "set bell-style none" >> ~/.inputrc
echo "set number" >> ~/.vimrc
echo "set noerrorbells" >> ~/.vimrc
echo "set t_vb=" >> ~/.vimrc
```

再贴一下我的 scoop 软件列表吧：

```txt
Installed apps:

Name             Version          Source Updated             Info
----             -------          ------ -------             ----
7zip             23.01            main   2024-04-24 07:59:49
coreutils        5.97.3           main   2024-04-24 10:04:16
dark             3.14             main   2024-04-24 10:58:10
dismplusplus     10.1.1002.1B     extras 2024-04-24 08:27:10
gcc              13.2.0           main   2024-04-24 08:29:06
gdb              14.1             main   2024-04-24 08:29:15
git              2.44.0           main   2024-04-24 08:00:34
honeyview        5.51             extras 2024-04-24 08:44:32
hugo             0.125.3          main   2024-04-24 08:29:19
innounp          0.50             main   2024-04-24 08:29:21
lxmusic          2.7.0            dorado 2024-04-24 08:36:47
mpv              0.38.0           extras 2024-04-24 08:37:45
neofetch         7.1.0            main   2024-04-24 08:34:48
notepadplusplus  8.6.5            extras 2024-04-24 08:27:30
obsidian         1.5.12           extras 2024-04-24 13:48:55
piclist          2.8.3            lemon  2024-04-24 08:37:01
python           3.12.3           main   2024-04-24 10:58:22
qqnt             9.9.9.240422     dorado 2024-04-24 08:24:28
r                4.3.3            main   2024-04-24 08:29:47
rstudio          2023.12.1-402    extras 2024-04-24 08:35:22
rtools           4.3.5958.5975    main   2024-04-24 08:34:21
snipaste-beta    2.8.8-Beta       dorado 2024-04-24 08:37:06
sumatrapdf       3.5.2            extras 2024-04-24 08:37:10
vim              9.1.0366         main   2024-04-24 08:35:29
vimtutor         0.2018.07.25     main   2024-04-24 08:37:11
vscode           1.88.1           extras 2024-04-24 08:27:58
wechat-np        nightly-20240424 dorado 2024-04-24 08:26:00
windows-terminal 1.19.10821.0     extras 2024-04-24 08:28:46
yt-dlp           2024.04.09       main   2024-04-24 08:38:37
zotero           6.0.36           extras 2024-04-24 09:27:45
7zip             23.01            main   2024-04-24 07:59:49 Global install
coreutils        5.97.3           main   2024-04-24 10:04:16 Global install
dark             3.14             main   2024-04-24 10:58:10 Global install
dismplusplus     10.1.1002.1B     extras 2024-04-24 08:27:10 Global install
gcc              13.2.0           main   2024-04-24 08:29:06 Global install
gdb              14.1             main   2024-04-24 08:29:15 Global install
git              2.44.0           main   2024-04-24 08:00:34 Global install
honeyview        5.51             extras 2024-04-24 08:44:32 Global install
hugo             0.125.3          main   2024-04-24 08:29:19 Global install
innounp          0.50             main   2024-04-24 08:29:21 Global install
lxmusic          2.7.0            dorado 2024-04-24 08:36:47 Global install
mpv              0.38.0           extras 2024-04-24 08:37:45 Global install
neofetch         7.1.0            main   2024-04-24 08:34:48 Global install
notepadplusplus  8.6.5            extras 2024-04-24 08:27:30 Global install
obsidian         1.5.12           extras 2024-04-24 13:48:55 Global install
piclist          2.8.3            lemon  2024-04-24 08:37:01 Global install
python           3.12.3           main   2024-04-24 10:58:22 Global install
qqnt             9.9.9.240422     dorado 2024-04-24 08:24:28 Global install
r                4.3.3            main   2024-04-24 08:29:47 Global install
rstudio          2023.12.1-402    extras 2024-04-24 08:35:22 Global install
rtools           4.3.5958.5975    main   2024-04-24 08:34:21 Global install
snipaste-beta    2.8.8-Beta       dorado 2024-04-24 08:37:06 Global install
sumatrapdf       3.5.2            extras 2024-04-24 08:37:10 Global install
vim              9.1.0366         main   2024-04-24 08:35:29 Global install
vimtutor         0.2018.07.25     main   2024-04-24 08:37:11 Global install
vscode           1.88.1           extras 2024-04-24 08:27:58 Global install
wechat-np        nightly-20240424 dorado 2024-04-24 08:26:00 Global install
windows-terminal 1.19.10821.0     extras 2024-04-24 08:28:46 Global install
yt-dlp           2024.04.09       main   2024-04-24 08:38:37 Global install
zotero           6.0.36           extras 2024-04-24 09:27:45 Global install
```

总之感觉是挺舒服的~

![](https://images.yuanj.top/202404251307232.png)

![](https://images.yuanj.top/202404251308817.png)